---
title: 快速素数判定
date: 2008-07-22 15:09:19
tags: ["Math", "数学", "Coding"]
---

在 SICP 1,3 节的时候有提到判断素数的方法。
朴素的素数判定，地球的同学都知道的，如果数 n 除 1 以外的最小因数是他自己，那么n就是素数。

```scheme
(define (remainder num divisor)
    (- num (* divisor (round (/ num divisor)))))

;return the smallest divisor of n
(define (smallest-divisor n)
    ;find a divisor(for 2 to n) of n
    (define (find-divisor n test-divisor)
        (cond ((< n (sqrt test-divisor)) n)
            ((= (remainder n test-divisor) 0) test-divisor)
            (else (find-divisor n (+ test-divisor 1)))))

    (find-divisor n 2))

;check n is prime or not
(define (prime? n)
    (= n (smallest-divisor n )))
```

但这个方法很慢，它的阶是O(n)。

<!--more-->

下面 SICP 给出了费马小定理：

> 如果一个数 n 是一个素数，而对而对任意 a<n，有 a^n≡a(mod n)

可惜哈，它的逆定理不成立，不过不代表不可用。我们可以挑选小于 n 的数 a，做上文所述的测试。如果满足 a^n≡a(mod n)，那么 n 很可能是一个素数。而挑选越多的 a 对 n 进行测试，n 是素数的可能性也越大。所以这是一个人品算法，只要测试的数量足够，那么 n 不是素数的概率就会变得很小。

于是我们有了 fermat 测试的程序。不用担心求 a^n 的效率问题，因为比较好解决：

试看：

```
a^n = (a^(n/2))^2; 当n为偶数
a^n = a* a^(n-1); 当n为奇数
```

总是成立的，所以几乎每次作乘法的时候，n 就增加了一倍。放到数轴看一看，其实这是二分法。所以时间复杂度得到降低。所以大概知道为什么编程语言提供了求 n 次幂的函数 `pow` 总是比自己写的 `a*a*a…` 快了。

但是不应该直接使用语言自带的 `pow` 函数。原因是我们的目标不是 a 的 n 次幂，而是想知道 a 的 n 次幂对 m 取模是否等于 a 对 m 取模。简化一下，即只需判断这个表达式是否成立：`a^n mod m = a`。如果直接表达成 `pow(a, n) % m == a` 计算机会郁闷，因为 `pow(a, n)` 的值会很大。所以应该采用类似的技术：

```
a^n mod m = ((a^(n / 2))^2) mod m;  当n为偶数
a^n mod m = (a* a^(n-1)) mod m;     当n为奇数
```

这样，每次计算得到的值都不比 m 大了，避免过大的数参与计算。于是很容易有了以下程序

```scheme
(define (remainder num divisor)
    (- num (* divisor (round (/ num divisor)))))

(define (expmod base exps m)
    (define (square n) (* n n))
    (cond ((= exps 0) 1)
        ((even? exps)
            (remainder (square (expmod base (/ exps 2) m))
                m))
        (else
            (remainder (* base (expmod base (- exps 1) m))
                m))))

;fermat test, check n. if (a^n) mod n == a then in all probability n is a prime
(define (fermat-test n)
    (define (try-it a)
        (= a (expmod a n n)))
        ;(= a (remainder (expt a n) n)))
    (try-it (+ 1 (random (- n 1)))))

;get prime by fermat-test. test n is prime or not, try times time
(define (fermat-prime? n times)
    (cond ((= 0 times) #t)
        ((fermat-test n) (fermat-prime? n (- times 1)))
        (else #f)))
```

但是，总有一些不完美。

存在一种数叫 Carmichael 数，能骗过费马测试。在 2 到 10000 这个区间内有这么几个：

> 561，1105，1729，2465，2821，6601，8911

这些钉子户是合数，但是不管你用什么 a 去测试，他们都可以在费马测试中冒充素数。他们数量也不少，而分布广，在100 000 000 以内一共有 255 个 Carmichael 数。所以，如果不愿意去记录 Carmichael 数表的话，可以加强我们的素数测试方法。

还好，在练习 1.28中，Miller 和 Rabin 给我们带来了 Miller-Rabin 测试法。该测试法基于 fermat 小定理的一个变形：

> 如果 n 是素数，对任何 a < n，a^(n-1) ≡ 1(mod n)

