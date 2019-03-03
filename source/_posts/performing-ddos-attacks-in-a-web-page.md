---
title: PERFORMING DDOS ATTACKS IN A WEB PAGE
date: 2011-01-06 15:09:19
tags: ["Security", "Tech"]
---


Last month, I scanned Lava's security theme [blog](http://blog.andlabs.org/) and found there are many awesome articles talking about new web technology and web security. In [this post](http://blog.andlabs.org/2010/12/performing-ddos-attacks-with-html5.html), Lava brings forth a method about improving the performance of web-based DDoS technique, with HTML5 features: [Cross Origin Requests](http://www.w3.org/TR/cors/)(OCR) and [WebWorker](http://www.whatwg.org/specs/web-workers/current-work/).

Performing DDoS attacks in a web page is nothing new for us. The famous network stress testing application [LOIC](http://en.wikipedia.org/wiki/LOIC) also has a javascript version: jsLOIC (Yep, so many interesting DDoS activities around Wikileaks :) ). New web techniques,  such as HTML5 and CSS3 , make tremendous progress in web world. So what influences will it make on DDoS technique?

<!--more-->

## Test

For some reasons, Lava didn't release POC. I am very interested in it, so I wrote code and make up four test cases to test this technique. They are:

- perform 5000 GET requests by img object in a simple loop
- perform 5000 GET requests by COR object in a simple loop
- perform 5000 GET requests by COR object in a WebWorker (Maybe I can call it The Alternative to Thread  in Javascript)
- perform 5001 GET requests by COR object in a timer callback with 4ms delay (Minimum delay for setTimeout and setInterval in HTML5 spec)

And then I wrote a python script to collect the information from my Nginx's access log. Here is the result:

```
Summary [IMG LOOP]
During: 2010-12-26 00:04:08 - 2010-12-26 00:04:27
Cost: 19 sec
Packets: 5000
Rate: 263.000000 packets/sec

Summary [XHR LOOP]
During: 2010-12-26 00:05:50 - 2010-12-26 00:06:09
Cost: 19 sec
Packets: 5000
Rate: 263.000000 packets/sec

Summary [XHR WebWorker]
During: 2010-12-26 00:04:55 - 2010-12-26 00:05:16
Cost: 21 sec
Packets: 5000
Rate: 238.000000 packets/sec

Summary [XHR Timer]
During: 2010-12-26 00:31:08 - 2010-12-26 00:31:36
Cost: 28 sec
Packets: 5001
Rate: 178.000000 packets/sec
```

## Analysis

Obviously, the first three tests have very similar performance figures, because they all eat out the processor's time as possible.  Although using WebWorker may improve the performance in multi-processor machine theoretically, the bottleneck is not the speed the requests being created, and the new web techniques seems do little help about improving the DDoS performance.

In my test, I open 5k connections in one time. If I increase the number to 10k or even more, most of the connections will lose, for I cannot find enough records from web server's access log. I don't dive into the source of Webkit, but I guess there are some limits making the browser refuse to send large numbers of request in a short time.

The real reason that make the request faster is the performance of modern browsers. In my laptop, Chrome can do 5k requests in about 20sec. Say in other words, if we construct our DDOS code carefully (thanks for Lava's help), We can send 15k request per minute. That's a very large number.

In my view,  high performance JavaScript VM in modern browser and increasing rich Web Apps are the essential, making DDoS, XSS and other web-based attacks more dangerous. Browser acts as an OS today. The Web Apps, such as Gmail, Facebook and Twitter, run in a browser as processes/programs in an OS. People often keep their web pages open until they shutdown the machine. Extended length of each visit make it possible do something more for many attack techniques like DDosS and XSS Shell.

It's worth noting that all modern browsers support to write extensions in JavaScript. Though both Mozilla and Google have many safety measures to protect browser from malicious code, these measures can't bring it to perfection. If malicious code can be embedded in a browser extension, it will be more dangerous and powerful.

## Additional remark

'Referer' field in request header may leak the information of infectant web page. To avoid this is very simple, just create a new blank iframe and let the code run in it.
