---
layout: post
title: Switch template engine to Handlebars
summary: How to switch the default template engine from Jade to Handlebars in Express
---


I am a huge fan of using `Handlebars` and think its nicer to work with it on the backend with an express project. Switching it out from `Jade` to `Handlebars` is really simple.

## Install Express-Handlebars

The npm handlebars package is `express-handlebars`. Add the following to your `package.json` dependencies list:

    "express-handlebars": "*"

 `"*"` will pull the latest version, feel free to substitute this with a specific version number if you wish. Now just change `app.js` to use the handlebars engine instead of Jade.


## Switch template engines

{% highlight javascript %}

var handlebars = require("express-handlebars");


var app = express();

// Put this just past the above line
app.set('views', path.join(__dirname, 'public/javascripts/templates'));

app.engine('handlebars', handlebars({
  defaultLayout: 'main', 
  layoutsDir: path.join(__dirname,'views/layouts'),
  partialsDir: path.join(__dirname, 'views/partials')
}));
app.set('view engine', 'handlebars');

{% endhighlight %}


Thats all! Now under `{project_root}/views` you can put your handlebars templates. By default the file extension is `.handlebars`.