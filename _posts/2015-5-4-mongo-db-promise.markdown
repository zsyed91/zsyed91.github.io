---
layout: post
title: MongoDB and promises
summary: Use and wrap mongodb operations in promises
---

Using `node.js` and `mongodb` provide for an interesting stack. The catch of course, is that if you block during any operations, the entire server blocks. To get around this you use callbacks or async operations. The nice thing with using `mongoose` as the framework around MongoDB is that you can provide callbacks to your methods to prevent any blocking. The problem is that this quickly becomes callback hell:

{% highlight javascript %}

var mongoose = require('mongoose');

// Example employee list route
router.get('/employees', function(req, res, next){

	var Department = mongoose.model('Department');
	var Manager = mongoose.model('Manager');
	var Employee = mongoose.model('Employee');	

	// Apologies for not error checking!
	Department.find({name: 'Development'}, function(error, department){
		Manager.find({departmentId: department._id}, functioni(error, manager){
			Employee.find({manager: manager.name},function(error, employees){
				res.render('employees', {
					employees: employees
				});
			});
		});
	});

});

{% endhighlight %}

Ahhh what a nightmare. The code seems to get wider before it gets longer. It quickly becomes a sideways pyramid. Working with code like this is difficult and can easily lead to bugs from overlooking or missing something.

It would be way easier if we could somehow rewrite the code above like this:

{% highlight javascript %}

// Example index route
router.get('/', function(req, res, next){
	var Department = mongoose.model('Department');

	Department.getDepartment("Development")
		.then(function(department){
			var Manager = mongoose.model('Manager');
			return Manager.findManager(department);
		})
		.then(function(manager){
			var Employee = mongoose.model('Employee');	
			return Employee.getEmployeesForManager(manager);	
		})
		.then(function(employee){
			res.send('/employees', {
				employee: employee
			});

		})
		.catch(error) {
			console.log(error);
		})
		.done();

});

{% endhighlight %}

That looks so much better. We will be using `deferred promises` because this will allow us to convert the callback based methods of `mongoose` to promises.

Within our mongoose schema for `Department` we can wrap a static method into a promise. A javascript promise is basically a tool we have at our disposal to write async code without relying on nested callbacks. Instead we can provide a queue style approach, where the each subsequent step will only run after a successfull completion of the previous step. However nothing will block and with the `catch` method, we can fall directly there if there is any error before we complete everything. Promises are extremely flexible and you can do quite a bit with them. If you want to see a full list, please check it out [here](https://www.npmjs.com/package/q). 


## NPM Promise module - Q

We are going to use `Q` with `mongoose` to wrap our mongoose calls in promises. `Q` is just one of the many packages available that follow the official promise spec.

### Installing Q

Go to your `package.json` file and add the following in your dependency list. Replace the version number with whatever version number you need or `*` if you just always want the latest.

{% highlight json %}

"dependencies": {
    "q": "~1.2.0"
}

{% endhighlight %}

And run `npm install` in your `{project_root`} to install the module into your project.



### Importing Q

Lets assume all of our mongoose models are located in `{project_root}/models/`. We are going to have the following files:

- Department
- Manager
- Employee

In each of those files we are going to have the following 3 lines of code:

{% highlight javascript %}

var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var Q = require('q'); // We can now use promises!

{% endhighlight %}

## Wrapping a mongoose method into a promise

Ok now we get to the good part. Let's take a peek at `Department.js` and then I will explain what the code is doing.

{% highlight javascript %}

var mongoose = require("mongoose");
var Schema = mongoose.Schema;
var Q = require("q");

// Document schema in MongoDB
var DepartmentSchema = new Schema({
	name: String,
	budget: Number
});

// Static method we can call via Department.getDepartment in our code
Department.statics.getDepartment = function(name) {

	// Create our deferred object, which we will use in our promise chain
    var deferred = Q.defer();

    // Find a single department and return in
    this.findOne('name', {name: name}, function(error, department){

        if (error) {
        	// Throw an error
            deferred.reject(new Error(error));
        }
        else {
       		// No error, continue on
            deferred.resolve(manufacturers);
        }
    });

    // Return the promise that we want to use in our chain
    return deferred.promise;
}


// We can now call mongoose.model('Department') in our code
var Department = mongoose.model('Department', DepartmentSchema);

// This will allow us to do this if we want:
// var Department = require("./models/department")
module.exports = Department;
{% endhighlight %}



### Q.defer

`Q.defer()` will create a deferred promise so we can run our mongo query. Basically what happens here is we run our query and return immediately while the query will run in the background. 

We then return `deferred.promise` so that we can use `.then` and continue a chain.

### deferred.reject

In the event we have any errors, `deferred.reject` will jump to the `.catch` method in our promise chain. We can handle our error handling there as well as before calling `deferred.reject`.

### deferred.resolve

If the find was successfull we call resolve with any arguments and they will be passed in that order to the next promise chain. In this example, the department object will be passed into to the first `.then` method.

## Wrapping mongoose in Q promises

Now that we have seen `Department.js`. We can simply use `Q.defer()` and wrap our queries into promises. The promises must start with an initial promise, which is our `Department.getDepartment` method.

## The promise chain

Here is what our code looks like now that we created the first promise.

{% highlight javascript %}

// Inside the router.get() method
// Assume we instantiated our Department, Manager, and Employee mongoose schemas

// Approach 1 - Explicit promise variable

var promise = Department.getDepartment('Development');

promise.then(Manager.findManager)
	   .then(Employees.getEmployeesForManager)
	   .then(function(employees){
	   		res.send('employees', {
	   			employees: employees
	   		});
	   })
	   .catch(console.log)
	   .done();


// Approach 2 - Direct chain
	Department.getDepartment('Development')
	   .then(Manager.findManager)
	   .then(Employees.getEmployeesForManager)
	   .then(function(employees){
	   		res.send('employees', {
	   			employees: employees
	   		});
	   })
	   .catch(console.log)
	   .done();
{% endhighlight %}

Just look at how awesome and compact our code is now! So much easier to read, right? Our code is even cleaner now because we can pass a function reference directly into the `.then` method. Instead of doing this explicitly, which is equivalent to the above.

{% highlight javascript %}

Department.getDepartment('Development')
	.then(function(department){
		return Manager.findManager(department);
	})
	.then(function(manager){
		return Employee.getEmployeesForManager(manager);
	})
	.then(function(employees){
		res.send('employees', {
			employees: employees
		});
	})
	.catch(function(error){
		console.log(error);
	})
	.done();

{% endhighlight %}



## Conclusion

Now that we have wrapped our `mongoose` methods with deferred promises we can use promise chains and make our code easier to read, follow, and understand. The added benefit is that we have created functions within our schemas so we are following `DRY` principles as well. I will do a follow up post explaining promises in more detail. This post was written really just to do the above. Wrap our `mongoose` calls into `deferred promises` instead of nested callbacks.






