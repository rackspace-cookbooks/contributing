CONTRIBUTING
===========

# General
* The following document will serve as a guide on what and how to contribute to any cookbook within [rackspace-cookbooks](http://github.com/rackspace-cookbooks/).
* The cookbook name, attribute namespace and git repo should all be the same
 * i.e. `rackspace_yum`, `rackspace_apache`
* The following items will not be supported in the rackspace-cookbooks
 * Compiling from Source in recipes
 * Operating systems outside `ubuntu`,`debian`,`rhel`,`centos`
* We will default to Rackspace provided systems
 * i.e. for ntp, we will default to rackspace ntp servers and fall back to community ones  
* Berkshelf should update to point to github locations for any cookbook dependency
 * i.e `cookbook "rackspace_apt", github: "rackspace_cookbooks/rackspace_apt"`

#Chef
## Attributes
* All Attribute hashes should be indexed with strings instead of symbols or using 'dot notation'
 * i.e. `node['rackspace_apt']['config']`, not `node['rackspace_apt']['config']` and not `node.rackspace_apt.config`
* All Attributes namespace should match the cookbook name
 * i.e. `default['rackspace_user']`
* All Attributes that will be written a configuration file must fall under a ['config'] hash
 * i.e. `default['rackspace_apache']['config']`
* Attributes should default to the same ones as installed by the package or sane modifications.
* "data dirs" should be configurable for any service
 * i.e. for mysql `default['rackspace_mysql']['data_dir'] = "/var/lib/mysql"` 

## Recipes
* monitors.rb - Standard rackspace cloud checks for this cookbook. These should not automatically be included by the cookbook and should only be available to be included. 
 * This should populate the config hash `node['rackspace_cloudmonitoring']['monitors']` 

##Templates

### Headers
Templates must contain a banner stating they are Chef managed and the name of the controlling cookbook.

### Config Hashes
Templates should use configuration hashes as much as possible to allow adding of options to the config without needing to commit changes to the core cookbook.
The config hash must:
* Use the `node['cookbook']['config']` namespace
* Use an inner hash (`node[cookbook]['config']['key'] = value`) instead of direct key:value pairs.

An example of a mongodb.conf template exclusively following this style would be:

```ruby
#
# CHEF MANAGED FILE: DO NOT EDIT
# Controlling Cookbook: rackspace_contributing_example
#

<%
# Generated using a config hash methodology
# Each setting is a key in the node['rackspace_contributing_example']['config']['mongodb.conf'] hash
#
# The key of the config hash is the option name.
#   The supported keys of the value are
#      'comment': An optional comment
#      'value': The value for the option

node['rackspace_contributing_example']['config']['mongodb.conf'].each do |key, value|
  if value.key?('comment')
-%>
# <%= key -%>: <%= value['comment'] %>
<% end -%>
<%= key -%> = <%= value['value'] %>
<% end -%>  
```

And the corresponding attributes/default.rb:

```ruby
# Attributes used in the mongodb.conf file
default['rackspace_contributing_example']['config']['mongodb.conf']['dbpath']['value']       = '/var/lib/mongodb'
default['rackspace_contributing_example']['config']['mongodb.conf']['logpath']['value']      = '/var/log/mongodb/mongodb.log'
default['rackspace_contributing_example']['config']['mongodb.conf']['logappend']['comment']  = 'Append to the logs by default.'
default['rackspace_contributing_example']['config']['mongodb.conf']['logappend']['value']    = true
default['rackspace_contributing_example']['config']['mongodb.conf']['auth']['value']         = true
default['rackspace_contributing_example']['config']['mongodb.conf']['keyFile']['value']      = '/var/lib/mongodb/mongodb.key'
```

As opposed to a basic key:value hash the inner hash is used to allow hooks for more complicated cookbooks.
This allows the adding of comments, weights, disable flags, etcetera.
Supporting the 'value' and 'comment' inner hash keys is strongly recommended.
Use of any other keys is left to the discretion of the author.

The following is a more complicated template that mixes explicit and hash options:

```ruby
#  
# CHEF MANAGED FILE: DO NOT EDIT
# Controlling Cookbook: rackspace_contributing_example
#

<%
# Generated using a config hash methodology
# Each setting is a key in the node['rackspace_contributing_example']['config']['thing.conf'] hash
#
# The key of the config hash is the option name.
#   The supported keys of the value are
#      'comment': An optional comment
#      'value': The value for the option

# Duplicate the hash so we can remove keys we explicitly call
# This is required to prevent modifying the main node data
my_conf = node['rackspace_contributing_example']['config']['thing.conf'].dup
-%>

# Explicitly use an option
SpecialArg1: <%= my_conf['SpecialArg1']['value'] -%>
<% my_conf.delete('SpecialArg1') # Delete the key from the hash -%>

# Explicitly use another option
SpecialArg2: <%= my_conf['SpecialArg2']['value'] -%>
<% my_conf.delete('SpecialArg2') # Delete the key from the hash -%>

# Explicitly use  a third option
SpecialArg3: <%= my_conf['SpecialArg3']['value'] -%>
<% my_conf.delete('SpecialArg3') # Delete the key from the hash -%>

#
# Dynamically added values
#

<%
# As we've deleted the used keys, we can now roll over the remaining keys as before:
my_conf.each do |key, value|
  if value.key?('comment')
-%>
# <%= key -%>: <%= value['comment'] %>
<% end -%>
<%= key -%> = <%= value['value'] %>
<% end -%>
```

That is a simplified example, ideally the explicitly used keys would still read comments and handle other options implemented.
It is also possible, for templates where the author objects to dynamic options, to use the method above but comment out the option with a big warning, or even call fail to abort the run.

See [rackspace-cookbooks/rackspace_sysctl](https://github.com/rackspace-cookbooks/rackspace_sysctl) for a direct example usage or [rackspace-cookbooks/rackspace_iptables](https://github.com/rackspace-cookbooks/rackspace_iptables) for a more abstracted use.

### Template Provider Calls
Template provider calls should always include the `cookbook` attribute as to allow for easily overriding from a wrapper cookbooks. The default should always be the cookbook itself.

i.e. in a recipe:

```ruby   
template "/etc/sudoers" do
  cookbook node['rackspace_sudo']['templates_cookbook']['sudoers']
  source "sudoers.erb"
  mode 0440
  owner "root"
  group "root"
end
```

i.e in attributes:

```ruby
   default['rackspace_sudo']['templates_cookbook']['sudoers'] = "rackspace_sudo"
```

## metadata.rb
* All dependencies should be listed with the pessimistic operation ("~> ") to the minor version
 * i.e. depends "rackspace_apt", "~> 1.2" 
 

#Misc
## GIT Tags
* Version increases should have an accompanying git tag. Jenkins will be responsible for ensuring this is complete.

## Licensing
* All Cookbooks must be Apache 2.0 licensed. 
* Include a `LICENSE` file in the top level directory of the cookbook with the Apache 2.0 Official license
* Include a `License and Authors` section of the `README.md`. See example at [rackspace-user](https://github.com/rackspace-cookbooks/rackspace-user)
* If you've forked the cookbook from another repo, please add notes attributing the original work to that repo

## README.md / Documentation
* Please include a README.md file in the cookbook root directory.
* Please include Descriptions, Platform support, notes, notes on recipes, attributes, CONTRIBUTING and testing specifications.
* If the cookbook is a fork, please credit original cookbook authors.

## CHANGELOG.md
* Please include a `CHANGELOG.md` in the cookbook root directory
* Please include brief notes about your changes and your name in each pull request


# Testing specifications
* Added features should include additional tests. Pull requests without tests may be denied.
* Workflow: `Jenkins -> rake -> knife -> foodcritic -> chefspec -> kitchen -> Jenkins -> Thor (git tag)`

## CI
* CI will be performed via Jenkins at jenkins.rackops.org on any pull requests.

## test-kitchen support
* test-kitchen 1.x support is required for all cookbooks. Please see [test-kitchen](https://github.com/opscode/test-kitchen) for more details.
* test-kitchen should include platforms section:
 

```ruby
---
driver_plugin: vagrant
driver_config:
  require_chef_omnibus: latest

platforms:
- name: ubuntu-12.04
  driver_config:
    box: opscode-ubuntu-12.04
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-12.04_chef-provisionerless.box
  run_list:
  - recipe[rackspace_apt]
- name: centos-6.4
  driver_config:
    box: opscode-centos-6.4
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.4_chef-provisionerless.box
- name: debian-7.2.0
  driver_config:
    box: opscode-debian-7.2.0
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_debian-7.2.0_chef-provisionerless.box
  run_list:
  - recipe[rackspace_apt]
```

### test-kitchen structure
* Tests should be called and have any needed attributes set in a .kitchen.yml file with separate suites as appropriate. We will append our own .kitchen.local.yml during the jenkins run.

## Gemfile
* standard Gemfile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Gemfile)

## Rakefile
* standard Rakefile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Rakefile)

## Thor
* Standard Thorfile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Thorfile)

## Rubocop
* standard .rubocop.yml is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/.rubocop.yml)
* Disabling cops in code is allowed where the style recommendation results in code which is more difficult to read, is messier than the original, or is otherwise arguable as an anti-pattern.  Disabled cops must have comments documenting why they are disabled and disable target code blocks, not a whole file.  See [the Rubocop docs](https://github.com/bbatsov/rubocop#disabling-cops-within-source-code) for details on in-code disables.

### Chefspec
* All Chefspec tests should be located in `spec` within the parent cookbook
* All in memory testing. Isolated, independent, atomic.
* LWRPs and libraries need additional unit tests 

### Functional tests (test-kitchen)
* Tests should be handled as a directory under `/test/integration/$suitename`. This is defined [here](http://kitchen.ci/docs/getting-started/writing-test) whereby the $suitename matches the suite defined in .kitchen.yml

## Vagrantfile 
* Not required but nice to have :)

# Code Review
* All code additions and pull requests are subject to the following:
    * Automated CI run must pass. Successes and Failure feedback will be provided in the pull request.
    * An admin must review the code prior to being merged 

# Guidelines
* TODO: add notes regarding guidelines for advanced cookbooks (libraries, LWRPS, etc)

