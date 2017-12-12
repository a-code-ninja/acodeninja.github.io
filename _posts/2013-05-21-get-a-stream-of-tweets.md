---
layout: post
title: get a stream of tweets
---

> Moved from onmylemon.co.uk

The first part of the course I have been taking on data science deals with analysis of Twitter data. This is aimed at discerning the overall sentiment of a Tweet or collection of tweets.

To get this going the first thing you need is a big collection of tweets in a format that is easy for a program to digest. Step in JSON, a text based output that represents data in a collection of arrays. Once you have this data set you can then start experimenting. Maybe try something simple first, like taking ten minutes worth of samples every hour and mapping the twitter activity for an average of how many tweets per country for that hour.

```python
# Twitter Stream
# From "Introduction to Data Science" A coursera program with lectures from Bill Howe
# You will need to edit the access and consumer credentials to make this application work
# Run as follows: python twitterstream.py > output.json

import oauth2 as oauth
import urllib2 as urllib

access_token_key = "<Enter your access token key here>"
access_token_secret = "<Enter your access token secret here>"

consumer_key = "<Enter consumer key>"
consumer_secret = "<Enter consumer secret>"

_debug = 0

oauth_token    = oauth.Token(key=access_token_key, secret=access_token_secret)
oauth_consumer = oauth.Consumer(key=consumer_key, secret=consumer_secret)

signature_method_hmac_sha1 = oauth.SignatureMethod_HMAC_SHA1()

http_method = "GET"


http_handler  = urllib.HTTPHandler(debuglevel=_debug)
https_handler = urllib.HTTPSHandler(debuglevel=_debug)

'''
Construct, sign, and open a twitter request
using the hard-coded credentials above.
'''
def twitterreq(url, method, parameters):
  req = oauth.Request.from_consumer_and_token(oauth_consumer,
                                             token=oauth_token,
                                             http_method=http_method,
                                             http_url=url,
                                             parameters=parameters)

  req.sign_request(signature_method_hmac_sha1, oauth_consumer, oauth_token)

  headers = req.to_header()

  if http_method == "POST":
    encoded_post_data = req.to_postdata()
  else:
    encoded_post_data = None
    url = req.to_url()

  opener = urllib.OpenerDirector()
  opener.add_handler(http_handler)
  opener.add_handler(https_handler)

  response = opener.open(url, encoded_post_data)

  return response

def fetchsamples():
  url = "https://stream.twitter.com/1/statuses/sample.json"
  parameters = []
  response = twitterreq(url, "GET", parameters)
  for line in response:
    print line.strip()

if __name__ == '__main__':
  fetchsamples()
```

I’ll be putting up one or two examples of what you can do with this data in the coming days and weeks.
