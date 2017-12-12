---
layout: post
title: javascript, still a thing?
categories:
    - comtemplation
---

Javascript is a great, and expressive language. You can be as succinct or vague as you like, you can scratch the surface or leverage a really powerful event loop.

```javascript
alert('Annoying popup');
```

Lots of people just think of the annoying browser popup, but you can deeper than that.

```javascript
'use strict';

class Person {
    constructor(firstname, lastname) {
        this.firstname = firstname;
        this.lastname = lastname;
    }

    get name() {
        return this.firstname + ' ' + this.lastname;
    }
}
```

Classes can be defined, albeit through some syntax sugar over the top of javascript’s prototype inheritance.

```javascript
console.log(() => (new Person('thejs', 'ninja')).name))
```

Anonymous functions, thats right, javascript has lambda abstractions.

```javascript
const http = require('http');

const hostname = 'localhost';
const port = 8080;

const server = http.createServer((req, res) => {
  res.end('nodejs is serving you\n');
});

server.listen(port, hostname, () => {
  console.log('Server running at http://${hostname}:${port}/');
});
```

You want a web server? nodejs / iojs runtimes can do this out of the box.

```javascript
var five = require("johnny-five");
var board = new five.Board();

board.on("ready", () => {
  (new five.Led(13)).strobe();
});
```

Or maybe you want to interact with some real world electronics using a well established library with native bindings, or you can build your own.

> Embrace it, it's a thing.

In short, javascript is a powerful abstraction language. Embrace it, it’s a thing.
