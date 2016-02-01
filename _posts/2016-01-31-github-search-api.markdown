---
layout: post
title: Github search api
summary: How to use the octokit's search api in ruby
---

I recently created the [pr_status gem](https://github.com/zsyed91/pr_status) to
be able to easily get the number of pull requests I opened in the current month.
The inspiration came from being able to track my progress to my commit of
[opening 2 pulls](/2015/10/22/open-source-contribution/). I should hopefully sometime soon
be able to use this gem and officially keep track of my goal and progress each month.

I found the documentation for the api a bit lacking as I was initially following
the url structure provided. Something like the following:

{% highlight ruby %}
# did NOT work!
@octokit.search_issues('author:zsyed91+type:pr')
{% endhighlight %}

It turns out that the values are `URI` escaped and so using `+` is not needed. A
simple space is sufficient. The full api documentation is available [here](https://developer.github.com/v3/search/#search-issues).

### Find issues and pull requests by author

This will return all issues and pull requests opened by the author.

{% highlight ruby %}
@octokit.search_issues('author:zsyed91')
{% endhighlight %}

### Find pull requests only

This will return all pull requests opened by the author.

{% highlight ruby %}
@octokit.search_issues('author:zsyed91 type:pr')
{% endhighlight %}

### Find pull requests opened after a certain date

The date fields follow the `yyyy-mm-dd` format. This is how to get all pull requests
opened by the author on or after January 1st, 2016.

{% highlight ruby %}
@octokit.search_issues('author:zsyed91 type:pr created:>=2016-01-01')
{% endhighlight %}

In order to exclude the starting date simply change `>=` to `>`.

{% highlight ruby %}
@octokit.search_issues('author:zsyed91 type:pr created:>2016-01-01')
{% endhighlight %}

In order to get all pull requests opened on a specific date remove the `>` or `>=` qualifier.

{% highlight ruby %}
@octokit.search_issues('author:zsyed91 type:pr created:2016-01-01')
{% endhighlight %}


### Find pull requests by language
In order to get pull requests opened for a specific language for a certain user do the following:

{% highlight ruby %}
@octokit.search_issues('author:zsyed91 type:pr language:ruby')
{% endhighlight %}


### Conclusion
The gist of this post is just to show some examples on how to actually use the `Octokit::Client` api for `search_issues`.
As stated before, the full api options can be found [here](https://developer.github.com/v3/search/#search-issues).
Keep in mind the api rate limit as of January 2016 is 10 requests per minute for unauthenticated requests and 30 requests per minute
for authenticated requests.
