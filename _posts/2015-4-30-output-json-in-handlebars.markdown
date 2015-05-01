---
layout: post
title: How to quickly output JSON in handlebars
summary: How to quickly output a JSON object into your handlebars templates
---

Using Express, I find myself in need of having some bootstrapped data for the front end when the backend has done some processing. Here is a really easy way to output a json object into your handlebars template.


## The helper function

{% highlight javascript %}

var Handlebars.registerHelper('toJSON', function(object){
	return new Handlebars.SafeString(JSON.stringify(object));
});

{% endhighlight %}

## Global helper in express

Here is an easy way to add the above helper into your express project using `express-handlebars`.

{% highlight javascript %}

app.engine('handlebars', handlebars({
  defaultLayout: 'main', 
  helpers: {
    toJSON : function(object) {
      return JSON.stringify(object);
    }
  }
}));

{% endhighlight %}


## Handlebars template

If you use the helper function defined with `Handlebars.SafeString` add this in your template:

{% raw %}

	{{toJSON someJSON}}

{% endraw %}

And if you don't use the `Handlebars.SafeString` method you will need to prevent the auto-escaping that `{{ }}` will do.

{% raw %}

	{{{toJSON someJSON}}}

{% endraw %}