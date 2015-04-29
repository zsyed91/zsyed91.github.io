---
layout: post
title: How to start a Node/Express Project
date: 2015-4-28
summary: Starting an Express.JS project from scratch
---


I have recently started playing around with Node and Express to see what all the fuss is about. It's actually very cool and easy to get a project started.

## Installing Node.js

Before we start an express project, we need to install Node. Installing node is extremely straight forward. Just go to [nodejs.org](https://nodejs.org/). Click on the big `Install` button and it will serve the binary installer appropriate for your platform. Go ahead and install it as you would any other application.

The installer will add both `node` and `npm` commands to your `PATH` so you should be able to run them from the terminal. To make sure everything is in working order lets open up a terminal and type in the following:

    $ node --version
    v0.12.0   <-- should output a version number 

If you got the above, then node is installed and added to the path correctly. Now lets make sure `npm` is working:

    $ npm -version
    2.5.1    <-- again, it should output a version number



## Installing Express

Express is a web framework for node, similar to how Sinatra is to Ruby. Installing it is really easy. Go back to the terminal and type in the following:

    $ npm install express --save

It should output some text and say it was successful. Let's break down this command:

    npm install  <-- tell npm we want to install a package
    express      <-- look for a package called express at npmjs.com
    --save       <-- make this global so we can call express from anywhere


## Creating an Express Project

Now go to where you want to have your project files sit and type this into the terminal:

    $ express name-of-your-project

The above command will create a directory with the name `name-of-your-project` and generate a skeleton project for you to get started. The directory structure is as follows:

    bin/
    	www
    public/
    	images/
    	javascripts/
    	stylesheets/
    routes/
    	index.js
    	users.js
    views/
    	error.jade
    	index.jade
    	layout.jade
    app.js
    package.json


#### Bin
The `bin` folder is where your binaries should go and is also where the project launcher sits.


#### Public
All requests to your assets under `public/` get redirected so in your html to include javascript you would call `javascripts/test.js` and the backend will know to pull it from the `public` directory.

#### Routes
The `routes` folder holds all of your controllers that map to a front end url. A basic express project has all of your routes defined through `app.js` which reads the `index.js` and `users.js` files by default for paths.


#### Views
The `views` directory holds the views for your html templates. The routes have a place for each path to map to a view in this folder. This folder can be moved and by default the html templates will be `Jade` templates. You can change this to another templating system like `Handlebars` if you wish. My personal preference is to use `Handlebars` and I will add a guide  on how to switch this. The views directory (and templating engine) are defined in `app.js`.

#### App.js

This is the main entry point of an express project. All requests are passed through this file. This file holds all of our configuration and set up for our web application.

#### Package.json

This is where we hold our npm dependencies for the backend like `jquery`, or `underscore` or any other library that you find yourself in need of. To install all of the dependencies here just open up a terminal and go to your project and type in this:

    $ npm install

It will then search `package.json` and attempt to find a matching package on npmjs.com and install the package locally under a folder called `node_modules`.


## Running your project

Type in the following in a terminal at the root of your project:

    $ npm start

It should successfully start running a web instance of your project that you can visit via [localhost:3000](localhost:3000). I like to use a tool called nodemon because it automatically restarts the webserver every time you change a `.js` file.

### Nodemon
To install `nodemon` run the following in a terminal:

    $ npm install -g nodemon

This will install it globally via the `-g` flag. And now replace `npm start` with (at the root of your project):

    $ nodemon bin/www

## Conclusion

That's it! We now have a test project all set up and we can begin working on getting our pages running and hooking things up to a database and api.
