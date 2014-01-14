CONTRIBUTING
===========

# General
* The following document will serve as a guide on what and how to contribute to any cookbook within [rackspace-cookbooks](http://github.com/rackspace-cookbooks/).
* The cookbook name, attribute namespace and git repo should all be the same
 * i.e. `rackspace_yum`, `rackspace_apache`
* Attempt to follow the style guide from [github](https://github.com/styleguide/ruby)
* The following items will not be supported in the rackspace-cookbooks
 * Compiling from Source in recipes
 * Operating systems outside `ubuntu`,`debian`,`rhel`,`centos`
* We will default to Rackspace provided systems
 * i.e. for ntp, we will default to rackspace ntp servers and fall back to community ones  

#Chef

## Attributes
* All attributes should by ruby symbols instead of strings
 * i.e. `default[:rackspace_apache]` instead of `default['apache']`
* All Attributes namespace should match the cookbook name
 * i.e. `default[:rackspace_user]`
* All Attributes that will be written a configuration file must fall under a [:config] hash
 * i.e. `default[:rackspace_apache][:config]`
* Attributes should default to the same ones as installed by the package or sane modifications.
* "data dirs" should be configurable for any service
 * i.e. for mysql `node[rackspace_mysql][data_di] = "/var/lib/mysql"` 

## Recipes
* monitors.rb - Standard rackspace cloud checks for this cookbook. These should not automaticlaly by included by the cookbook and should only be available to be included. 
 * This should populate the config hash `node[cloud_monitoring][monitors]` 

##Templates
* Template provider calls should always include the `cookbook` attribute as to allow for easily overriding from a wrapper cookbooks. The default should always be the cookbook itself.
 * i.e. in recipe


```ruby   
template "/etc/sudoers" do
  cookbook node[:rackspace_sudo][:templates_cookbook]
  source "sudoers.erb"
  mode 0440
  owner "root"
  group "root"
end
```

* i.e. in attributes

```ruby
   default[:rackspace_sudo][:templates_cookbook] = "rackspace-sudo"
```

#Misc

## GIT Tags
* Version increases should have an accompying git tag. Jenkins will be responsbile for ensuring this is complete.

## Licensing
* All Cookbooks must be Apache 2.0 licensed. 
* Include a `LICENSE` file in the top level directory of the cookbook with the Apache 2.0 Official license
* Include a `License and Authors` section of the `README.md`. See example at [rackspace-user](https://github.com/rackspace-cookbooks/rackspace-user)
* If you've forked the cookbook from another repo, please add notes attributing the original work to that repo

## README.md / Documentation
* Please include a README.md file in the cookbook root directory.
* Please include Descriptions, Platform support, notes, nots on recipes, attributes, CONTRIBUTING and testing specifications.
* If the cookbook is a fork, please credit original cookbook authors.

## CHANGELOG.md
* Please include a `CHANGELOG.md` in the cookbook root directory
* Please include brief notes about your changes and your name in each pull request

# Testing specifications

## CI
* All Cookbooks must be integrated with with a CI server.
    * Jenkins and TravisCI are two popular choices
* Added features should include additional tests. Pull requests without tests may be denied.

## test-kitchen support
* test-kitchen 1.0 support is required for all cookbooks. Please see [test-kitchen](https://github.com/opscode/test-kitchen) for more details.
* test-kitchen should include platforms for
 * Ubuntu 12.04
 * Debian 7.2
 * RHEL 6
 * CentOS 6

### test-kitchen structure
* Tests should be handled as a sub cookbook under /test/cookbooks/$name_test similar to how the opscode [chef-client](https://github.com/rackspace-cookbooks/chef-client) cookbook is layed out. 
* The tests should be called and have any needed attributes set in a .kitchen.yml file with seperate suites as appropiate. We will append our own .kitchen.local.yml via branch `testint` that provides kitchen-openstack support for our testing against openstack.

## foodcritic
* TODO: Add notes regarding foodcritic
* 

## chefspec/serverspec/minitest

# Code Review
* All code additions and pull requests are subject to the following:
    * Automated CI run must pass. Successes and Failure feedback will be provided in the pull request.
    * An admin must review the code prior to being merged 

# Guidelines
* TODO: add notes regarding guidelines for advanced cookbooks (libraries, LWRPS, etc)

