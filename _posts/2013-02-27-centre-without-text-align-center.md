---
layout: post
title: centre without text align center
---

> Moved from onmylemon.co.uk

CSS is great for changing things around on a page but knowing the best way to accomplish something when you are just starting out  is difficult. So how do you centre block level elements, and what on earth are block level elements anyway? Well for a list of html elements that are block level check out the MDN site here: https://developer.mozilla.org/en-US/docs/HTML/Block-level_elements

So now we know what they are what is the best way to centre them on a page or within a parent element? Well you might think to try and use the text-align style, or you might not, I don’t know. But the way you should do it is like this:

HTML
```html
<body>
  <div class="centre">
  </div>
</body>
```

CSS
```css
.centre {
  width: 400px;
  margin: 0 auto;
}
```

We are using a `<div>` here as it is a very common block level element. Applying the class of centre to the `<div>` so that we have granular control over the centring of elements. You might even want to create a separate class to define the width of your element to offer even more control.

This technique will centre block level elements no matter what size the viewport and is recommended when you want to get something centred.

# Sidebar

If you want to centre inline elements (as listed on this page: https://developer.mozilla.org/en-US/docs/HTML/Inline_elements) then you can and should apply the style `text-align: center;` to the element.
