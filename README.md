# knapsack_pro ruby gem

[![Circle CI](https://circleci.com/gh/KnapsackPro/knapsack_pro-ruby.svg)](https://circleci.com/gh/KnapsackPro/knapsack_pro-ruby)
[![Gem Version](https://badge.fury.io/rb/knapsack_pro.svg)](https://rubygems.org/gems/knapsack_pro)
[![Code Climate](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby/badges/gpa.svg)](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby)
[![Test Coverage](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby/badges/coverage.svg)](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby)

Knapsack Pro gem splits tests across CI nodes and makes sure that tests will run comparable time on each node. It uses [KnapsackPro.com API](http://docs.knapsackpro.com). Original idea came from [knapsack](https://github.com/ArturT/knapsack) gem.

The knapsack_pro gem supports:

* [RSpec](http://rspec.info)
* [Cucumber](https://cucumber.io)
* [Minitest](http://docs.seattlerb.org/minitest/)
* [Turnip](https://github.com/jnicklas/turnip)

# Requirements

* >= Ruby 2.0

# Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Update gem](#update-gem)
- [Installation](#installation)
- [Usage](#usage)
  - [Step for RSpec](#step-for-rspec)
  - [Step for Cucumber](#step-for-cucumber)
  - [Step for Minitest](#step-for-minitest)
  - [Custom configuration](#custom-configuration)
- [Setup your CI server](#setup-your-ci-server)
  - [Info about ENV variables](#info-about-env-variables)
    - [Repository adapter](#repository-adapter)
    - [Environment variables for debugging gem](#environment-variables-for-debugging-gem)
  - [Passing arguments to rake task](#passing-arguments-to-rake-task)
    - [Passing arguments to rspec](#passing-arguments-to-rspec)
    - [Passing arguments to cucumber](#passing-arguments-to-cucumber)
    - [Passing arguments to minitest](#passing-arguments-to-minitest)
  - [Knapsack Pro binary](#knapsack-pro-binary)
  - [Supported CI providers](#supported-ci-providers)
    - [Info for CircleCI users](#info-for-circleci-users)
    - [Info for Travis users](#info-for-travis-users)
    - [Info for semaphoreapp.com users](#info-for-semaphoreappcom-users)
    - [Info for buildkite.com users](#info-for-buildkitecom-users)
- [Gem tests](#gem-tests)
  - [Spec](#spec)
- [Contributing](#contributing)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Update gem

Please check [changelog](CHANGELOG.md) before update gem. Knapsack Pro follows [semantic versioning](http://semver.org).

## Installation

Add those lines to your application's Gemfile:

```ruby
group :test, :development do
  gem 'knapsack_pro'
end
```

And then execute:

    $ bundle


Add this line at the bottom of `Rakefile` if your project has it:

```ruby
KnapsackPro.load_tasks if defined?(KnapsackPro)
```

## Usage

You can find here example of rails app with already configured knapsack_pro.

https://github.com/KnapsackPro/rails-app-with-knapsack_pro

### Step for RSpec

Add at the beginning of your `spec_helper.rb`:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

KnapsackPro::Adapters::RSpecAdapter.bind
```

### Step for Cucumber

Create file `features/support/knapsack_pro.rb` and add there:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

KnapsackPro::Adapters::CucumberAdapter.bind
```

### Step for Minitest

Add at the beginning of your `test_helper.rb`:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

knapsack_pro_adapter = KnapsackPro::Adapters::MinitestAdapter.bind
knapsack_pro_adapter.set_test_helper_path(__FILE__)
```

### Custom configuration

You can change default Knapsack Pro configuration for RSpec, Cucumber or Minitest tests. Here are examples what you can do. Put below configuration instead of `CUSTOM_CONFIG_GOES_HERE`.

```ruby
# you can use your own logger
require 'logger'
KnapsackPro.logger = Logger.new(STDOUT)
KnapsackPro.logger.level = Logger::INFO
```

## Setup your CI server

Set one or a few tokens depend on how many test suites you run on CI server.

`KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` - as value set token for rspec test suite. Token can be generated when you sign in to [knapsackpro.com](http://www.knapsackpro.com).
`KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER` - token for cucumber test suite.
`KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST` - token for minitest test suite.

On your CI server run this command for the first CI node. Update `KNAPSACK_PRO_CI_NODE_INDEX` for the next one.

    # Step for RSpec
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:minitest

You can add `KNAPSACK_PRO_TEST_FILE_PATTERN` if your tests are not in default directory. For instance:

    # Step for RSpec
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_specs/**/*_spec.rb" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_features/**/*.feature" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_tests/**/*_test.rb" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:minitest

### Info about ENV variables

By default knapsack_pro gem [supports a few CI providers](#supported-ci-providers) so you don't need to set environment variables.
In case when you use other CI provider for instance [Jenkins](https://jenkins-ci.org) etc then you need to provide configuration via below environment variables.

`KNAPSACK_PRO_CI_NODE_TOTAL` - total number CI nodes you have.

`KNAPSACK_PRO_CI_NODE_INDEX` - index of current CI node starts from 0. Second CI node should have `KNAPSACK_PRO_CI_NODE_INDEX=1`.

#### Repository adapter

`KNAPSACK_PRO_REPOSITORY_ADAPTER` - When it has value `git` then your local version of git on CI server will be used to get info about branch name, commit hash and project directory path.
By default this variable has no value so knapsack_pro will try to get those info from [supported CI](#supported-ci-providers) (CI providers have branch, commit, project directory stored as environment variables). In case when you use other CI provider like Jenkins then please set below variables on your own.

`KNAPSACK_PRO_BRANCH` - It's branch name. You run tests on this branch.

`KNAPSACK_PRO_COMMIT_HASH` - Commit hash. You run tests for this commit.

`KNAPSACK_PRO_PROJECT_DIR` - Path to the project on CI node for instance `/home/ubuntu/my-app-repository`. It should be main directory of your repository.

#### Environment variables for debugging gem

`KNAPSACK_PRO_ENDPOINT` - Default value is `http://api.knapsackpro.com` which is endpoint for [Knapsack Pro API](http://docs.knapsackpro.com).

`KNAPSACK_PRO_MODE` - Default value is `production`. When mode is `development` then endpoint is `http://api.knapsackpro.dev:3000`. When mode is `test` then endpoint is `http://api-staging.knapsackpro.com`.

### Passing arguments to rake task

#### Passing arguments to rspec

Knapsack Pro allows you to pass arguments through to rspec. For example if you want to run only specs that have the tag `focus`. If you do this with rspec directly it would look like:

    $ bundle exec rake rspec --tag focus

To do this with Knapsack Pro you simply add your rspec arguments as parameters to the knapsack_pro rake task.

    $ bundle exec rake "knapsack_pro:rspec[--tag focus]"

#### Passing arguments to cucumber

Add arguments to knapsack_pro cucumber task like this:

    $ bundle exec rake "knapsack_pro:cucumber[--name feature]"

#### Passing arguments to minitest

Add arguments to knapsack_pro minitest task like this:

    $ bundle exec rake "knapsack_pro:minitest[--arg_name value]"

For instance to run verbose tests:

    $ bundle exec rake "knapsack_pro:minitest[--verbose]"

### Knapsack Pro binary

You can install knapsack_pro globally and use binary. For instance:

    $ knapsack_pro rspec "--tag custom_tag_name --profile"
    $ knapsack_pro cucumber "--name feature"
    $ knapsack_pro minitest "--verbose --pride"

This is optional way of using knapsack_pro when you don't want to add it to `Gemfile`.

### Supported CI providers

#### Info for CircleCI users

If you are using circleci.com you can omit `KNAPSACK_PRO_CI_NODE_TOTAL` and `KNAPSACK_PRO_CI_NODE_INDEX`. Knapsack Pro will use `KNAPSACK_PRO_CIRCLE_NODE_TOTAL` and `KNAPSACK_PRO_CIRCLE_NODE_INDEX` provided by CircleCI.

Here is an example for test configuration in your `circleci.yml` file.

```yaml
test:
  override:
    # Step for RSpec
    - bundle exec rake knapsack_pro:rspec:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for Cucumber
    - bundle exec rake knapsack_pro:cucumber:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for Minitest
    - bundle exec rake knapsack_pro:minitest:
        parallel: true # Caution: there are 8 spaces indentation!
```

Please remember to add additional containers for your project in CircleCI settings.

#### Info for Travis users

You can parallel your builds across virtual machines with [travis matrix feature](http://docs.travis-ci.com/user/speeding-up-the-build/#Parallelizing-your-builds-across-virtual-machines). Edit `.travis.yml`

```yaml
script:
  # Step for RSpec
  - "bundle exec rake knapsack_pro:rspec"

  # Step for Cucumber
  - "bundle exec rake knapsack_pro:cucumber"

  # Step for Minitest
  - "bundle exec rake knapsack_pro:minitest"

env:
  - KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0
  - KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=1
```

If you want to have some global ENVs and matrix of ENVs then do it like this:

```yaml
script:
  # Step for RSpec
  - "bundle exec rake knapsack_pro:rspec"

  # Step for Cucumber
  - "bundle exec rake knapsack_pro:cucumber"

  # Step for Minitest
  - "bundle exec rake knapsack_pro:minitest"

env:
  global:
    - RAILS_ENV=test
    - MY_GLOBAL_VAR=123
    - KNAPSACK_PRO_CI_NODE_TOTAL=2
  matrix:
    - KNAPSACK_PRO_CI_NODE_INDEX=0
    - KNAPSACK_PRO_CI_NODE_INDEX=1
```

Such configuration will generate matrix with 2 following ENV rows:

    KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 RAILS_ENV=test MY_GLOBAL_VAR=123
    KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=1 RAILS_ENV=test MY_GLOBAL_VAR=123

More info about global and matrix ENV configuration in [travis docs](http://docs.travis-ci.com/user/build-configuration/#Environment-variables).

#### Info for semaphoreapp.com users

Knapsack Pro supports semaphoreapp ENVs `SEMAPHORE_THREAD_COUNT` and `SEMAPHORE_CURRENT_THREAD`. The only thing you need to do is set up knapsack_pro rspec/cucumber/minitest command for as many threads as you need. Here is an example:

    # Thread 1
    ## Step for RSpec
    bundle exec rake knapsack_pro:rspec
    ## Step for Cucumber
    bundle exec rake knapsack_pro:cucumber
    ## Step for Minitest
    bundle exec rake knapsack_pro:minitest

    # Thread 2
    ## Step for RSpec
    bundle exec rake knapsack_pro:rspec
    ## Step for Cucumber
    bundle exec rake knapsack_pro:cucumber
    ## Step for Minitest
    bundle exec rake knapsack_pro:minitest

Tests will be split across threads.

#### Info for buildkite.com users

Knapsack Pro supports buildkite ENVs `BUILDKITE_PARALLEL_JOB_COUNT` and `BUILDKITE_PARALLEL_JOB`. The only thing you need to do is to configure the parallelism parameter in your build step and run the appropiate command in your build

    # Step for RSpec
    bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    bundle exec rake knapsack_pro:minitest

## Gem tests

### Spec

To run specs for Knapsack Pro gem type:

    $ bundle exec rspec spec

## Contributing

1. Fork it ( https://github.com/ArturT/knapsack/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
