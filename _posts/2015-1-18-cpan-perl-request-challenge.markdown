---
layout: post
title:  2015 CPAN Perl Challenge
date:   2015-1-18
summary: CPAN Perl pull request challenge, 1 distro per month
---

I have been assigned the `Ouch` perl module for the month of January. While the module seems pretty stable I decided to try and integrate it with `travis-ci`. I have never used Travis or created a `Build.PL` file. So I set out to find out how to do it. I discovered that its pretty easy.



## Travis CI


#### Create a travis account

Before you create any files, it is easy to create an account. The purpose of a travis account is so that it can access your github account and run the test cases as needed when the repository receives commits. The signup process will ask you to link your github account. After doing so, you can select which repositories that you want travis to watch for.



#### .travis.yml
First you need to create a `.travis.yml` file in your repository. The format is pretty simple. Here is an example of mine for perl:

    language: perl
    perl:
      - "5.18"
      - "5.16"
      - "5.14"
      - "5.12"
      - "5.10"
      - "5.8"


You can be as specific as you want to for the language versions. As of today, according to the travis documentation, if you leave out the `perl` key with the versions, it will default to `5.14`




## Build file

Travis looks for a `Build.PL` file at the root level of the repository. I found that using `Module::Build` makes it really easy to create a build system for your module.

My build script was this:

{% highlight perl %}

use strict;
use warnings;

use Module::Build;

my $builder = Module::Build->new(
	'module_name' => 'Ouch',
	'license' => 'perl',
	'dist_author' => 'JT Smith',
	'build_requires' => {
		'Test::More' => 0
	},
	'create_makefile_pl' => 'traditional'
);

$builder->create_build_script();

{% endhighlight %}

That is all you need for travis but if you wanted to make a build file for your own use directly then all you need to do is `perl Build.PL` and it outputs a `Build` file. There are numerous commands to pass into `Build` but I think these are the most common:



- `./Build install` will install the perl modules into your perl library. 
- `./Build test` will run the test cases for the module.
- `./Build build` will compile the libraries.




