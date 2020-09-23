---
layout: post
title: Deploying Rails to AWS
author: igbanam
category: blog
tag:
- aws
- rails
- journal
headerImage: false
---

> TL;DR If Rails can be deployed on a VPS, can it be deployed on a shared server?

I want to build applications which value. One of the most important steps in my value chain is deploying this application; bringing the application into the customer's hands. There are many platforms through which I can deliver my application. One of the easiest to use is Heroku which provides a platform, and orchestrates most of the deployment details away. For the longest time, I deployed Rails applications using the command below

```
git push heroku feature-branch:master
```

Recently, I looked into what it would take to deploy Rails into a VPS which I had complete control over. It was quite an interesting exercise; laced with even more interesting problems. Here, I would detail the steps I used in getting Rails 6 deployed on an AWS VPS.

What did I need

  - A server
  - A database
  - A deployment tool
  - An application to deploy

## A Server

To get the server running, I provisioned an Ubuntu server on AWS EC2. This is available in the free tier. I chose Ubuntu because I am comfortable with its terminal environment. You can use any linux box you're comfortable with. The commands I use here are specific to Ubuntu. Do well to find and use the equivalent for your Linux box.

To Launch the EC2 instance:

  - select EC2 from the list of AWS services
  - click on "Launch Instance"
  - the defaults should be fine, but do well to click through them
  - at the Security Group, make sure to open up port 80 for web traffic; it's closed by default

At this point, I am thinking "Someone should have done the work. This isn't novel." So I go to google, searching. I see this [fanta-amazing post][1] from GoRails. Chris Oliver does a good job in setting up Rails on an EC2 instance. This remains my north star for deployments.

The only pitfall I faced here was around assets. See… I had created a new rails application to deploy to the server. There were no controllers, no models, nothing. Just a vanilla Rails application. Turns out Capistrano did not feel the need to cmopile assets since it was not needed at the time.

> Always have an application you want to deploy before deploying

With Rails now deployed on AWS, running on NGINX + Passenger, deployed with Capistrano… I began to think if this is possible on shared servers.

## Updates

### Deploy Keys

Github has this neat feature where it allows you to add SSH keys per repository.
Github calls this "Deploy Keys". The idea is to give a certain key rights to a
reposoty so it can pull (by default) contents of that repository. This makes it
easy for servers to [add their public SSH keys to these repositories as Deploy
keys][2] and allow deployment do on smoothly.


### SSH Forwarding

On AWS, the acceptable way to ues an SSH client to access your server us through
a PEM file. When deplying using Capistrano, you would have to tell Capistrano to
use this private key while running your deploys.

```ruby
# in config/deploy.rb

set :ssh_options, {
  forward_agent: true,
  auth_methods: %w[publickey],
  keys: %w[/path/to/key.pem]
}
```

  [1]: https://gorails.com/deploy/ubuntu/18.04
  [2]: https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys
