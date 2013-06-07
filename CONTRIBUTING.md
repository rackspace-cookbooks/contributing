CONTRIBUTING
===========

# General
* The following document will serve as a guide on what and how to contribute to any cookbook within [rackspace-cookbooks](http://github.com/rackspace-cookbooks/).

## Licensing
* All Cookbooks must be Apache 2.0 licensed. 
* Include a `LICENSE` file in the top level directory of the cookbook with the Apache 2.0 Official license
* Include a `License and Authors` section of the `README.md` file with the following:
> Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0 
> Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.`

# Testing specifications

## CI
* All Cookbooks must be integrated with with a CI server.
    * Jenkins and TravisCI are two popular choices
* Added features should include additional tests. Pull requests without tests may be denied.

## test-kitchen support
* test-kitchen 1.0 support is required for all cookbooks. Please see [test-kitchen](https://github.com/opscode/test-kitchen) for more details.

### test-kitchen structure
* All test should be included in a `tests` subdirectory within the cookbook root directory.

## foodcritic
* TODO: Add notes regarding foodcritic

# Code Review
* All code additions and pull requests are subject to the following:
    * Automated CI run must pass. Successes and Failure feedback will be provided in the pull request.
    * An admin must review the code prior to being merged 

## Google Hangouts
* TODO: add details regarding code review sessions

# Guidelines
* TODO: add notes regarding guidelines for advanced cookbooks (libraries, LWRPS, etc)

