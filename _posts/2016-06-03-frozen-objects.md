---
layout: post
title: frozen objects
categories:
    - haiku
---

> objects are frozen,<br>
> never to be extended<br>
> or altered again

```javascript
class Ninja {
    constructor() {
        this.dead = false;
        Object.freeze(this);
    }
}

var shinobi = new Ninja();

shinobi.dead = true;

console.log(shinobi); // Ninja { dead: false }
```
