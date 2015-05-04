---
layout: post
title: Simple lightbox using CSS
summary: Simply lightbox with SASS and making it a Backbone component
---


Sometimes I want to have a simple popup without having to bust out the full JQuery UI dialog widget. Using a little bit of SASS and HTML, we can create a simple popup/lightbox widget and convert it into a Backbone component so our code is DRY.

Here is the link to the [source](https://github.com/zsyed91/lightbox)


## The HTML

Here is what the skeleton of our lightbox will be:

{% highlight html %}

<div id="lightbox">
	<div class="content">
		<!-- Your custom content styled however you like -->
	</div>
</div>

{% endhighlight %}

That's it for the html.


## The SASS

{% highlight sass %}

// Provide a transparent background without using a png
// Feel free to swap this mixin with a png if you need to support old browsers
@mixin transparent-background($red, $green, $blue, $alpha) {
	background-color: rgb($red, $green, $blue);
	background-color: rgba($red, $green, $blue, $alpha);
}


// Vertical align the popup in the browser window
@mixin vertical-align($offset) {
	position: relative;
	top: $offset;
	-webkit-transform: translateY(-#{$offset});
	-ms-transform translateY(-#{$offset});
	transform: translateY(-#{$offset});
}

#lightbox {
	position: fixed;
	top: 0; left: 0;
	z-index: 100;

	// So our popup is center horizontally
	text-align: center;

	/* darken overlay */
	width: 100%;
	height: 100%;

	// A transparent black, tune this to your needs
	@include transparent-background(0, 0, 0, .5); 
	/* end overlay */

	// Your actual content and the popup itself
	.content {
		// Tune this to what looks good, having it dead center for my needs was odd
		@include vertical-align(40%); 

		// Your custom styling goes here (or in another stylesheet)
	}
}

{% endhighlight %}


Basically the `#lightbox` wrapper will contain our overlay, and `.content` is our popup/lightbox that we can style however we want.

## The Backbone component

In order to trigger the popup, I needed to create a `backbone` component that is shared across views.

Here is my `lightbox.js`:

{% highlight javascript %}

define(["Backbone"],
function(Backbone){
	
	var Lightbox = Backbone.View.extend({
		// Replace this with your templating system
		template: CustomTemplateLoader("lightbox"), 

		initialize: function(options) {

			// Where to append the lightbox, default to body
			this.selector = options.selector || "body";

			// The rendered html that you want in your popup
			this.content = options.content;

		},

		events: {
			"click .close": "remove" // Delete this view when pressing close
		},

		render: function(){

			// Only one popup, remove existing one if there is one
			$("#lightbox").remove();

			// Feel free to swap out what goes in this.content
			this.$el.html(this.template(this.content));

			$(this.selector).append(this.el);
		}

	});

	return Lightbox;

});

{% endhighlight %}


## Calling our lightbox

Now all we have to do is tie it together to some sort of view or event so we can render the lightbox.

{% highlight javascript %}

// In our view

define(function(require){

	var Backbone = require("backbone");
	var Lightbox = require("path/to/lightbox");

	// We want to render this email form within the lightbox
	var EmailFormView = require("content/within/lightbox");
	
	var ContactView = Backbone.View.extend({

		template: "YourTemplate.handlebars",

		initialize: function(options) {
			// init logic here
		},

		events: {
			"click .email-form": "showForm"
		},

		render: function() {
			// render logic

			this.selElement($(this.template()));
			return this;
		},

		showForm: function(event) {
			this.emailForm = new EmailForm();

			this.lightbox = new Lightbox({
				content: this.emailForm.render().el
			});

			// Calling render will show the popup
			this.lightbox.render();
		}

	});


	return ContactView;

});

{% endhighlight %}

## Lightbox template (Handlebars)

Here is the lightbox html template in `Handlebars`.

{% highlight html %}

<div id="lightbox">
	<div class="content">
		<!-- Bootstrap style close  -->
		<button type="button" class="close" aria-label="Close">
			<span area-hidden="true">&times;</span>
		</button>

		{ { content } } <!-- ignore the spaces between each {} -->
	</div>
</div>

{% endhighlight %}


## Source

Github source code: [lightbox source](https://github.com/zsyed91/lightbox)

