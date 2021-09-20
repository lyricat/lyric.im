---
title: Password Management with Pass
date: 2021-09-21 13:25:46
tags: ["Linux", "Password Management"]
---

I have been using commercial password services for a while now, but I am sure that they are not a good choice. I want to replace them with an open source, cross-platform, and developer-friendly password manager.

After researching for days, I found [Pass](https://www.passwordstore.org/ "Pass, the standard unix password manager"), a password manager that satisfies all my needs. It is designed to follow [Unix philosophy](http://en.wikipedia.org/wiki/Unix_philosophy), which is to be easy to integrate with other tools.

The mechinism behind Pass is pretty simple:

- all password files are encrypted with [GPG](https://www.gnupg.org/ "GNU Privacy Guard") at `~/.password-stores`
- the `pass` command provides an interface to manage them

That's it.

## Preparing GPG keys

If you don't have a GPG key yet, generate one with the following command:

```bash
gpg --gen-key
```

or use following command to specify a key algorithm:

```bash
gpg --full-generate-key --expert
```

For more information, see [GnuPG's Arch Wiki](https://wiki.archlinux.org/title/GnuPG) and [GnuPG's Website](https://www.gnupg.org/).

> [!IMPORTANT]
> Please store your key pairs in a safe place. If you lost your key, you will lose access to your passwords.

## Setting up Pass

ArchLinux users can install Pass with the following command:

```bash
sudo pacman -S pass
```

And then initialize a default password store at `~/.password-stores`:

```bash
pass init "your_gpg_key_id@email.com"
```

This command will use the gpg key with the id `your_gpg_key_id@email.com` to encrypt and decrypt the password files.

### Basic usage

Generate a new password:

```bash
pass generate twitter/lyricwai
```

Edit a password file:

```bash
pass edit twitter/lyricwai
```

Copy a password to the clipboard:

```bash
pass -c twitter/lyricwai
```

### Password file format

The password files are simple plain-text files with no particular format indeed.

The only requirement is that the first line of the file is the password because the flag `-c` only copies the first line of the file.

For my personal opinion, I prefer the following format:

```yaml
MY_PASSWORD
---
user: MY_USERNAME
url: THE_URL_TO_LOGIN
# ... other information need to keep in the file
```

This format is also supported by some [3rd-party password managers](https://www.passwordstore.org/#other).

## Helper scripts

Pass only provides the minimum of features to manage passwords.

But sometimes, I need more features to make things convenient. For example, for each login, I want to be able to copy the username to the clipboard quickly. So I wrote some scripts to help me do that.

I am not familiar with shell, please modify them to your needs:

### pass-tail

The first one is `pass-tail`, which can be used to display the content of the each password entry file without displaying the password itself. It works like [pass-extension-tail](https://github.com/palortoff/pass-extension-tail).

```bash
#!/usr/bin/env zsh

pass "$@" | sed 1,1d
```

### pass-cp

In password store, the password is stored in the first line followed by data like the URL, username and other meta data in the following lines. A common password file would look like this:

```yaml
Hdii&^jd91
---
user: abc@123.com
url: https://login.abc.com
```

A common use case is to copy the first line, the password, using `pass -c <password file>`. But the meta data like username or url can not be copied.

`pass-cp` is a script to satisfy the requirement. It can be used to copy a specific meta field of the password entry file by using `pass-cp <password file> <field>`

```bash
#!/usr/bin/env zsh

field=$2

if [ -z "$2" ]; then
  field="user"
fi

echo -e "copy $field of $1"

pass $1 | sed 1,1d | grep "$field:" | awk '{gsub(/ /,""); print $2}' FS=':' | wl-copy -n

notify-send "copied '$field' to clipboard."
```

It also uses `notify-send` to send notification on the Linux desktop.

### pass-login

Another common use case is to open the login url in a web browser, and then fill the username and password. `pass-login` is the script to get this job done.

```bash
#!/usr/bin/env zsh

echo -e "open url of $1"

url=`pass $1 | sed 1,1d | grep "url:" | awk '{gsub(/url:/,""); print $0}' FS=':'`

xdg-open $url

pass-cp $1 user
```

`xdg-open` is a command to open a url in the default web browser. If you are using macOS, you may need to change it to `open`.

### Command completion

```bash
# set completion for pass util scripts
compdef pass-login=pass
compdef pass-cp=pass
compdef pass-tail=pass
```

## Other notes

### Migrate from other applications

There are a lot of [migration scripts](https://www.passwordstore.org/#migration) for other password managers that provided by the community.


### Buffer the private key

By default, you need to enter the password of your GPG key every time you use Pass. If you are tried for that, run GPG agent service to buffer the private key so you don't have to be entered on every single password retrieval.

Edit file `~/.gnupg/gpg-agent.conf` to enable GPG agent.

```conf
# after 300 seconds, the password must be entered again.
default-cache-ttl 300

# set the maximum lifetime of buffered private keys to 1 hour
max-cache-ttl 3600
```

After that, use the following command to restart the agent:

```bash
gpg-connect-agent reloadagent /bye
```

### OTP support

Pass supports OTP (One-Time Password) authentication by plugin ["pass-opt"](https://github.com/tadfisher/pass-otp).

Insert an otp url in your password file like this:

```yaml
MY_PASSWORD
---
user: MY_USERNAME
url: THE_URL_TO_LOGIN
otpauth://totp/your_otp_name?secret=your_otp_secret&issuer=your_otp_issuer
```

Copy the otp code:

```bash
pass otp -c twitter/lyricwai
```

### Git support

Pass has built-in support for Git. We can additonally initialize a git repository for your password store and add an url of a remote repository.

```bash
$ pass git init
$ pass git remote add origin GIT_URL
```

Pass will then automatically commit the changes of password stores each time you add, update or delete a password entry.

And then, you can push the changes to the remote repository.

```bash
pass git push
```

### Multiple password stores

You can use a flag `--path=folder` to specify a folder to store your password stores.

```bash
pass init --path=sub-folder gpg-id
```

or according to [this email thread](https://lists.zx2c4.com/pipermail/password-store/2016-February/002070.html), setting the environment variable `PASSWORD_STORE_DIR` to set different folder for each password store.

