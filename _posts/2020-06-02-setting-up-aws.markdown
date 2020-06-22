---
layout: post
title: "[Rails 6 Series] Setting up AWS resources"
summary: Setting up the AWS resources needed including Elastic Beanstalk for our Rails 6 project
toc: true
---

<h3 class="no_toc">
Posts in Series
</h3>
* [Rails 6 Series - Starting Rails 6 with Vue on AWS](/2020/06/01/starting-rails-6-vue-on-aws/)
* Rails 6 Series - Setting up AWS resources
* [Rails 6 Series - Deploying to AWS](/2020/06/10/deploying-to-aws)

# Overview

To begin hosting our application in AWS we will first go into the Console and set up our environment configuration on AWS Elastic Beanstalk manually. Beanstalk
can create your RDS instance as well however we will be creating our separately. This is because resetting or tearing down your beanstalk stack also deletes the
RDS instance which may not always be desired. This post will go through the Console but everything can be done via the CLI as well. Since this is a one time setup, I have opted to just use the Console directly.

Let's go into the [Console](https://console.aws.amazon.com) and log in. After that select the desired region to use, I will be using `us-west-2`.

## VPC

For our beanstalk application, we will create our own VPC instead of using the default VPC. This is not strictly required but does make things cleaner down the road having the application separated into its own resources.
Doing so will not incur any additional cost either!

From the main console page, head over to the VPC page.

<img src="{{ "/images/2020_06_02_setting_up_aws/vpc.png" | prepend: site.baseurl }}" />

Create a VPC, you can name it whatever you want. I chose my project name for my VPC name. For the CIDR block we do not need to restrict ourselves so we will create a large VPC with a `/16` block.
If you feel that is excessive feel free to reduce it to `/18` or `/20`. We are also not using Dedicated tenancy so we will stick to Default.

<img src="{{ "/images/2020_06_02_setting_up_aws/select_vpc_button.png" | prepend: site.baseurl }}" />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_vpc.png" | prepend: site.baseurl }}" />

The next thing to ensure is that DNS resolution is enabled within your VPC. If this is not toggled then you will be unable to use an ELB with your EC2 targets. Simply go to `Edit DNS resolution` and ensure that the `DNS resolution` checkbox is enabled.

<img src="{{ "/images/2020_06_02_setting_up_aws/edit_dns_resolution.png" | prepend: site.baseurl }}" />
<hr />
<img src="{{ "/images/2020_06_02_setting_up_aws/enable_dns_resolution.png" | prepend: site.baseurl }}" />

### Subnet

Best practice is to create at least 2 subnets so that you can have hosts in 2 different availability zones. This increases the availability story of your application. Let's go ahead and create 2 subnet's in our new VPC (or default VPC if you decided not to create a new VPC). Head over to the VPC overview page and select the `Subnets` link from the left navbar. Hit the Create Subnet button at the top of the page.

1. First select the VPC you want to create the subnet
1. Pick an Availability Zone (make sure each subnet has a different one)
1. Create your CIDR range so that its a subset of the main VPC. For example use `172.30.1.0/24` and `172.30.2.0/24`.

<img src="{{ "/images/2020_06_02_setting_up_aws/subnet_overview.png" | prepend: site.baseurl }}" />
<hr />
<img src="{{ "/images/2020_06_02_setting_up_aws/create_subnet.png" | prepend: site.baseurl }}" />

To make life a bit easier let's make sure to enable public ip's on our instances. We will be applying a security group so that only the ELB can talk to the instances but this will allow us to expand that later if need be.

1. Select each subnet on the Subnets page for our VPC
1. Select `Actions` at the top and navigate to `Modify auto-assign IP settings`
1. Ensure that the checkbox is enabled

<img src="{{ "/images/2020_06_02_setting_up_aws/enable_public_ipv4_address.png" | prepend: site.baseurl }}" />

### Internet Gateway

In order to route traffic to your ELB and ultimately your application, your VPC must be able to have ingress/egress from the internet. On the same VPC page, select the `Internet Gateways` link and create a new Internet Gateway.
This will allow us to attach it to the VPC. Once the Internet Gateway is created, from the `Actions` dropdown attach it to the desired VPC.

