---
layout: post
title: Add RuboCop to Existing Projects
summary: Quick guide on adding RuboCop to new or existing projects to increase code quality
toc: true
---

Adding RuboCop to a project might seem daunting but is completely do-able. When working with an existing code base that might have grown in size, it can seem impossible to consider fixing all the RuboCop issues up front. To accomodate this, RuboCop has a built-in functionality to create a TODO file of existing violations that can then slowly be tackled over time.

## Installing RuboCop

The following command will install rubocop globally:

```bash
gem install rubocop
```

If you desire an installation directly into the project, you can add it to the `Gemfile`

```ruby
gem 'rubocop', require: false
```

## Generating .rubocop_todo.yml

By default rubocop will turn off the entire cop if there are too many violations. This is **not** what we want, because if we let the entire cop become disabled then any new code is allowed to continue that violation. Instead when we generate the whitelisting file, we'll tell rubocop to keep whitelisting files instead of disabling the entire cop. To do that, we will run this command:

{% highlight bash %}
rubocop --auto-gen-config --auto-gen-only-exlude --exclude-limit 10000
{% endhighlight %}

<span class="bold">auto-gen-config - </span>runs RuboCop against the codebase and captures all violations into a `.rubocop_todo.yml` file.

<span class="bold">auto-gen-only-exclude - </span>ensures that RuboCop adds Exclude and not Max when generating the results. This flag is used in conjuction to the `--exclude-limit`. We need to ensure that the second flag is high enough otherwise `auto-gen-only-exclude` will revert back to using Max.

<span class="bold">exclude-limit - </span>limits how many files will be added to the `Exclude` block for each cop. The default is very low (15) and if we do not adjust this higher, we will end up turning off the entire cop if there are too many violations. This flag is the key element to our whitelisting approach. As long as this value is high enough, none of our cops will be outright disabled and we can continue onboarding new code at higher standards while working to fix all the violations on the existing code.

As a sanity check, once generating the file is complete make sure that no cops are disabled. If they are, re-run the command above with a higher `--exclude-limit`.

## Addressing Existing Violations

The key takeaway with generating the `.rubocop_todo.yml` is not to forget the old violations but to slowly tackle them while enforcing all new code to higher standards. This is a continuious improvement effort. When fixing the issues, make sure that the commits are solely for those and not regular feature work as well. It is much easier to push along changes that are code style improvements and provides a nice commit history of how the improvements were handled.

RuboCop provides a `--auto-correct` option that will attempt to clean up the violations for the supported cop. The feature is still experimental as of this post but worth starting off with. It's easy to run the command and run a quick `git diff` to make sure that the new changes are valid and of course running your test suite to make sure there aren't bugs. This is another reason why its much better to commit refactors and code cleanups into their own isolated commits.

## Conclusion

RuboCop makes it a lot easier to have newer engineers onboard to a Ruby project by leveraging consistent coding styles and enforcing new commits to adhere to them. For new or old projects, RuboCop can easily be part of the workflow to keep the code quality high and remove the burden of reviewing coding styles within the code review process. I hope that at this point it should clear that onboarding RuboCop is very straight forward.

To learn more about the RuboCop command line flags, check out the [official docs](https://docs.rubocop.org/rubocop/usage/basic_usage.html#). RuboCop uses the rules defined by the [official ruby style guide](https://rubystyle.guide/).