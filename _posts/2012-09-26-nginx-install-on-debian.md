---
layout: post
title: nginx install on debian
---

> Moved from onmylemon.co.uk

It took ages to try and find a guide that actually helps with installing Nginx on a Debian system. Most either involve compiliing from source or are incomplete to the point of leaving you with a useless system.

So there are a number of steps to take to get Nginx up and running on a Debian system and I will cover my way here.

# Step 1 – sources.list

First step is to get the sources for Nginx and php5-fpm into your apt source list. To do this you need to add the following lines to your `/etc/apt/sources.list` file.

```bash
# NGinX Sources
deb http://nginx.org/packages/debian/ squeeze nginx
deb-src http://nginx.org/packages/debian/ squeeze nginx

# DotDeb Sources - php-fpm
deb http://dotdeb.mirror.somersettechsolutions.co.uk/ stable all
deb-src http://dotdeb.mirror.somersettechsolutions.co.uk/ stable all
```

Once you have done this the first impulse is to run aptitude update, don’t, it will fail and complain at you that there are no valid keys for the Nginx or dotdeb sources.

# Step 2 – apt keys
So the next step is to set up the crypto keys that allow the new apt packages to be checked and trusted.

## Nginx
With Nginx you need to download their signing key, to do this run the following command:

```bash
wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
```

Drop the sudo if you are running as root (shouldn’t be but just in case). This will add the key to your apt keychain and allow for verification of the packages you download.

## php5-fpm
php-fpm is the same, run the following command to add the signing key for the dotdeb repository.

```bash
wget http://www.dotdeb.org/dotdeb.gpg
sudo apt-key add dotdeb.gpg
```

Once all this is done feel free to go ahead and run aptitude update

# Step 3 – aptitude install
So we have all the required apt repositorys set up, now its time to install a server stack. So what you do next depends on what you are intending to run on your server. I run Nginx primarily for WHMCS installations as it makes the most out of the VPS boxes my clients use to run it. For a WHMCS installation you need a few components:

* An SQL server
* PHP-Curl (With SSL)
* GD2 Image library
* PHP-IMAP

So for this I would go ahead and run:

```bash
aptitude install nginx mysql-server-5.5 mysql-client-5.5 php5-fpm php5-curl php5-gd php5-mysql php5-imap
```

This will set up nginx with php5-fpm adding in the required modules, for a stipped down set up that doesn’t include any extras try:

```bash
aptitude install nginx mysql-server-5.5 mysql-client-5.5 php5-fpm php5-mysql
```

You can even get rid of the mysql packages if you have no plans to run an sql server.

# Step 4 – Configuration
The last stage in sorting out your Nginx install is the configuration files.

# Conclusion
Nginx is a very efficient and fast web server that when coupled with php-fpm provides a great platform for high traffic web applications. With this guide it is now easy to install and get up and running with a basic configuration.
