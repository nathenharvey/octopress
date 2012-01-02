---
layout: post
title: "Deploying Green Screen"
date: 2012-01-02 16:59
comments: true
categories: 
  - chef
  - customink
  - devops
  - github
  - greenscreen
  - heroku
  - hudson
  - jenkins
  - opschef
  - opscode
  - sinatra
  - sysadmin
  - testing
  - vagrant
  - virtualbox
  - web operations
---
In my [previous post](http://nathenharvey.com/blog/2012/01/02/green-screen/), I introduced [Green Screen](https://github.com/customink/greenscreen), a build monitoring tool that is designed to be used as a dynamic Big Visible Chart (BVC) in your work area.  It lets you add links to your build servers and displays the largest possible information on a monitor so that the team can see the build status from anywhere in the room.


It is easy enough to get [Green Screen](https://github.com/customink/greenscreen) up and running on your own server or VM. The project's [README](https://github.com/customink/greenscreen/blob/master/README.md) includes all the information you'll need for doing so. In this post, I'll describe the steps necessary to run Green Screen on Heroku or on your own server using Chef.

## Deploying to Heroku

Deploying to [Heroku](http://www.heroku.com/) is probably the easiest way to get up and running with Green Screen. You'll need a Heroku account but a free one should be sufficient. Check the [quick start guide](http://devcenter.heroku.com/articles/quickstart) if you don't yet have an account.

Once you've got your Heroku account set-up, simply follow these steps to get your Green Screen app deployed:

1. `git clone git@github.com:customink/greenscreen.git`
1. `cd greenscreen`
1. `gem install heroku`
1. `heroku create`
1. `git push heroku master`
1. `heroku open`

If your build servers are running on the Internet, Heroku may be all that you need.

**Warning** this default Green Screen looks at all of the builds currently running on [http://ci.jenkins-ci.org](http://ci.jenkins-ci.org).  This is fine for demo purposes but you may find it to be a bit overwhelming since it's **over 300 builds** at the time of this writing.

You can see a sample of this app running at [http://greenscreenapp.com](http://greenscreenapp.com)

<!--more-->

## Deploying with Chef

If your build servers are not publicly accessible, Heroku won't be a great option. [CustomInk](http://www.customink.com) has published a [Chef cookbook](http://community.opscode.com/cookbooks/greenscreen) for setting up Green Screen on one of your nodes.

You simply need to include the greenscreen recipe to install, configure, and run one or more GreenScreen applications.  Or add it to your role, or directly to a node's recipes.

``` ruby 
include_recipe "greenscreen"
```

Of course, if you're just getting started with Chef, you should look at [Vagrant](http://vagrantup.com/) which is a tool for building and distributing virtualized development environments. With Vagrant, you can quickly spin-up a VM in [VirtualBox](http://www.virtualbox.org/) and have it use the greenscreen cookbook.

The cookbook allows you to specify credentials and jobs to include or ignore with each server and allows you to set-up multiple Green Screens on the same node. At CustomInk, we use different Green Screen applications for different teams.

Here's an excerpt from one of our Chef environment files:

``` ruby
"greenscreens" => [
  {
    :name => "greenscreen",
    :port => "4567",
    :servers => [
      {
        :url => "http://build01.customink.office:8080/cc.xml"
      },
      {
        :url => "http://build02.customink.office:8080/cc.xml",
        :username => "hudson",
        :password => "hudson_password"
      },
      {
        :url => "http://build03.customink.office:8080/cc.xml",
        :username => "hudson",
        :password => "hudson_password",
        :ignore_jobs => ["www_redirects"]
      }
    ]
  },
  {
    :name => "greenscreen.webops",
    :port => "4568",
    :servers => [
      {
        :url => "http://build03.customink.office:8080/cc.xml",
        :username => "hudson",
        :password => "hudson_password",
        :jobs => ["www_redirects"]
      },
      {
        :url => "http://build04.customink.office/cc.xml",
        :username => "jenkins",
        :password => "jenkis_password"
      }
    ]
  }
]
```

With this configuration, we have 2 Green Screens running, on ports 4567 and 4568. Both are polling build servers and showing different jobs. For instance, the server on 4567 excludes the www_redirects build (`:ignore_jobs => ["www_redirects"]`) whereas the server on 4568 only includes this build (`:jobs => ["www_redirects"]`) when polling the build03 server.



