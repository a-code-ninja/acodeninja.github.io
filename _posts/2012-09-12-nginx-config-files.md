---
layout: post
title: nginx config files
---

> Moved from onmylemon.co.uk

Ever since moving to Nginx I have had the self satisfied smile of someone who knows his web applications are going to snappy. Thats not just down to good coding, but down to the quality of the server software employed.

One problem that I came across with Nginx was implementing features found in the .htaccess files you would usually use to set per site and directory settings within PHP and Apache. No longer would these work out of the box, so a new tack was needed.

Nginx has a pretty good config file structure, easy to read, logical and above all nice to look at. A standard server without any PHP looks a little something like this:

How nice does that look? On Debian you just drop this into the /etc/nginx/conf.d directory (making sure to call it somthing.conf) and run sudo /etc/init.d/nginx restart. On Ubuntu it is even easier, just drop this into /etc/nginx/sites-available/onmylemon.co.uk, run ngxensite then sudo /etc/init.d/nginx restart.

```
server {
	root /var/www/onmylemon.co.uk/www;
	index index.html;

	access_log  /var/www/onmylemon.co.uk/access.log;
	error_log  /var/www/onmylemon.co.uk/error.log;

	server_name onmylemon.co.uk www.onmylemon.co.uk;
}
```

Try hammering it a little, I usually use loadimpact for this and you can run a few free tests every day against a URL of your choice.

Now you have this maybe lets try some PHP in there? You will need to get php5-fpm up and running but this isn’t too much of a challenge. A small alteration needs to be made to the configuration we used previously.

```
server {
	root /var/www/onmylemon.co.uk/www;
	index index.html;

	access_log  /var/www/onmylemon.co.uk/access.log;
	error_log  /var/www/onmylemon.co.uk/error.log;

	server_name onmylemon.co.uk www.onmylemon.co.uk;

	location ~ \.php$ {
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		include fastcgi_params;
	}
}
```

The first thing I noticed was fastcgi_pass 127.0.0.1:9000; at first it didn’t sink in, but then I realised this is one of Nginx more interesting features. This allows you to pass php execution off to other servers thus using the server with Nginx on it purely as a reverse proxy.

So this just covers some basic stuff with the configuration files, I’ll be putting up more information as I delve deeper.
