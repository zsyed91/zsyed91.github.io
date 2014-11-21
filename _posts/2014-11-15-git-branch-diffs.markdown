---
layout: post
title:  Git Tips - Diff 2 branches
date:   2014-11-15
summary: How to see what files were changes in one branch vs another (like the master branch)
---


Sometimes before mergine a development branch back into the master branch, I want to see what files were changed. The command to do this in git is simple. It will list out the files that were either added, modified, or deleted.


	git diff --name-status branch1..branch2

An example of checking a development branch against the master branch:

	git diff --name-status master..development


Will output:

	A   app/controllers/new_controller.rb
	M   app/views/layout/main_template.html.erb
	D   app/assets/javascripts/demo.js