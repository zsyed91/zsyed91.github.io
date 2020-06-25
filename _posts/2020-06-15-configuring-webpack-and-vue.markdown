---
layout: post
title: "[Rails 6 Series] Configuring Vue and Webpack"
summary: Configuring Vue and Webpack for production use
toc: true
---

<h3 class="no_toc">
Posts in Series
</h3>

* [Rails 6 Series - Starting Rails 6 with Vue on AWS]({% post_url 2020-06-01-starting-rails-6-vue-on-aws %})
* [Rails 6 Series - Setting up AWS resources]({% post_url 2020-06-02-setting-up-aws %})
* [Rails 6 Series - Deploying to AWS]({% post_url 2020-06-10-deploying-to-aws %})
* Rails 6 Series - Configuring Vue and Webpack

# Overview

We have successfully deployed the basic Rails 6 sample application to AWS. All that remains now is to configure both Vue and Webpack for production use. In local development we can serve our packs through `bin/webpack-dev-server` but in production we need everything pre-compiled so that Nginx can serve the content in the packs directory.

In this post we will go over configuring webpack so that we can import Vue globally in each pack as required. We will configure Vue and some basic Vue plugins and also make it available in all packs as needed. Lastly we will go over how webpack differs from the traditional Rails Asset Pipeline.

## Webpack Configuration

In the root directory of our application we have the webpack configuration files by stage. You can find these under `config/webpack/*.js`. The main file we need to update is `environment.js`. This file imports webpack, vue, and any other top level library we need.

When we created our initial Rails structure, we told rails that we will be using webpack and vue via the `--webpack=vue` flag in the cli. By default this will create the following content in `environment.js`

{% gist f1aa077b35c187d113f7ecd1e41a9d4c default_environment.js %}

To properly configure Vue for production, lets make sure that Webpack is configured to load the right payload for it. Create a new file named `vue_config.js` within the `config/webpack` directory and copy the contents below into it.

{% gist f1aa077b35c187d113f7ecd1e41a9d4c vue_config.js %}

After that, update the `environment.js` file from earlier to load our custom vue config and override the default development payload for vue. This will configure it to load correctly in production.

{% gist f1aa077b35c187d113f7ecd1e41a9d4c environment.js %}

## Package.json

With webpack configured, we can sanity check that our packages are up to date and included. If any are missing or if you need additional packages, add them to your `package.json` file in the root directory of our application.

{% gist f1aa077b35c187d113f7ecd1e41a9d4c package.json %}

## Vue Configuration

The final bit is to load in Vue into our application pack found in `app/javascript/packs/application.js`. We will import both Vue and BootstrapVue. If you do not want to use BootstrapVue, feel free to import just Vue. Since Rails uses Turbolinks, there is also a vue adapter for it. We will be importing all 3. Only Vue is absolutely required.

{% gist f1aa077b35c187d113f7ecd1e41a9d4c application.js %}

## Migrating away from Asset Pipeline

Using webpack lets us migrate out of the Asset Pipeline for javascript files. This is a bit of a shift in thinking if you have been using Rails for a while. The key difference is that you can write your javascript files within `app/javascript/` instead of `app/assets`. The way to model this approach is that you build your directory structure within `app/javascript/*` and use require statements to cross load all your local dependencies. Everything here will be compiled and minified by `webpack` during application build.

Intead of using `javascript_include_tag`, we use `javascript_pack_tags` to load files inside of the `app/javascript/packs` folder. Each pack can be considered a mini application bundle. You can generate as many as you need or bundle it all into 1 pack.

## Conclusion

At this point everything has been setup for both local development and pushing to production. Webpack will load the correct version of Vue and Vue has been configured to pull in both Turbolinks and BootstrapVue. With this, we can start building out our application and creating as many packs as we need. Webpack will handle the compilation, minification, and tree-shaking for us!

To learn more about Webpack take a look at the [concepts page](https://webpack.js.org/concepts/) at the official docs. This was sorely missing in the Rails Guides and helped me understand how webpack works when learning how to use webpack **with** rails. 

#### Rails 6 Series

* [Rails 6 Series - Starting Rails 6 with Vue on AWS]({% post_url 2020-06-01-starting-rails-6-vue-on-aws %})
* [Rails 6 Series - Setting up AWS resources]({% post_url 2020-06-02-setting-up-aws %})
* [Rails 6 Series - Deploying to AWS]({% post_url 2020-06-10-deploying-to-aws %})
* Rails 6 Series - Configuring Vue and Webpack
