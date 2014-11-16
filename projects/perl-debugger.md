---
layout: page
title: Perl Debugger
permalink: /projects/perl-debugger/
---

This project was designed to aid users in selecting better passwords when signing up for services.



### Features

- Log method to log strings and scalar variables
- Dump method to inspect data structures, objects and other variables
- Provide stacktraces
- Provide timing
	- Script
	- Methods only
	- Arbitrary points
- A pure perl library


### How to use

All you need to do is import the library by putting this in your script:
`use Debugger;`

#### Constructor arguments

`log_file => path/to/debug/file`

This is optional and defaults to `debug.log` where the script is stored.


### Examples
  
#### Object instantiation

{% highlight perl %}

my $debug = new Debugger();
# Or
my $debug = new Debugger({
	'log_file' => '/path/to/debug/file'
});

{% endhighlight %}

#### Log a message or scalar variable
{% highlight perl %}

my $x = "some text in this variable";
$debug->log($x);

{% endhighlight %}
Will output `some text in this variable` into the log file.


#### Dump an object or variable

##### Example 1

{% highlight perl %}

my @lines = get_lines();

$debug->dump(\@lines);

{% endhighlight %}

Will output the contents of `@lines` to the log file.

##### Example 2
  
 {% highlight perl %}

my $class = new Some::Class();

$debug->dump($class);
# Outputs the current state of the object

{% endhighlight %}

#### Time a script

{% highlight perl %}
	
# Top of script
my $debug = new Debugger(); # <- Time starts from constructor

#... rest of script ...

$debug->timestamp(); # <- Provides time difference from constructor to this point and logs it

{% endhighlight %}


#### Provide a stacktrace
	
{% highlight perl %}

sub a_function_nested_deeply {
  my $self = shift;

  $debug->stacktrace(); # <- Provide how we got here via stacktrace;
}

{% endhighlight %}

### Get it on Github

You can download the project via github: [Perl Debugger](https://github.com/zsyed91/perl-debugger)