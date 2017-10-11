# Lesson 1: From Ping to HTTP

## Setting up

I am setting up a local VM on my Mac. Below are is the process I used.

### Installation
[Virtualbox]: https://www.virtualbox.org/
[Vagrant-Manager]: http://vagrantmanager.com/
[Vagrant]: http://www.vagrantup.com/

[Vagrant] uses [Virtualbox] to manage the virtual dependencies. You can directly download virtualbox and install or use Homebrew for it.

`$ brew cask install virtualbox`

Now install Vagrant either from the website or use Homebrew for installing it.

`$ brew cask install vagrant`

[Vagrant-Manager] helps you manage all your virtual machines in one place directly from the menu bar.

`$ brew cask install vagrant-manager`

Once you have VirtualBox and Vagrant installed, open a terminal and run the following commands:

```
mkdir networking
cd networking
vagrant init ubuntu/trusty64
vagrant up
```

When it is complete, you can log into the Linux instance with `vagrant ssh`.

### Installing networking tools

SSH into your Linux machine. Then take a moment to bring it up to date with any package updates: `sudo apt-get update && sudo apt-get upgrade`

You'll be using several network utility programs in this course. Some of them may already be installed, but just to make sure, let's install them all:

`sudo apt-get install netcat-openbsd tcpdump traceroute mtr`


## ping

All network traffic is sent in packets, each marked with the sender and recipient addresses. Ping command sends an n amount of packets to test if it can get from one address to the next and back.

`$ ping -c3 8.8.8.8`

`-c` followed by a number is the amount of times to ping.

## HTTP header info

Input: `$ printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80`

Output:
```
HTTP/1.1 301 TLS Redirect
Date: Wed, 11 Oct 2017 19:06:29 GMT
Server: Varnish
X-Varnish: 425686357
X-Cache: cp1055 int
X-Cache-Status: int
Set-Cookie: WMF-Last-Access=11-Oct-2017;Path=/;HttpOnly;secure;Expires=Sun, 12 Nov 2017 12:00:00 GMT
Set-Cookie: WMF-Last-Access-Global=11-Oct-2017;Path=/;Domain=.wikipedia.org;HttpOnly;secure;Expires=Sun, 12 Nov 2017 12:00:00 GMT
X-Client-IP: 108.5.233.34
Location: https://en.wikipedia.org/
Content-Length: 0
Connection: keep-alive
```

## printf

A shell command for printing formatted strings. Like `echo` but smarter. For example, it can turn `\n` to a line break.

## netcat (nc)

A computer networking utility for reading from and writing to network connections using TCP or UDP.

```
// start listening on a specific port for a connection
  $ nc -l 3456

// create a localhost to connect to port
  $ nc localhost 3456

// ^D to tear down the connection (disconnect)

// saying hello to google's gmail services
  $ nc gmail-smtp-in.l.google.com 25
  220 mx.google.com ESMTP h135si6533063qka.140 - gsmtp
```

## Port Numbers

The port range that a normal (non-root) user can listen on is 1024 through 65535.

But if you use root access (including `sudo`) then you can listen on ports down to 1.

#### Common Port Numbers
- 21: File Transfer Protocol (FTP)
- 22: Secure Shell (SSH)
- 23: Telnet remote login service
- 25: Simple Mail Transfer Protocol (SMTP)
- 53: Domain Name System (DNS) service
- 80: Hypertext Transfer Protocol (HTTP) used in the World Wide Web
- 110: Post Office Protocol (POP3)
- 119: Network News Transfer Protocol (NNTP)
- 123: Network Time Protocol (NTP)
- 143: Internet Message Access Protocol (IMAP)
- 161: Simple Network Management Protocol (SNMP)
- 194: Internet Relay Chat (IRC)
- 443: HTTP Secure (HTTPS)

## List open files (lsof)

 It lists information about any files that are open, by processes currently running on the system.

`$ sudo lsof -i` displays a list of open network connections.
