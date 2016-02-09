---
layout: post
title: "Camaleon CMS - Wordpress Alternative built in Ruby on Rails 4"
date: 2016-02-08 18:01:20 +0530
comments: true
categories: 
- Ruby On Rails
---
<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	<a href="http://camaleon.tuzitio.com/">Camaleon CMS</a> is a free and Open source Content Management System developed on Ruby on Rails 4 which was released 6 months back. This CMS is an alternative to wordpress for Ruby on Rails developers to manage advanced contents easily. Within 6 months of release, this project has <b>713</b> stars , <b>114</b> forks in github and  also have <b>16</b> contributors. 
	 {% img /images/camaleon-cms.png 650 230  %}
  <br/><br/>
	Let’s have a look at its features.
	<br/>
	<strong>Plugins</strong>:<br/>
This CMS is coming with a collection of plugins by default and we can simply enable it in our site using admin pannel. They have a <a href="http://camaleon.tuzitio.com/store/plugins">plugin store</a> which currently contains 14 plugins including Analytics, Ecommerce, Contact form, Post reorder and a few others.
<br/>
<strong>Security</strong>:<br/>

Camaleon CMS protects your content from common attacks, such as Remote code execution, SQL injections, Advanced sessions security, Cross Site Scripting, Control of abusive requests, Cross-Site Request Forgery.
<br/>
<strong>Nice Design and UX</strong>:<br/>
Camaleon CMS looks pretty good and modern, comparing to other CMS options available in Ruby on Rails. Camaleon CMS permit you to adapt the CMS to all your needs and not you adapt to the CMS. that is you can create your custom architecture with all attributes that you need for each kind of content.
<br/>
<strong>Site Speed</strong>:<br/>
Camaleon CMS include a lot of cache strategies to optimize the site access velocity. Cache contents, Cache queries, Manifests (compress and join asset files), Customize your content visualization for Desktop, Mobile and Tablet are some of the strategies used in Camaleon CMS.
<br/><br/>
<strong>Installing Camaleon CMS</strong>
<br/>
You can install Camaleon-CMS with the following steps. 
<br/>
Create your rails project :
{%codeblock %}
rails new my_project
{%endcodeblock %}
Add the gem in your Gemfile :
{%codeblock %}
gem ‘camaleon_cms’
{%endcodeblock %}
Install the gem :
{%codeblock %}
bundle install
{%endcodeblock %}
Install the CMS (This will copy some basic templates and plugins in your project) :
{%codeblock %}
rails generate camaleon_cms:install
{%endcodeblock %}
Install required Gems for CMS and basic plugins
{%codeblock %}
bundle install
{%endcodeblock %}
Create database structure
{%codeblock %}
rake db:migrate
{%endcodeblock %}
Start your server
{%codeblock %}
rails server # start your server
{%endcodeblock %}
Open a browser and navigate to <a href='http://localhost:3000'>http://localhost:3000</a> to see the initial setup page.
	</div>
</div>