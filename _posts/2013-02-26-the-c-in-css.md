---
layout: post
title: the c in css
---

> Moved from onmylemon.co.uk

CSS is a very powerful tool for web designers and developers, as with all great tools you need to know how to use it effectively to get things right. This article covers some fundamentals about the Cascading part of CSS to give you a better handle on how your CSS affects the HTML content of sites you work on.

# Cascade based on style location

So, what does it mean by Cascade? The styles you set using CSS can come from several locations, from an external file, from the <head> portion of your page or applied directly to an element of your page. The web browser treats each with a different priority and will allow styles set in the <head> tag to override anything set in an external CSS file.

The order goes like this:

* External CSS file
* <head> tag
* inline styles (styles applied directly to an element)
* Styles that have the !important attribute set

The !important flag will override any styles set using the other methods, it should be avoided generally as it can cause confusion when it comes to working out what bits of style affect what.

# Cascade based on file position

The other part of the picture is where in your CSS file the styles are declared, take this for instance:

```css
p { color: #fff; }
p { color: #03f; }
```

Here we have the colour of P elements on a page set twice, due to the ‘read from the top down’ approach that browsers take to interpreting markup, the second declaration will be applied.

# Conclusion

This basic concept of how CSS works will help you in understanding where you should apply styles and how to manage their priority.
