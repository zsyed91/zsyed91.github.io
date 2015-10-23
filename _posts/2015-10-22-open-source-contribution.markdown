---
layout: post
title: Pledge to contribute to open source
date: 2015-10-22
summary: After breaking the ice, a pledge to continue contributing to open source projects
---

I recently completed my 4 pull requests needed for the 
[Hacktoberfest Challenge](https://hacktoberfest.digitalocean.com) sponsored by Digital Ocean. I found it difficult at first because I have been wanting to find and pick a project that I am capable of contributing to. Well the pressure was on after signing up for hacktoberfest. I decided to just pick a project that I use in my day to day life. The project I have chosen is [RSpec](http://rspec.info).

I initially took a peak at the open issues and was able to actually find an issue that seemed reasonable. But then I noticed someone beat me to it! I then simply cloned the repo and ran the test suite, _which is RSpec itself :)_. There was a failing test! The change seemed straight forward so I went ahead and made the change, which simply consisted of removing some extra spaces. I went ahead and opened my first pull request against RSpec. But, that too, was closed as someone beat me to it.

Well none of the other open issues seemed doable until I started learning the codebase. That is exactly what I started to do and I noticed that the code looked like it had areas for improvement. And thats when it clicked!

You can contribute to making the code cleaner, not just fix bugs and add features! With this new realization I began looking at various files and running rubocop on it. I began tackling a couple files and cleaning them up best I could. This mainly consisted of spacing issues and breaking up really long lines. But eventually I was able to make very minor performance tweaks, `tr` instead of `gsub`. 

Armed with this new goal, I am pledging to make at least 2 pull request per month. I will increase this as I go, but as a minimum baseline I will attempt 2 per month. I will continue looking at the RSpec codebase and try to clean it up best I can and hopefully learn the codebase as I go. I will then try to tackle any open issues and even look towards adding features!

My contributions towards RSpec can be found [here](https://github.com/rspec/rspec-expectations/commits?author=zsyed91).
