---
layout: post
title: "ActiveSkin - Flat skin for active admin"
date: 2015-01-19 09:25:41 +0530
comments: true
categories: 
- Ruby On Rails
- ActiveAdmin
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		<a href="https://github.com/activeadmin/activeadmin">Active Admin</a> is a Ruby on Rails plugin for generating administration style interfaces. It abstracts common business application patterns to make it simple for developers to implement beautiful and elegant interfaces with very little effort.
		<br/>
		<br/>
		Default interface of active admin:
		{% img /images/aa.png 800 250  %}
		<br/>
		We can customize the default style of active admin by over riding css values and and sass variables.
<a href="https://github.com/rstgroup/active_skin">ActiveSkin</a> is a flat skin for active admin. We can even change basic colors of the theme by changing values of some variables.
<br/>
<br/>
{% img /images/active-skin-edit.png 390 230  %}
{% img /images/active-skin-menu.png 390 230 %}
<strong>Installation</strong>
<br/>
Having active admin installed add the following line to your application's Gemfile:
{%codeblock%}
gem 'active_skin'
{%endcodeblock%}
Now we need to include active skin css to our project.
<br/>
Add following code to <strong>active_admin.css.scss</strong><br/>
{%codeblock%}
@import "active_admin/mixins";
@import "active_admin/base";
...
@import "active_skin";
...
{%endcodeblock%}
Change logo by setting the $skinLogo variable above active_skin import line in active_admin.css.scss.
{%codeblock%}
$skinLogo: url("admin_logo.png") no-repeat 0 0;
{%endcodeblock%}
You can even change basic colors of the theme by placing some other variables:
{%codeblock%}
$skinActiveColor: #001cee;
$skinHeaderBck: #002744;
$panelHeaderBck: #002744;

@import "active_skin";
{%endcodeblock%}
<strong>Color examples</strong>

<br/>
{% img /images/color1.png 390 230  %}
{% img /images/color3.png 390 230  %}
{% img /images/color4.png 790 250  %}
	</div>
</div>