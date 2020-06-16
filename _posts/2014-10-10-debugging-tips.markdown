---
layout: post
title: Debugging Tips
date: 2014-10-10 12:45:00
summary: Tips and tricks on how to debug code effectively
---

Debugging is useful for finding out how a bug occurs in code. However, another useful scenario for debugging is exploring how some piece of code works.

### Approach 1: Exploring code

Sometimes we run into code that we either have to alter or use. We can harness our debugging skills to find out how the code works and what we need to do to make our changes to it.

#### Tip 1: Start at the bottom

If we start at the point of code that is the result and work our way backwards, we can learn the flow of the application. For example, lets take a look at this code snippet:

{% highlight javascript %}
function cryptic_method(city) {

    if (city.population < 1000) {
    	return 'Rural';
    }

    if (city.population < 50,000) {
    	return 'Suburb';
    }

    return 'Urban';

}

// ... some complex code ...

\$("#vague_class").val(cryptic_method(dst));
{% endhighlight %}

If we looked at this file (assume it contains lots of different methods) we would notice that the code is quite unclear. Suppose we had a form and wanted to know how a particular field obtained a value. If we start at the bottom where we have:

{% highlight javascript %}
\$("#vague_class").val(cryptic_method(dst));
{% endhighlight %}

It would be confusing at first. What does cryptic_method do? What is dst, and what is its value?

#### Tip 2: Work in small logical chunks

First lets take a look at what the cryptic_method does and what it returns.

{% highlight javascript %}
function cryptic_method(city) {

    if (city.population < 1000) {
    	return 'Rural';
    }

    if (city.population < 50,000) {
    	return 'Suburb';
    }

    return 'Urban';

}
{% endhighlight %}

The argument that this function takes is `city`. Looking at the function contents we see that the `city` argument contains a population key. The first `if` statement checks to see if the population is less than 1000 and if so, returns Rural. The second `if` statement checks to see if population is less than 50,000 and if so returns Suburb. And then finally if the population is 50,0001 or more it returns Urban.

We can conclude that `dst` must be a city that contains a `population` key that is an integer. The `cryptic_method` takes this value and returns the type of city depending on the city's population. So the field in question is updated depending on what the population size is for that city.

If we worked our way from top to bottom, we would have to look at each function defined in the file before we reached our line that applies the action.

### Approach 2: Find the source of a bug

Let's change to another file in our application. Our scenario is simple, we need to add 2 inputs together and get a result. Our method concats 2 inputs and returns it.

{% highlight javascript %}
function concat(a, b) {
    return a + b;
}
{% endhighlight %}

In javascript, two strings are concatted by using the `+` operator in between both strings. This method will work fine if we do the following:

{% highlight javascript %}

concat("Hello ", "world");
concat("#", "5");

// Outputs "Hello world";
// Outputs "#5";
{% endhighlight %}

Now lets say we are told we need to implement a form on an existing page that takes two fields and outputs the fields as a concatenation.
We look through our library and find this really usefull function. So we go ahead and put this on the page:

{% highlight html %}

<form onsubmit="alertCombination()">
	<input type="text" name="part1" id="part1" />
	<input type="text" name="part2" id="part2" />
</form>

{% endhighlight %}

We then define our `alertCombination` method:

{% highlight javascript %}
function alertCombination() {
    var part1 = $("part1").val();
    var part2 = $("part2").val();

    alert(concat(part1, part2));
}
{% endhighlight %}

We go ahead and commit this change and push it onto production. Now, we get a bug report saying that the alert message is showing an incorrect value. The form was supposed to take integer inputs and concat them. The report stated that an input of 1 and 5 were not returning 15.

#### Tip 3: Run code with known inputs and a known output

We know that an input of 1 and 5 should return 15 so let's first run this through our form and observe the results.

{% highlight javascript %}
// Enter 1 into the 'part1' field
// Enter 5 into the 'part2' field

// Run alertCombination()

// Alert box shows "6"
{% endhighlight %}

Ok, so not as expected. Well we now know some inputs that do not result in the correct output. The next step is to fix this bug.

#### Tip 4: Start at the highest level of code

We remember to start at the bottom. In this case "bottom" represents the highest level of code. We look at our input boxes and see nothing particular. We then move up and look at the what is called when the form is submitted and note that the `form` tag has an `onsubmit` method `alertCombination`. Let's take a look at this method now.

{% highlight javascript %}
function alertCombination() {
    var part1 = $("part1").val();
    var part2 = $("part2").val();

    alert(concat(part1, part2));
}
{% endhighlight %}

There are two variables here that simply get the value. Lets go further. We then take a look at the alert statement and note a method `concat` is called passing in both of our variables. We will dive into that method now.

{% highlight javascript %}
function concat(a, b) {
    return a + b;
}
{% endhighlight %}

This method takes to inputs and returns the concatenation of both. Except for when both `a` and `b` are integers! Javascript will treat the `+` operator on two integers as an addition! The change we need to make is to force one or both variable as strings to force a string concatenation. An alternative solution is to try `a + "" + b`. This would be fine but we want to make our intentions clear. We will update the function to the following:

{% highlight javascript %}
function concat(a, b) {
    return a.toString() + b.toString();
}

// Alternative method to take advantage of casting
function concat(a, b) {
    return a.toString() + b;
}
{% endhighlight %}

We can test our code with the inputs of 1 and 5:

{% highlight javascript %}
concat(1, 5);

// Returns "15";
{% endhighlight %}

#### Tip 5: Write a test case after fixing bugs

Now that we found the bug and fixed it, the most important step is to create a test case for this bug. This is so if any code changes in the future we don't introduce this regression back in. You can use your testing framework of your choosing.

{% highlight javascript %}

describe("The function concat", function() {
    it("should concat two vars into one when both vars are integers", function() {
        expect(concat(1, 5)).toBe("15");
    });
});

{% endhighlight %}

### Conclusion

Debugging is an excellent skill to have. It serves multiple purposes and can help find bugs or understand how some logic in the code works. These are just some of the many tips on how to effectively debug code.
