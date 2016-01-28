---
layout: post
title: Perl Pull Request Challenge 2016
summary: Second year, hopefully more successful
---

This is the second year of the perl pull request challenge. One repo per month with the
goal of at least 1 pull request open or merged. With great thanks to [Neil Bowers](http://neilb.org)
to getting this up and running. I am giving this a second go this year with the hope of sticking
through the end of the year.


For January I got the [Exobrain](https://github.com/pjf/exobrain) repo. I have opened 2 pull requests so far
and probably won't be able to open more before the month ends. The first one was pretty simple and consisted of
an extermely minor typo fix in a comment.


The second one involved a bit more work. It is for adding validation to a custom `MooseX::Types`. I don't have
much experience with the `MooseX` framework but the code is pretty readable. I plan on both adding the validation
and creating some new tests to make sure the validations are accurate and correct. The original code did not
have any validation on the `PhoneNum` type.

{% highlight perl %}
subtype PhoneNum,
    as Str,
    where { 1 },
;
{% endhighlight %}

My suggested change at the moment is making a regex that will ensure the phone number fits a certain style. Like the following:

{% highlight perl %}
subtype PhoneNum,
    as Str,
    where { $_ ~= /\+?\d+$/ },
;
{% endhighlight %}

Once I get the implementation finalized I will write a post explaining the change in greater detail. But the regex above
basically makes sure the the phone number consists of an optional `+` and the beginning which is denoted by the `\+?`. The question
mark makes the escaped `\+` optional. After the optional plus, we have strictly digits denoted by the `\d` until the end of the string
which is denoted by the `\$` at the end.

In conlusion, let's see how far I can go this year!
