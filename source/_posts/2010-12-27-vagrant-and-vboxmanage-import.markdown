---
layout: post
title: "Vagrant and VBoxManage import"
date: 2010-12-27 19:25
comments: true
categories:
  - chef
  - devops
  - opscode
  - sysadmin
  - vagrant
  - virtualbox
  - web operations
---
I've recently started using [Vagrant](http://vagrantup.com/) for building local VMs with VirtualBox. It's a great way to test out my [Chef](http://www.opscode.com/chef) cookbooks.

I recently ran into an error after a system crash. When I tried to run:

``` bash
vagrant up
```

the following error was reported:

``` bash
The VM import failed! Try running `VBoxManage import` on the box file manually 
for more verbose error output.
```

To successfully run VBoxManage import, you first need to locate the .ovf file used by Vagrant when building your VM. Typically, you'll find your box definitions in the `~/.vagrant/boxes` directory. So, I tried to run the command specified from there:

``` bash
cd ~/.vagrant/boxes/base

VBoxManage import box.ovf

ERROR: Cannot register the hard disk 
'~/.vagrant/boxes/base/vagrant-centos64-disk.vmdk' with UUID
{5d2426bf-f04e-40a0-bf36-a27c8d45f920} because a hard disk
'~/.vagrant/boxes/base/vagrant-centos64-disk.vmdk' with UUID
{5d2426bf-f04e-40a0-bf36-a27c8d45f920} already exists in the media registry
('~/Library/VirtualBox/VirtualBox.xml')
```

To get around this, I simply removed `~/Library/VirtualBox/VirtualBox.xml`. I was then able to vagrant up and continue on with my Chef cookbook.