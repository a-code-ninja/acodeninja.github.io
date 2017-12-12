---
layout: post
title: nginx ssl only configuration
---

> Moved from onmylemon.co.uk

Configuring Nginx with SSL can be a little bit of a nightmare to do properly. Here is a handy config file I use as a starting base for it.

```
#############################################
#
# NGINX CONFIGURATION FILE
#  SSL only set up with a redirect to point
#  http traffic at the https server.
#
#############################################

#HTTP
server {

  # Set up the port listener
  listen 80;
  listen [IPV6]:80 default ipv6only=on;

  # Set the hostname to be served
  server_name SITE www.SITE;

  # Set up access and error logs
  access_log /var/www/SITE/logs/nginx.access.log;
  error_log /var/www/SITE/logs/nginx.error.log;

  # Redirect all requests on http to https
  location / {
    rewrite ^ https://$server_name$request_uri? permanent;
  }

}

# HTTPS
server {

  # Set up the port listener
  listen 443;
  listen [IPv6]:443 default ipv6only=on;

  # Set the hostname to be served
  server_name SITE www.SITE;

  # Set up SSL
  ssl on;
  ssl_certificate /var/www/SITE/certs/ssl.crt;
  ssl_certificate_key /var/www/SITE/certs/ssl.key;
  ssl_session_timeout 5m;
  ssl_prefer_server_ciphers on;

  # Set up access and error logs
  access_log /var/www/SITE/logs/nginx.access.log;
  error_log /var/www/SITE/logs/nginx.error.log;

  # Set up the root of public html folders
  root /var/www/SITE/www;
  index index.php index.htm index.html;

  # Set up the php system
  location ~ .php$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME /var/www/SITE/www$fastcgi_script_name;
    include fastcgi_params;
  }

}
```

Looking through the config file you can see a number of parts, the two most important parts are as follows:

* `rewrite ^ https://$server_name$request_uri? permanent;` This allows any requests made to the http service to be redirected to the https
* `ssl on;` This tells the server to use SSL on port 443 to server https encrypted pages.

I hope that this short article helps get you up and running with SSL on Nginx.
