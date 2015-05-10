---
layout: page
title: Handlebars-Contrib
permalink: /projects/handlebars-contrib/
---

Underscore-contrib style library but for handlebars. Provide some common `handlebars` helper functions for use in templates. They are split out by feature area. 

### Features

- String helpers
- Math helpers



### Build Status

[![Build Status](https://travis-ci.org/zsyed91/handlebars-contrib.svg)](https://travis-ci.org/zsyed91/handlebars-contrib)

### About

Underscore contrib style handlebars helper functions. Adds helper functions to your handlebars templates as well as in your javascript.


### Source

This is an open source project hosted on github. The source is available [here](https://github.com/zsyed91/handlebars-contrib).


### String Helpers

#### Capitalize

Capitalize the first character of the first word in the string.

{% highlight javascript %}
var strings = require("handlebars.strings");

strings.capitalize("this is a sentence.");
// This is a sentence.
{% endhighlight %}

{% raw %}
    {{capitalize this.firstName}}
{% endraw %}

#### Capitalize All
{% highlight javascript %}
var strings = require("handlebars.strings");

strings.capitalize_all("john smith");
// John Smith
{% endhighlight %}

{% raw %}
    {{capitalize-all this.name}}
{% endraw %}

#### Normalize Spaces

{% highlight javascript %}
var strings = require("handlebars.strings");

strings.normalize_spaces("This   is a    sentence.");
// This is a sentence.
{% endhighlight %}

{% raw %}
    {{normalize-spaces this.summary}}
{% endraw %}

#### To URL Slug

{% highlight javascript %}
var strings = require("handlebars.strings");

strings.to_url_slug("Blog Post title!");
// blog-post-title
{% endhighlight %}

{% raw %}
    <a href="localhost/blog/{{to-url-slug this.postTitle}}">post</a>
{% endraw %}

#### Match

This helper is a conditional and fully supports the `else` clause.

{% raw %}
    <!-- commonNameRegex = /^John/, for example -->
    {{#match this.name this.commonNameRegex}}
        <p>What a common name!</p>
    {{else}}
        <p>What a unique name!</p>
    {{/match}}
{% endraw %}


### Math Helpers

While you can use these in javascript, they are really designed for use within the templates. The calculate method in javascript would be easier to do it directly.

#### Calculate

The supported operators are `[+, -, /, *, %]`

{% raw %}
    {{calculate this.costOfCookies '+' this.costOfMilk}}

    {{calculate this.totalPrice '*' this.taxRate}}
{% endraw %}

#### Compare

This helper is a conditional and fully supports the `else` clause. The supported operators are `[<, <=, >, >=, ==, !=, ===, !==]`.

{% raw %}
    {{#compare this.price '<=' this.budget}}
        <p>Affordable</p>
    {{else}}
        <p>Too expensive :(</p>
    {{/compare}}
{% endraw %}