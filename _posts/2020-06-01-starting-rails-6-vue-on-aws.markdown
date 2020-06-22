---
layout: post
title: "[Rails 6 Series] Starting Rails 6 with Vue on AWS"
summary: This is the first post of a series on launching a Rails 6 app with Vue/Webpack on AWS.
---

I recently started a personal project and wanted to try out Rails 6 with Vue (and Webpack). I was able to get this stack working locally with relative ease. However, when I attempted to push
this idea to production I was met with many roadblocks. My goal was to use AWS to host the project. AWS has migrated to using "Amazon Linux 2" which has resulted in a lot of
outdated tutorials and code snippets. Furthermore, I also found a lack of conscise and up to date documentation for pushing Rails 6 with Webpack to production.

I will be documenting my journey on pushing my personal project to AWS through a series of posts to hopefully help with the lack of documentation. To kick the series off, I will note down
which resources I will be using:

- [Ruby 2.7](https://www.ruby-lang.org/en/news/2019/12/25/ruby-2-7-0-released/)
- [Rails 6](https://edgeguides.rubyonrails.org/6_0_release_notes.html)
- [Vue.js](https://vuejs.org/guide/)
- [Webpack](https://webpack.js.org/guides/)
- [AWS](https://console.aws.amazon.com/)
  - [EB (Elastic Beanstalk)](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)
  - RDS (Postgresql)
  - EC2 (VPC/Security Group setup)
  - [Optional] ELB (Elastic Load Balancing)

For the most part we can get by using the free tier on AWS. However, because I will be using this to document my personal project some resources may not actually fall into the free tier. I will call these out as optional items or provide an alternative that is within the free tier.

### Getting Started

For the purpose of this series, I will not go into the details on how to create an AWS account. If you do not have an account already I would highly advise just following the [official guide](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/).

### Install Ruby

To install ruby locally there are several tools available that let you install multiple versions of ruby. We will use RVM.

First install RVM: [https://rvm.io/rvm/install](https://rvm.io/rvm/install). Once that is complete proceed with installing the latest ruby (2.7.1 at the time of this post).

{% highlight bash %}
rvm install ruby-2.7.1
{% endhighlight %}

In case you have multiple versions of ruby installed, you can switch to using the one we just installed with the following command:

{% highlight bash %}
rvm use ruby-2.7.1
{% endhighlight %}

You can see if you have other ruby versions installed as well with `rvm list` and confirm which one is currently being used:

```bash
> rvm list
   ruby-2.4.0 [ x86_64 ]
   ruby-2.4.3 [ x86_64 ]
 * ruby-2.6.3 [ x86_64 ]
=> ruby-2.7.1 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

### Install Rails

Installing rails should be fairly straight forward. Simply run `gem install rails` and then you can call `rails` from your terminal.

Confirm you have rails installed

{% highlight bash %}
rails --version
Rails 6.0.3
{% endhighlight %}

### Install Elastic Beanstalk

Elastic Beanstalk is a deployment orchestration engine provided by AWS. You can configure beanstalk to launch databases in RDS, spin up ELB's, and launch the EC2 instances that will host your application. There are other options to perform this but for ease we will rely on AWS elastic beanstalk for the heavy lifting. The beanstalk binary is not part of the AWS SDK so we will have to install it separately. In order to get the CLI in your environment, AWS provides a nice [automated installer](https://github.com/aws/aws-elastic-beanstalk-cli-setup).

### Bootstrap Project

Now that we have installed the prerequisites, let's go ahead and create the project in our local environment. We wil be calling the `rails` command and telling it to use `Vue` with webpack and `Postgres` as our database. If you wish to either avoid RDS or use another database type, feel free. One option is to leverage `sqlite` but the catch is that this will run on the EC2 host itself. Having said that, we will be using the free tier with RDS.

{% highlight bash %}
rails new app_name --webpack=vue --database=postgresql
cd app_name
bundle install
{% endhighlight %}

### Conclusion

At this point we created our local environment and installed the necessary binaries. The next post will dive into setting up AWS resources including the beanstalk environment, RDS instance, and VPC/Security Groups.

#### Rails 6 Series
* Rails 6 Series - Starting Rails 6 with Vue on AWS
* [Rails 6 Series - Setting up AWS resources](/2020/06/02/setting-up-aws/)
* [Rails 6 Series - Deploying to AWS](/2020/06/10/deploying-to-aws)