要使用 MR 测试法呢，就需要在执行上文 Fermat 测试的平方步骤时检查是否遇到了一个 “1 mod n的非平凡平方根”，如果存在一个 s = a^2;  而且 s <> 1 且 s <> n-1，而且 s^2 mod m = 1，那么这个数n就不是素数了。

> [Matrix67](http://www.matrix67.com/blog/archives/234)：Miller-Rabin 素性测试同样是不确定算法，我们把可以通过以a为底的 Miller-Rabin 测试的合数称作以a为底的强伪素数(strong pseudoprime)。第一个以 2 为底的强伪素数为 2047。第一个以 2 和 3 为底的强伪素数则大到 1 373 653。

所以当你测试量足够，那么该方法可以在工业应用中保证安全，下面是我写的1.28题答案

```scheme
(define (remainder num divisor)
    (- num (* divisor (round (/ num divisor)))))
    
(define (mr-expmod base exps m)
    (define (square x) (* x x))
    (define (check-square-root x n)
        (define (check r)
            (if (and (not (= x 1)) (not (= x (- n 1))) (= r 1)) 0 r))
        (check (remainder (square x) n)))

    (cond ((= exps 0) 1)
        ((even? exps)
            (check-square-root(mr-expmod base (/ exps 2) m) m))
        (else
            (remainder (* base (mr-expmod base (- exps 1) m))
                m))))

;Miller-Rabin test, check n. if (a^(n-1)) mod n == 1 then in all probability n is a prime
(define (mr-test n)
    (define (try-it a)
        (= 1 (mr-expmod a (- n 1) n)))
    (try-it (+ 1 (random (- n 1)))))

;get prime by Miller-Rabin-test. test n is prime or not, try times time
(define (mr-prime? n times)
    (cond ((= 0 times) #t)
        ((mr-test n) (mr-prime? n (- times 1)))
        (else #f)))

```

我构造了一些数据，分别求[1000, 1100], [10000, 10100], [100000, 100100], [1000000, 1000100]这些区间的素数，看看它们这些方法的表现如何：

```
    常规方法           常规方法的因数2优化版本       Miller-Rabin测试法
    [1000, 1100]           1 ms                1 ms                     1 ms
    [10000, 10100]         13 ms               8 ms                     1 ms
    [100000, 100100]       142 ms              78 ms                    1 ms
    [1000000, 1000100]     1337 ms             830 ms                   1 ms
```

这是在我的 intel Core 1 1.83GHz 的机器上跑的结果（Ubuntu 8.04 + DrScheme）。

我还另写了一个 C 语言的版本，运行结果如下：

区间[2,  10000]

```
    $ ./prime
    Normal:  return 0 cost 10ms
    Fermat: return 0 cost 24ms
    Miller-Rabin: return 0 cost 28ms
```

区间[200000, 1000000]:

```
    $ ./prime
    Normal: return 0 cost 3088ms
    Fermat: return 0 cost 824ms
    Miller-Rabin: return 0 cost 959ms
```

采用 gprof 分析的结果基本与预期一致，部分分析报告如下：

```
    Each sample counts as 0.01 seconds.
    %   cumulative   self              self     total
    time   seconds   seconds    calls  us/call  us/call  name
    63.55      2.58     2.58   800000     3.23     3.23  is_prime(int)
    15.76      3.22     0.64   800005     0.80     0.84  fermat_expmod(long, long, long)
    10.10      3.63     0.41   800012     0.51     0.88  mr_expmod(long, long, long)
    7.27      3.92     0.29 14813786     0.02     0.02  chk_unusual_square_root(long, long)
    1.23      3.98     0.05                             main
    0.86      4.01     0.04   800000     0.04     0.93  mr_test(long, long)
    0.74      4.04     0.03 14813658     0.00     0.00  square(long)
    0.25      4.05     0.01   800000     0.01     0.94  mr_is_prime(int)
    0.25      4.06     0.01   800000     0.01     0.85  fermat_is_prime(int)
    0.00      4.06     0.00   800000     0.00     0.84  fermat_test(long, long)
```

当 n 比较小的时候效果不好的原因我猜是 C 语言在处理普通递归时花销比较大。而当数据变得庞大时，这种递归上的花销的影响就相对不大了。如果有同学把上面的算法改成迭代形式（我不会改:P）或者是尾递归形式（gcc可以把尾递归形式优化成迭代形式）我想效果更加明显。
