---
layout: post
title: C.I. with Github Actions
date: 2019-10-09 14:47 +0100
author: igbanam
category: blog
tag:
  - ci
  - github
headerImage: true
image: /assets/images/jekyll-logo-light-solid.png
---

Continuous Integration is key in the delivery of any software. Now, "continuous integration" has had many definitions over the years. Some say it has to do with bringing code from different branches, "integrating" them into a delivery beanch. Some day it's the introduction of automated tests into your pipeline. Others, as primitive as me, say it's CircleCI and TrravisCI. Wherever you fall on this spectrum, you may have the belief that continuous integration makes it easier to ship code.

There are many continuous integration tools. Most of these tools handle running tests for a particular changeset of your sourcecode. This helps you validate that the new changeset both meets its requirements (written in tests) and does not break the hitherto realized requirements of the system. Previously, I have depended on CircleCI to get this done. But with the recent Github Actions, one could do anything which could be done with a repo based on some Github events like push, and pull-request. With this new capability, I sought to wean myself from CircleCI and other CI pipelines. Since code is hosted on GitHub, I thought it best to localize the delivery of the code on Github.

### The Setup

  1. A new Hanami project. Hanami comes with RSpec as a default testing framework.
  2. [Github Actions](https://github.com/features/actions)

### Our CI

**Name**  
This defines the action, and creates a reference through your workflow

```yaml
name: Run Tha CI
```

**on**  
Actions run on specific events in the Github ecosystem. Push and Pull Request are two popular events Actions could be run on. For a more comprehensive list, read [Events that trigger workflows](https://help.github.com/en/articles/events-that-trigger-workflows)

We would want to "Run Tha CI" on every push event.

```yaml
on: [push]
```

This list is an array. If you want this workflow to trigger for more than one event, add the event to the array separated by commas. Another way of writing this could be

```yaml
on:
  - push
```

**jobs**

This bit defines the action itself. 

Actions is pretty new, and I don't know my way around it that well yet. But at this point, I like keeping my jobs to just one job in a workflow. It must be possible to have more than one job per workflow.

Here we define the job: giving it a name, and telling it the machine to run on. If you are familiar with Dockerfiles, this is akin to the FROM keyword in a Dockerfile which provides a base for your image to be run on. This "runs-on" attribute defines the machine (or set of machines) your jons would run on.

```yaml
jobs:
  run_tha_ci:
    name: build
    runs-on: ubuntu-latest
```

**services**

Now we have to define dependent services we would need to run the job. In this case, I need a database to run my tests.

```yaml
jobs
  # ... snipped for brevity ...
  services:
    database:
      images: postgres:11.5-alpine
      env:
        POSTGRES_USER: runner
        POSTGRSE_DB: project_test
        POSTGRES_PASSWORD: ''
      ports: [5432:5432]
```

Notice that the PostgreSQL user here is set to `runner`. I had originally set this to a random name since I thought the name of the DB user would be irrelevant. While testing, however, I realized Hanami required the DB user be "runner".

**steps**

These steps are commands run in your environment to carry out the job.

Here, we would like to

  1. Checkout the sourcecode
  2. Setup Ruby with version 2.6.3
  3. Install dependencies: libraries, and gems
  4. Setup our database
  5. Run the specs.

Thankfully, there are some actions which we would need to use over and over again. With Github Actions, you can abstract this into a repository and reference it anytime you need. A good example is the checkout action. This has been abstracted into the [actions/checkout](http://github.com/actions/checkout) repository.

```yaml
steps:
  - uses: actions/checkout@v1
  - name: Setup Ruby
    uses: actions/setup-ruby@v1
    with:
      ruby-version: 2.6.3
  - name: Install Dependencies
    run: |
      sudo apt-get update
      sudo apt-get -yqq install \
        postgresql \
        postgresql-contrib \
        libpq-dev \
        chromium-chromedriver
      gem install bundler
      bundle install --without development production
  - name: Setup Database
    env:
      HANAMI_ENV: test
      PGHOST: localhost
      PGUSER: runner
    run: |
      hanami db prepare
  - name: Run Specs
    run: bundle exec rspec
```

Notice two new keywords in this snippet `uses` and `run`

  - `uses` references an action in a repository
  - `run` defines commands to run in the box

**all together…**

With the Yaml file below, every push gets run these steps run against the changeset. If any step in your workflow returns with a non-zero code, that step is marked as a failed step. If any steps in your workflow fails, the action "fails".

With this, we have our continuous integration baked right within Github.

_p.s: Getting this up for Rails shouldn't be much different._


```yaml
name: Run Tha CI
on: [push]

jobs:
  run_tha_ci:
    name: build
    runs-on: ubuntu-latest

    services:
      database:
        image: postgres:11.5-alpine
        env:
          POSTGRES_USER: runner
          POSTGRSE_DB: catalyst_test
          POSTGRES_PASSWORD: ""
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v1
      - name: Setup Ruby
        uses: actions/setup-ruby@v1
        with:
          ruby-version: 2.6.3
      - name: Install Dependencies
        run: |
          sudo apt-get update
          sudo apt-get -yqq install \
            postgresql \
            postgresql-contrib \
            libpq-dev \
            chromium-chromedriver
          gem install bundler
          bundle install --without development production
      - name: Setup Database
        env:
          HANAMI_ENV: test
          PGHOST: localhost
          PGUSER: runner
        run: |
          hanami db prepare
      - name: Run Specs
        run: bundle exec rspec
```
