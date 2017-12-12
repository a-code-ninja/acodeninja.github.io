---
layout: post
title: object.create, applying prototypes
categories:
    - haiku
---

> prototyping chaining, <br>
> object extension for all the <br>
> modern programmers

```javascript
var Ninja = function () {};

Ninja.prototype.throw = function (item) {
    console.log('Throwing ' + item);
};

var Shinobi = function () {};
Shinobi.prototype =  Object.create(Ninja.prototype);

var shinobi = new Shinobi;
shinobi.throw('knife'); // Throwing knife
```
