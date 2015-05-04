---
layout: page
title: Lightbox
permalink: /projects/lightbox/
---

A simple lightbox written using `SASS`, `Handlebars`, and `Backbone`. It is wrapped as a Backbone component to allow for easy reuse on the front end and prevent code duplication and follow DRY principles. The backbone component is basically a backbone view that you can use within your code.


### Features

- Simple, minimum CSS written in SASS
- Content and design is 100% customizable
- Backbone component is a drop and run view instance
- Component has a default close event that will remove the pop up


### Tutorial on how to use it

I wrote up a post explaining how the lightbox works and also how to use the `lightbox.js` backbone view. You can read it [here](/2015/05/01/simple-css-lightbox/).


### Dependencies

There are a few dependencies on getting everything compiled and in working order.

- [SASS](http://sass-lang.com/)
- [Backbone](http://backbonejs.org/)
- [jQuery](https://jquery.com/)
- [Bootstrap](http://getbootstrap.com/) (if you want to use the default close)
- [Handlebars](http://handlebarsjs.com/) (if you want to use the template I wrote)



### Get it on Github

You can download the source via github: [lightbox](https://github.com/zsyed91/lightbox)

Please note, due to the way `Handlebars` loads and compiles templates you will need to swap out some code to add in your templating engine and your method of loading and compiling your templates.