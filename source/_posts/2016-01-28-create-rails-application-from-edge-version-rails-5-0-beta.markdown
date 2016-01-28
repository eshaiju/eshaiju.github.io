---
layout: post
title: "Create Rails Application from Edge Version -- 5.0.0.beta1.1"
date: 2016-01-28 10:34:32 +0530
comments: true
categories: 
- Ruby On Rails
---
<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		<b>5.0.0.beta1.1</b> version is released which has some exciting stuffs like Action Cable, API mode, new Rails commands. To test all these new items we need to setup rails application from Edge Version. There are different ways to setup a rails project using Edge version. In this post Iam explaining how I setup a project in Rails 5.0.0.beta.
    <br/>
		First We need to create an application folder with the name of the application and add a Gemfile.
		{%codeblock %}
		mkdir app_name
cd app_name
touch Gemfile
{%endcodeblock%}
		and add the following into the Gemfile
		{%codeblock %}
		source 'https://rubygems.org'
ruby '2.2.3'

gem 'rails', :github => 'rails/rails'
gem 'arel', :github => 'rails/arel'
gem 'rack', :github =>  'rack/rack'
		{%endcodeblock %}
Rails 5 requires Ruby 2.2.2 or greater.
Setup the Ruby Environment to Ruby 2.2 or greater then do the bundle install 
{%codeblock %}
rvm use 2.2.3
bundle install 
{%endcodeblock %}
Generate Rails application using the following code
{%codeblock %}	
bundle exec rails new . --force --dev 
{%endcodeblock %}
The --force flag will allow Rails to overwrite our Gemfile, and the --dev flag tells Rails to point to the edge version of Rails that we just bundled.
<br/>
Thats it, now we will have a brand new Rails application with Edge Version.
	</div>
</div>