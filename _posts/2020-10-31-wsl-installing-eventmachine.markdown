---
layout: post
title: Installing the eventmachine gem in WSL
summary: How to install the rubygem eventmachine on WSL
toc: true
---

I ran into this issue a few times and kept forgetting the fix for it which is why I am
writing a quick post on it. Generally when adding ruby gems, we add them to our `Gemfile` and just throw `bundle install` at it to get everything configured. However I kept running into a strange issue when attempting to get event machine installed for a gem I was writing myself.

The installation steps are actually quite simple if you run into this error.

## The error

When trying to install eventmachine (1.2.7) on [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10), I kept getting this stack error:

```
user@DESKTOP:/mnt/f/code/project$ bundle install
Fetching gem metadata from https://rubygems.org/......
Using rake 10.5.0
Fetching eventmachine 1.2.7
Installing eventmachine 1.2.7 with native extensions
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    current directory: /home/user/.rvm/gems/ruby-2.7.1/gems/eventmachine-1.2.7/ext
/home/user/.rvm/rubies/ruby-2.7.1/bin/ruby -I /home/user/.rvm/rubies/ruby-2.7.1/lib/ruby/2.7.0 -r
./siteconf20201031-507-no2sha.rb extconf.rb --with-ssl-dir\=c:/OpenSS
-----
Cannot find OpenSSL include path c/include:/OpenSS/include
-----
*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.

Provided configuration options:
        --with-opt-dir
        --without-opt-dir
        --with-opt-include
        --without-opt-include=${opt-dir}/include
        --with-opt-lib
        --without-opt-lib=${opt-dir}/lib
        --with-make-prog
        --without-make-prog
        --srcdir=.
        --curdir
        --ruby=/home/user/.rvm/rubies/ruby-2.7.1/bin/$(RUBY_BASE_NAME)
        --with-ssl-dir
        --with-ssl-include
        --without-ssl-include=${ssl-dir}/include
        --with-ssl-lib
        --without-ssl-lib=${ssl-dir}/lib

To see why this extension failed to compile, please check the mkmf.log which can be found here:

  /home/user/.rvm/gems/ruby-2.7.1/extensions/x86_64-linux/2.7.0/eventmachine-1.2.7/mkmf.log

extconf failed, exit code 1

Gem files will remain installed in /home/user/.rvm/gems/ruby-2.7.1/gems/eventmachine-1.2.7 for inspection.
Results logged to /home/user/.rvm/gems/ruby-2.7.1/extensions/x86_64-linux/2.7.0/eventmachine-1.2.7/gem_make.out

An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.
Make sure that `gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/'` succeeds before bundling.

In Gemfile:
  project was resolved to 0.1.0, which depends on
    action_cable_client was resolved to 3.1.0, which depends on
      websocket-eventmachine-client was resolved to 1.3.0, which depends on
        websocket-eventmachine-base was resolved to 1.2.0, which depends on
          eventmachine
```

## The fix!

To bypass this stack, the solution is to first install eventmachine locally before running `bundle install`.

The command is simply:

```
gem install eventmachine --pre
```

After this, you can successfully run `bundle install` to install the rest of your dependencies.

