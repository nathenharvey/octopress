---
layout: post
title: "Passenger and PHP"
date: 2010-06-11 09:25
comments: true
categories: [customink, apache, passenger, ruby on rails, wordpress]
---
I recently migrated a site from mongrel to passenger. Today I learned that the wordpress site that was served up by the same webserver wasn't working anymore.  

Compared the configurations and everything looked OK, no changes were made as part of the migration. PHP module was being loaded, URL was being properly aliased, etc. Watched my logs and saw that Passenger was attempting to serve up the request which is not what I wanted.  

A quick update to my Directory's definition and the issue was fixed. I had to explicitly disable Passenger using `PassengerEnabled off` as seen below:

``` apache
  Alias /blog /shared/www/blog
  <Directory /shared/www/blog>
    DirectoryIndex index.php 
    PassengerEnabled off
  </Directory>
```