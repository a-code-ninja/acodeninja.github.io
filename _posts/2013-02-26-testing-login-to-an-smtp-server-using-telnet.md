---
layout: post
title: testing login to an smtp server using telnet
---

> Moved from onmylemon.co.uk

So if you are having problems sending emails there is always one sure fire way to establish if the problem is your connection or not. This can be done using the telnet application, a command line tool that is included in most operating systems.

To do this you will need to open the command prompt, on windows you can do this by pressing the windows key and tapping the R button once. The dialog that pops up just needs “cmd” input and the OK button pressing.

The commands should be entered as follows:

```
telnet smtp.someserver.com 25
```

This will show the following on screen:

```
Trying 212.159.8.107...
Connected to smtp.someserver.com.
Escape character is '^]'.
220 smtp01 smtp smtp.someserver.com
```

To which you should respond by typing:

```
ehlo server
```

The ehlo command produces a list of capabilities and starts then exchange of information between yourself and the SMTP server. Once this has been done you need to tell the server you intend to give it a username and password. To tell the server this you need to enter the following:

```
auth login
```

The server will then respond with

```
334 VXNlcm5hbWU6
```

This apparently random set of characters is actually a string encoded in base64 this actually says “username”. So as it is asking the question in base64 we have to reply in the same encoding, to do this you will need to convert the username into base64. There is a handy website for doing this here: http://www.base64encode.org/

Once you enter the username the server will ask you for the password, again in base64. Once you enter the encoded password you will get confirmation of success or failure.

You can then go on to test sending an email but that is outside the scope of this article.
