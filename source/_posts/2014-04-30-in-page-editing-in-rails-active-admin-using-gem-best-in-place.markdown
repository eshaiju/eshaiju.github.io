---
layout: post
title: "In Page Editing In 'Active Admin' - Using Gem 'Best In Place'"
date: 2014-04-30 18:20:30 +0530
comments: true
categories: 
- Ruby On Rails
- ActiveAdmin
---
<div class='post'>
<div dir="ltr" style="text-align: left;" trbidi="on">
<span style="font-weight: bold;">Best in place </span>is a j Query based Ajax plug in that can help us to add in place editing to our application that takes profit of Restful server-side controllers to allow users to edit stuff with no need of forms. Usage of this gem in rails application is well documented in <a href="https://github.com/bernat/best_in_place">github page</a>. You can checkout their live demo <a href="http://bipapp.heroku.com">here</a>. In this post I concentrating on how we can use best in place gem with is active admin pages.
<br/>
To add Best in Place to our app we first need to add its gem to our application's Gemfile and run bundle.
{%codeblock%}
gem 'best_in_place', github: 'bernat/best_in_place'
{%endcodeblock%}
Require best in place in active_admin.js.coffee and initialise it
{%codeblock%} 
#= require best_in_place
#= require best_in_place.purr

$(document).ready ->
  jQuery(".best_in_place").best_in_place()
{%endcodeblock%}

To make text field editable in active admin show page 
{%codeblock%}
row :name do |project|
 best_in_place project, :name, :type => :input,:path =>[:admin,project]
end
{%endcodeblock%}
To make select field editable
{%codeblock%}
row :status do |project|
  best_in_place project, :status, :type => :select,:path =>[:admin,project],:collection => Project.statuses.map{|status| [status,status]}
end
{%endcodeblock%}
Textarea can be make editable using following code
{%codeblock%}
row :status_description  do |project|
  best_in_place project, :status_description, :type => :textarea,:path =>[:admin,project]
end
{%endcodeblock%}
For Editable Datepicker with formated output use <span style="font-weight: bold;">display_with</span>
{%codeblock%}
row "Planned Start Date" do |project|
  best_in_place project, :planned_start_date , :type => :date ,:path =>[:admin,project],  :display_with => lambda { |v| v.try(:strftime,'%b %d, %Y') }
end
{%endcodeblock%}
</div>
</div>