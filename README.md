jmp - ssh login automation
============

The jmp script uses pexpect to automate ssh login process.
It securely stores your password in a keychain. Where
OSX Keychain, Gnome Keyring and KDE Kwallet are supported.
You can configure different usernames and passwords for
hosts using glob patterns in jmp.conf file.

```
$ jmp
usage: jmp [-h] [-l USERNAME] [-C CONFIG] [-T TIMEOUT] [-P] [-N] [-n] [--pdebug] hostname
```
What does it actually do?
-------------------
jmp - is a simple ssh and expect wrapper script that will log you in to a 
server. It stores your passwords securely in the system keychain. 
It runs on Linux where GNOME keyring, KDE kwallet are supported and 
MacOSX with its default keychain. I'm using Linux so it's a bit better tested
on this platform but should work fine on OSX.


How can you contribute?
--------------------------
Just run it and let me know if it works or doesn't? Feel free to hack it, 
fork it, send me feature requests or any ideas you've got regarding 
this tool.


How can I tweak the config file?
--------------------------

The syntax of jmp.conf is easy. 

You can specify multiple user sections each with the username you use to login to 
servers e.g.

```
[john.doe]

[root]
```

For every user section you can specify multiple hostname to password tag mappings
e.g.

```
[root]
dfw* = jmp-dfw-root-pass
* = jmp-ldap-pass
```

The hostname entries can use glob patterns. For example:

```
* = jmp-ldap-pass
```
is a catch all entry..

The password tag lookup is based on the username and hostname provided at the 
command line and it is performed starting from top of the list. The first 
successful match will be used. 

The password tag e.g. 'jmp-ldap-pass' is simply the name of the password entry 
created in you keychain.

Some example config file:

```
[john.doe]
bigip.lhr1* = jmp-bigip-pass
* = jmp-ldap-pass

[operations]
* = jmp-operations-pass

[root]
dfw* = jmp-dfw-root-pass
*.iad1.* = jmp-iad1-root-pass

```

How can I get it running on my OSX - Snow Leopard?
=========

QUICK STEPS (for impatients):
------------------

```
sudo easy_install -Uv  pexptect
sudo easy_install -Uv  keyring
sudo easy_install -Uv  argparse
cd ~
git clone git@github.com:penggu/sshp.git
cd jmp
sudo cp jmp /usr/local/bin
sudo chmod +x /usr/local/bin/jmp
mkdir ~/.jmp
cp jmp.conf ~/.jmp/
vi ~/.jmp/jmp.conf 
jmp demo01.yourcompany.com
```

NORMAL STEPS:
------------

1.Install git if don't have it already installed:

http://code.google.com/p/git-osx-installer/

2.Clone the repository

You can skip this set if you pulled the files via github web interface:

git clone git@github.com:penggu/sshp.git

3.Copy the script to your favourite location e.g. /usr/local/bin

```
cd jmp
sudo cp jmp /usr/local/bin
sudo chmod +x /usr/local/bin/jmp
mkdir ~/.jmp
cp jmp.conf ~/.jmp/
```

The minimum would be to update the config with your LDAP username, e.g.

```
sed -i -e 's/your.user/peng.gu/g' ~/.jmp/jmp.conf
```

4.Install/upgrade python dependencies:

```
sudo easy_install -Uv  pexpect
sudo easy_install -Uv  keyring
sudo easy_install -Uv  argparse
```

5.Test it

On the first run (for every password tag specified in the config file) the 
script asks you to provide a password and stores it in the keychain. 
The next run should log youin without the password.

```
jmp demo01.yourcompany.com
```
You can check your keychain for the passwords tag entries that you 
configured in jmp.conf.