<img src="{{ "/images/2020_06_02_setting_up_aws/internet_gateway_overview.png" | prepend: site.baseurl }}" />

### Route Table

The next step is to make sure that your route table can route traffic correctly between resources inside your VPC and from the internet through your internet gateway.

1. From the VPC page, select the `Route Tables` tab
1. Select the route table for your VPC
1. Under `Actions` select `Edit Routes`
1. In the routes page, make sure you have your VPC ip CIDR map to `local` and `0.0.0.0/0` route to your internet gateway

<img src="{{ "/images/2020_06_02_setting_up_aws/edit_routes.png" | prepend: site.baseurl }}" />

<img src="{{ "/images/2020_06_02_setting_up_aws/edit_routes_internet_gateway.png" | prepend: site.baseurl }}" />

## EC2

We will now setup our EC2 resources which will just consist of a new security group for our application. Go back to the console and head over to the EC2 page.

<img src="{{ "/images/2020_06_02_setting_up_aws/management_console_ec2_dropdown.png" | prepend: site.baseurl }}" class="center-image" />

### Security Group

We need 2 security groups for our application. The first one will be for our ELB/targets and the second will be for our RDS instance.

#### Application Security Group

From the left bar select `Security Groups` which will be under the `Network & Security` heading. After that hit the `Create security group` button.

1. You can name the security group anything, I chose `app_name-web`
1. Select your VPC
1. Outbound rules can be simple: `All Traffic` : `0.0.0.0/0`
1. Inbound rules
    1. Make sure to lock it to HTTP and HTTPS traffic only

<img src="{{ "/images/2020_06_02_setting_up_aws/create_security_group_button.png" | prepend: site.baseurl }}" class="center-image"/>

<img src="{{ "/images/2020_06_02_setting_up_aws/create_security_group_inbound_rules.png" | prepend: site.baseurl }}" class="center-image" />

#### RDS Security Group

Create another security group following the same steps as above with a few adjustments.

1. You can name the security group anything, I chose `app_name-db` to distinguish that this is for RDS
1. Select your VPC
1. Outbound rules can be simple: `All Traffic` : `0.0.0.0/0`
1. Inbound rules
    1. Use Protocol `TCP`
    1. Enter the port that you will use for the RDS instance, default Postgres port is `5432`
    1. Source should be the security group id from our application security group, `app_name-web`

This will lock down the RDS instance to just the instances within our security group for the application.

## RDS

The last thing we need to setup before we create our Elastic Beanstalk environment is the database. RDS provides a free tier for most of the databases offered. From the main console page, go to the RDS page.

1. From the RDS page, hit the `Create database` button
1. Select `Standard Create`
1. Select desired DB type, I went with `PostgreSQL`
1. Make sure to select `Free Tier` under `Templates` unless you are creating an actual Production stack
1. Under the Settings section, pick your database name, master username and password
1. Leave `DB instance size` as default
1. Leave `Storage` as default
1. In the `Connectivity` section make sure to select your VPC (not the default unless you want to)
    1. Expand the `Additional connectivity configration` area
    1. Make the DB publicly accessible (note: this is a quality of life thing if we want to connect from our local machine to the DB, it will be locked down via the security group)
    1. Remove the Default VPC security group and instead select the RDS security group we created earlier (ie, `app_name-db`)
    1. Pick any availability zone, ideally one of the ones that we selected for the subnets earlier
    1. Pick a database port, or use the default one for Postgre (`5432`)
1. Scroll to the bottom and hit the `Create database` button

<img src="{{ "/images/2020_06_02_setting_up_aws/management_console_rds_dropdown.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_rds.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_rds_step_1.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_rds_step_2.png" | prepend: site.baseurl }}" class="center-image"  />

## Elastic Beanstalk

Now we get to the final part of our AWS setup, which is to create our Elastic Beanstalk environment and application. All the pre-work we did up to the point will make the configuration very straight forward.

### Environment & Application

1. Go to the Elastic Beanstalk page
1. From the left navbar, select `Environments`

1. Create a new Environment
    1. This will also create a new application at the same time
    1. Create a `Web server environment` and hit `Select`
    1. Enter an application name and description
    1. Enter an environment name and let EB generate your domain
    1. Select `Managed platform` and use Platform: `Ruby`, Platform branch: `Ruby 2.7 running on 64bit Amazon Linux 2`, and Platform version: `3.0.2`
    1. Use the `Sample application` for now, we will push our code eventually
