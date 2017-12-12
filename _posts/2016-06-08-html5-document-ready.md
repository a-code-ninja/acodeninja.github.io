---
layout: post

title: html5 document ready
categories:
    - haiku
---

> DOMContentLoaded,<br>
> document ready or not?<br>
> jQuery: overhead.

```javascript
document.addEventListener("DOMContentLoaded", function(event) {
    console.log('document load time: ' + event.timeStamp);
});
```
