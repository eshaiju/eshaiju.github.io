---
layout: post
title: "Travis Ci integration - Step by step tutorial"
date: 2015-08-09 21:00:06 +0530
comments: true
categories: 
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	<a href="https://travis-ci.org/">Travis-CI</a> is a open source, free and hosted continuous integration service that could build and run the unit tests for you, on every commit to a GitHub repository. It supports projects in a multitude of languages. The service allows you to configure steps that would be run before, after and during the build and test process, with just one YAMLfile (named .travis.yml) in the root of your repo. Apart from running the many unit tests in the project and letting you know how your latest check-in affected the health of the project, the Travis-CI service could also do custom build tasks like a post-build deployment. Testing your open source project is free,to integrate Travis Ci in private project we have to purchace suitable <a href="https://travis-ci.com/plans">plan </a>.
  <br/>
	Features created to help projects and teams
	<ul>
		<li>Watch your tests as they run</li>
		<li>Keep your config with your code</li>
		<li>Slack, HipChat, Emails and more</li>
		<li>A clean VM for every build</li>
		<li>Run your tests in parallel</li>
		<li>Linux and Mac (and iOS) supported</li>
		<li>Great API and command line tool</li>
  </ul>
  <strong>To get started with Travis CI: </strong>
  <br/>
  <ol>
	  <li>
	  	Sign in to Travis CI with your GitHub account, accepting the GitHub access permissions confirmation.
		</li>
		<li>
			Once you’re signed in, and Travis synchronized your repositories from GitHub, go to your profile page and enable Travis CI builds for your repository.  (Note: You can only enable Travis CI builds for repositories you have admin access to.)</li>
			{% img /images/Travis-CI-1.png 690 230  %}
		<li>
			Add .travis.yml to your project’s root folder
		</li>
		<li> 
			Add settings to .travis.yml 
		</li>
		{%codeblock%}
		language: ruby
rvm:
  - "2.2.0"
env:
  - DB=mysql
script: 
  - RAILS_ENV=test bundle exec rake db:migrate --trace
  - bundle exec rake db:test:prepare
  - bundle exec rspec spec/
before_script:
  - mysql -e 'create database myapp_test'
bundler_args: --binstubs=./bundler_stubs
		{%endcodeblock%}
		<li>
			Setup Database
MySQL on Travis CI is started on boot, binds to 127.0.0.1 and requires authentication. You can connect using the username “travis” or “root” and a blank password. Note that the “travis” user does not have full MySQL privileges that the “root” user does.</li>
		config/database.yml example for Ruby projects using ActiveRecord:
    {%codeblock%}
    test:
	  adapter: mysql2
	  database: myapp_test
	  username: travis
	  encoding: utf8

    {%endcodeblock%}
    <li>
    Commit this to github
(Note: Travis only runs a build on the commits you push after adding the repository to Travis.)
    </li>
    <li>
    	Wait for Travis CI to run a build on your repository, check the build status. (Travis CI sends you an email when build fails)
    </li>
    {% img /images/travis-ci-2.png 390 230  %}
{% img /images/travis-ci-3.png 325 230 %}
  </ol>
  Example of Travis Ci builds can be seen <a href="https://travis-ci.org/eshaiju/demo_app_with_travis_ci">here.</a>
	</div>
</div>