1. Hit the `Create environment button`

<img src="{{ "/images/2020_06_02_setting_up_aws/management_console_eb_dropdown.png" | prepend: site.baseurl }}"class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_new_eb_environment.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_new_eb_step_1.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_new_eb_step_2.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/create_new_eb_step_3.png" | prepend: site.baseurl }}" class="center-image"  />

For the next sections, simply go to your application in beanstalk and click on the environment name. This will take you to a page that looks like this:

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration.png" | prepend: site.baseurl }}" class="center-image"  />

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration_overview.png" | prepend: site.baseurl }}" class="center-image"  />

### Beanstalk Configuration - Software

1. Go to the Software section of the EB configuration section
1. Under the `Environment Properties` section make sure the following environment variables are set
    1. `BUNDLER_DEPLOYMENT_MODE = true`
    1. `BUNDLE_WITHOUT=` (blank value)
    1. `NODE_ENV = production`
    1. `RACK_ENV = production`
    1. `RAILS_ENV = production`
    1. `RAILS_SKIP_ASSET_COMPILATION = false`
    1. `RAILS_SKIP_MIGRATIONS = false`

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration_software.png" | prepend: site.baseurl }}" class="center-image"  />

### Beanstalk Configuration - Instances

1. Go to the Instances section of the EB configuration section
1. Under the `EC2 security groups` section select the web security group we created earlier along with the default EB security group that is already checked
    1. The security group that should be added is `app_name-web`
1. Leave everything else as default values

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration_instances.png" | prepend: site.baseurl }}" class="center-image"  />

### Beanstalk Configuration - Capacity

This step is **completely optional** and you can skip this if you do **not** want to use an ELB.

1. Go to the Capacity section of the EB configuration section
1. Under the `Auto Scaling Group` section
   1. Swap environment type to `Load balanced`
   1. [Optional] Set max instances to desired max count if scale out desired
   1. [Optional] Set Instance type to a larger host depending on your application needs, this will incur charges!
1. Under the `Scaling triggers` section
   1. Update the scaling triggers to what your application needs or just leave it to the default values for now, we can change this at any time

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration_capacity.png" | prepend: site.baseurl }}" class="center-image"  />

### Beanstalk Configuration - Load Balancer (ELB)

This step is **completely optional** and you can skip this if you do **not** want to use an ELB.

1. Go to the Load Balancer section of the EB configuration section
1. Under the `Listeners` section
    1. Make sure you have at least an HTTP listener
    1. Add in your HTTPS listener with your cert (you can use AWS ACM which will provision a free certificate)
1. Not much is required, you can also leave everything as default to have an ALB with an HTTP listener by default

### Beanstalk Configuration - Network

This step is **completely optional** and you can skip this if you do **not** want to use an ELB.

1. Go to the Network section of the EB configuration section
1. Under the `Load balancer settings` section
    1. Make your ELB Public
    1. Select the subnets created earlier
1. Under the `Instance settings` section
    1. Make sure to enable `Public IP addreess` on your instances so that the ELB will be able to send traffic to them
    1. Enable all subnets here as well

<img src="{{ "/images/2020_06_02_setting_up_aws/eb_configuration_network.png" | prepend: site.baseurl }}" class="center-image"  />

# Conclusion

Now our AWS stack is ready for us to deploy and iterate on top of! At this point we have created security groups, a VPC and subnets for it, configured the routing table and opened the VPC to the internet via an internet gateway, and lastly spun up an RDS database. With all of this and our elastic beanstalk environment, we can make changes and deploy our application with super ease, a simple call to `eb deploy` will get us up and running. We will launch our application in the next post in this series.

To check out the default application that elastic beanstalk has created for us, navigate to the endpoint that beanstalk provisioned to see the sample stack up and running.

#### Rails 6 Series

* [Rails 6 Series - Starting Rails 6 with Vue on AWS](/2020/06/01/starting-rails-6-vue-on-aws/)
* Rails 6 Series - Setting up AWS resources
* [Rails 6 Series - Deploying to AWS](/2020/06/10/deploying-to-aws)