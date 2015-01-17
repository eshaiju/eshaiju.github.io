---
layout: post
title: "Check Test Coverage With SimpleCov Gem"
date: 2015-01-15 10:25:59 +0530
comments: true
categories: 
- Ruby On Rails
---


<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		<a href="https://github.com/colszowka/simplecov">SimpleCov</a> is a code coverage analysis tool for Ruby. It uses Ruby's built-in Coverage library to gather code coverage data, but makes processing its results much easier by providing a clean API to filter, group, merge, format, and display those results, giving you a complete code coverage suite that can be set up with just a couple lines of code.
		<br/><br/>
		<strong>Getting started</strong>
		<br/>
		Add SimpleCov to your Gemfile and bundle install:
		{%codeblock%}
			gem 'simplecov', :require => false, :group => :test
		{%endcodeblock%}
		Load and launch SimpleCov at the very top of your <strong>spec_helper.rb</strong>  (or test/test_helper.rb, cucumber env.rb, or whatever your preferred test framework uses):
{%codeblock%}
require 'simplecov'
	
Spork.each_run do
  if ENV['DRB']
    require 'simplecov'
    SimpleCov.start 'rails'  if ENV["COVERAGE"]
    end
end
{%endcodeblock%}
Due to this code <strong>SimpleCov.start if ENV["COVERAGE"]</strong> SimpleCov will only run if you execute your tests like this:
<br/>
{%codeblock%}
COVERAGE=true bundle exec rspec
{%endcodeblock%}
Run your tests, open up <strong>coverage/index.html</strong> in your browser you can see detailed report on test coverage.
{% img /images/simplecov1.png 800 250  %}
{% img /images/simplecov2.png 800 250  %}
	</div>
</div>