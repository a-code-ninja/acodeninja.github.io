---
layout: post
title: information hiding
categories:
    - haiku
---

> object oriented <br>
> information hiding done. <br>
> closures: private methods


```javascript
class Person {
    constructor(name, dob) {
        var dob = dob;
        this.name = name;
        this.getBirthyear = () => new Date(dob).getFullYear();
    }
}

var person = new Person('thejsninja', '1985-12-04');

console.log(person.getBirthyear()); // 1985
```
