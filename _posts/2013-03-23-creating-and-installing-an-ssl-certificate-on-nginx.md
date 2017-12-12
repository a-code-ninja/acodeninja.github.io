---
layout: post
title: creating and installing an ssl certificate on nginx
---

> Moved from onmylemon.co.uk

Creation and management of SSL certificates can be a complete pain at the best of times. This guide will take you through the basics of generating the necessary certificate requests and installing the certificate on your Nginx server.

# Stages of this guide

* Generate the Certificate Signing Request (CSR)
* Submit the signing request
* Put together your certificate chain
* Install the certificate
* Generating the CSR

To generate a CSR you will need to run a few commands on the server you intend to install your final certificate. The first command is used to generate a private key and a CSR.

```bash
$ openssl req -out ssl.csr -new -newkey rsa:2048 -nodes -keyout ssl.key
```

* req This indicates that we are doing a certificate signing request
* out ssl.csr This tells openssl to output the CSR to the file ssl.csr
* new This indicates we are doing a new CSR
* newkey rsa:2048 This indicates a new key is being generated and that it should be a 2048bit RSA key
* nodes Tells Openssl to not encrypt the private key
* keyout ssl.key This tells Openssl to output the key to the file ssl.key


We use the openssl program to generate the required files. Openssl is usually installed on Linux systems but you may need to use your package management system to install it. This page will explain in detail what you can do with Openssl https://www.openssl.org/docs/apps/openssl.html you do not need to use sudo or a root shell to run this command. When you run this command you will see the private key being generated before being asked a number of questions relating to the certificate you wish to generate.

* Country Name (2 letter code) [AU]: This requires the 2 letter coutry code for the business or server location, there is a list of the country codes here
* State or Province Name (full name) [Some-State]: This requires the state or county name, in the UK for instance I would enter “South Yorkshire”
* Locality Name (eg, city) []: This just needs the name of the city or area, in my case “Sheffield”
* Organization Name (eg, company) [Internet Widgits Pty Ltd]: You should put your company or organisation name here, for this site it would be OnMyLemon
* Organizational Unit Name (eg, section) []: Usually you do not need to put anything in here unless the certificate is being generated for a specific reason, you might put “Marketing” if this was for a specific site used to market you product
* Common Name (e.g. server FQDN or YOUR name) []: Here we need to put the domain name for the website, minus the www. so in my case onmylemon.co.uk
* Email Address []: This would be the email address of the person or group responsible for the certificate so maybe hostmaster@onmylemon.co.uk
* A challenge password []: This password is a means of protecting the signing of the certificate and ensuring that only the authorised certificate authority is allowed to sign it
* An optional company name []: This can be left blank by inputting a .
* And you’re done! You will now have a directory containing the CSR and the private key:

```bash
$ ls
dotdeb.gpg  nginx_signing.key  projects  ssl.csr  ssl.key
```

# Submit the signing request

So now we have a CSR file we can get it signed by a certificate authority (CA), you may already of bought an SSL certificate but there are several available. Thwate are a very widely recognised CA and the one I use in most cases. You may think they are expensive but check out Namecheap who can set you up with a Thwate certificate for around $30.

Submitting your CSR is usually done via a link emailed to you by the CA. This will involve copying and pasting the content of the CSR into a web form and submitting it. To verify the request you would expect an email sent to the email address we entered earlier asking to approve the certificate.

The time this process takes depends on the identity verification process needed for the certificate. This may also include paper work and submitting incorporation documents if you are generating the certificate for a company.

Once you have the certificate you should save this in the same directory as the other files, I use the file name ssl.crt. You should now have three files in your directory:

* ssl.key – Your private key
* ssl.csr – Certificate Signing Request (CSR)
* ssl.crt – Your signed SSL certificate

# Putting together your certificate chain

The best way to explain a certificate chain is a list of different SSL certificates that establish a chain of trust. It says “my certificate is trusted by this certificate and that certificate is trusted by this one”.

To put together your certificate chain you first need the certificate that was given to you following  the submission of your CSR to the CA. You then need the CA bundle, you can usually find this on your CA’s website here is Thwate’s bundle. I will be downloading the “All other web server’s” files to get things set up. I end up with three files, the primary and secondary (sometimes there is a tertiary) certificates as well as my certificate.

We need to put all these certificates together in one file in the right order, in my case this is:

* My Certificate File
* The Primary Certificate File
* The Secondary Certificate File

Save this as “ssl.cert.bundle” and you will end up with a file that looks something like this:

```
-----BEGIN CERTIFICATE-----
MIIE6DCCA9CgAwIBAgIQXIZJNM2/namfZlUCzHnqGDANBgkqhkiG9w0BAQUFADBe
XiyU0ZHTS2XkYxIf
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIERTCCA66gAwIBAgIQM2VQCHmtc+IwueAdDX+skTANBgkqhkiG9w0BAQUFADCB
95OBBaqStB+3msAHF/XLxrRMDtdW3HEgdDjWdMbWj2uvi42gbCkLYeA=
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIEjzCCA3egAwIBAgIQdhASihe2grs6H50amjXAkjANBgkqhkiG9w0BAQUFADCB
bxQe3FL+vN8MvSk/dvsRX2hoFQ==
-----END CERTIFICATE-----
```

The text in between the BEGIN and END will be much longer your end I have cut them down for legibility.

# Installing the certificate

Now we have the required files we need to set up the Nginx server to use them properly. To do this you will need to place the files in a directory readable by the server, in my case I have the files for onmylemon stored like this:

* /var/www/onmylemon/
    * www/
    * certs/
    * public/

So in my case I have two files in the certs directory ssl.key and ssl.cert.bundle. We now need to tell Nginx where these are and that we want to use these to set up an encrypted site. I have written an article on setting up an SSL only Nginx site that has some code samples in it but for ease here is a basic Nginx configuration for SSL.

```
# HTTPS
server {

  # Set up the port listener
  listen 443;

  # Set the hostname to be served
  server_name SITE www.SITE;

  # Set up SSL
  ssl on;
  ssl_certificate /var/www/SITE/certs/ssl.cert.bundle;
  ssl_certificate_key /var/www/SITE/certs/ssl.key;
  ssl_session_timeout 5m;
  ssl_prefer_server_ciphers on;

  # Set up the root of public html folders
  root /var/www/SITE/www;
  index index.php index.htm index.html;

}
```

These two lines will tell Nginx to use the files we have created, obviously change the directories according to how you have things set up.

```
ssl_certificate /var/www/SITE/certs/ssl.cert.bundle;
ssl_certificate_key /var/www/SITE/certs/ssl.key;
```

Once all of this has been completed you will then need to restart the Nginx server for the changes to take effect. To do this you will need to run the following command:

Ubuntu: `sudo service nginx restart`

Debian: `sudo /etc/init.d/nginx restart`

# Conclusion

So you are all done and set up with an SSL certificate for Nginx, the process does take a while so you are forgiven if it became frustrating. This is the only method that consistently works for me and I use this over several servers at IPGeek. Please comment if you have any questions or feedback on this article.
