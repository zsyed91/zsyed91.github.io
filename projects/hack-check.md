---
layout: page
title: Hack-Check
permalink: /projects/hack-check/
---

This is a Firefox OS Application. It can be either hosted or packaged. The source is available at [GitHub](https://github.com/zsyed91/hack-check).
The purpose of this app to show users if any of their accounts have been compromised by any data breaches. If their email address shows up
on any data breach they know what sites they need to change passwords for.

### Demo

The project is hosted at [http://hackcheck.zshawnsyed.com](http://hackcheck.zshawnsyed.com/).

### Features

- Get a list of websites that compromised your email address
- Find out what else was stolen aside from your email address from each service
- Find out when it was stolen
- A description of the hack along with some details about it

### How to install

#### Hosted

Add the following manifest path to add the application: 

    http://hackcheck.zshawnsyed.com/manifest.webapp


#### Marketplace

The listing on the [Firefox Marketplace](https://marketplace.firefox.com/app/hack-check).


### Support 

If you have any questions or need assistance please send me an [email](mailto:hackcheck@zshawnsyed.com)!


### How to build

If you want to build it from source, all you need to run `ruby build.rb` and that will compile all the needed assets.


### Data Source

All data is pulled from haveibeenpwned.com's public api.