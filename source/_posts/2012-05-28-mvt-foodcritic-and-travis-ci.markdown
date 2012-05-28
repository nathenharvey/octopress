---
layout: post
title: "MVT:  Foodcritic and Travis CI"
date: 2012-05-28 12:01
comments: true
categories: 
  - ChefConf
  - ChefConf2012
  - chef
  - customink
  - devops
  - opschef
  - opscode
  - foodcritic
  - travisci
  - testing
  - web operations
  - lordcope
---
One of the big themes that emerged during [#ChefConf](http://chefconf.opscode.com/) was that we should be testing our infrastructure code.  Software engineers have been practicing test-driven development, behavior-driven development, continuous integration, and many other testing-related practices for a long time.  It's becoming more important for the infrastructure engineers to learn from and apply these practices to our day-to-day workflow.  When it comes to testing Chef-driven infrastructure automation, there are a number of tools and practices that are starting to emerge.  In this article I'll look a "minimum viable testing" (MVT) approach to this problem using [Foodcritic](http://acrmp.github.com/foodcritic/) and [Travis CI](http://travis-ci.org/).  Follow the steps in this article to get your public cookbooks tested after every `git push`

### Testing with Chef

The idea of building automated tests for your infrastructure code has been getting a lot of traction lately.  When it comes to [Chef](http://www.opscode.com/chef/), many tools are starting to emerge.  

The first tool in this area to get any significant traction, that I know of, was [cucumber-chef](http://www.cucumber-chef.org/).  I first learned of this tool when I saw a pre-release copy of [Test-Driven Infrastructure with Chef](http://shop.oreilly.com/product/0636920020042.do) at the O'Reilly booth at [Velocity Conf 2011](http://velocityconf.com/velocity2011).  [Stephen Nelson-Smith](http://twitter.com/lordcope), the book's author and framework's lead developer, proposes an outside-in approach to testing where your tests can also act as monitors that look after the health of your infrastructure.  I like the idea of this approach and feel it makes a lot of sense in a greenfield environment.  In my particular situation this doesn't feel like the most appropriate way to start testing our infrastructure.  The blurring of testing and monitoring is appealing.  In fact, we typically practice what we call "monitoring-driven development."  When provisioning a new application server, for example, we wait until our monitors have validated the health of the server before adding the server to our load balancer.

[ChefSpec](https://github.com/acrmp/chefspec)is another tool for testing your Chef code.  It is a gem that makes it easy to write [RSpec](http://rspec.info/) examples for Chef cookbooks.  This style of testing allows you to execute your tests without needing to converge the node that your tests are running on.  In other words, you can execute your tests without needing to provision a server.  One huge appeal to this style of testing is that the feedback loop is very small.  You'll get feedback about your cookbook changes within seconds or a very few minutes of saving your changes.

