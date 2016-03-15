gitlab-installer
================

I've tweaked this from the original upstream to use http by default (to avoid self-signed cert issues w/ CI runner), and to use a private network IP rather than a port number on localhost. This version also installs Docker.

1. Install Vagrant & VirtualBox if you don't already have them
2. Run "vagrant up" from command line in directory you cloned this repo to
3. Have some coffee
4. Vagrant will ask want you to enter your password so that it can edit your /etc/hosts file. If you don't want to enter it, you can manually add "192.168.201.100 gitlab.local".
5. Open browser to http://gitlab.local
6. Log in as root / 5iveL!fe

Gitlab CI
=========

To set up a runner, you'll need to first set up a project in gitlab. Then, go to that project's settings, click Runners, and under "Specific Runners" find the registration token.

1. Edit project settings and put Lines:\s+(\d+.\d+\%) under Test Coverage Parsing
2. "vagrant ssh" from command line in the directory you cloned this repo to
3. Find the CI token of the project that you want to add a runner for, and paste it in to the command line below instead of "CI_TOKEN_GOES_HERE":
```
# docker run --add-host gitlab.local:192.168.201.100 -e CI_SERVER_URL=http://gitlab.local/ci -e REGISTRATION_TOKEN=CI_TOKEN_GOES_HERE -e GITLAB_SERVER_FQDN=gitlab.local bobey/docker-gitlab-ci-runner-php5.6
```
4. It'll take a little bit for docker to download and spin up, depending on your connection and system.

Once running, whenever you commit code to the project, a build will automatically kick off and run any tests defined in the .gitlab-ci.yml file of your project.

An example .gitlab-ci.yml file which runs phpunit against a folder called "tests":

```
.gitlab-ci.yml
before_script:
    - /usr/local/bin/composer self-update
    - composer install

phpunit:
  script:
    - vendor/bin/phpunit --coverage-text tests
```

I have a simple project set up w/ phpunit tests and gitlab-ci.yml file at https://github.com/snazzware/gitlab-ci-test

Project Settings: Runners
-------------------------
![ScreenShot](/screenshots/Screenshot%202016-03-14%2019.42.32.png)

Project Build List
------------------
![ScreenShot](/screenshots/Screenshot%202016-03-14%2019.45.35.png)

Original README.md below
========================

Easy Gitlab installer, targeting Ubuntu 14.04 LTS, on Vagrant or on metal.

Supported Vagrant providers:
 * Virtualbox
 * VMWare
 * Parallels
 * LXC

Usage
=====

Copy `gitlab.rb.example` to `gitlab.rb` and modify it with your preferences.
For complete template, please see https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/files/gitlab-config-template/gitlab.rb.template

In VM, Gitlab is available at https://127.0.0.1:8443/
In server install, Gitlab is available at https://127.0.0.1/

CI is integrated into Gitlab from version 8 onwards.


Releases
========

Check tags for releases matching GitLab releases.

From Gitlab version 7.1.1 onwards, it utilizes the omnibus package instead of executing
long setup scripts. If you wish to use setup scripts or Puppet/Chef, there are plenty of
other choises.

From Gitlab version 7.11.2 onwards, it uses packageserver with apt. Which means there is
little reason to change the installer anymore.


Gitlab CI
=========

Gitlab CI and Gitlab CI Runner scripts were split into separate repository. You can find them at:
https://github.com/tuminoid/gitlabci-installer

From 7.5.1 onwards, CI is also enabled in this repository as it is bundled with Gitlab.
To disable CI, comment out `ci_external_url` line in script.

From 8 onwards, CI is part of Gitlab. You need to comment out following lines in `gitlab.rb`
(if you reuse your old `gitlab.rb`):
```
# ci_external_url 'http://gitlabci.invalid/'
# nginx['redirect_http_to_https'] = false
```
