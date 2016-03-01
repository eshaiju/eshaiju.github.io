---
layout: post
title: "Profile API endpoints Using rack-mini-profiler"
date: 2016-02-25 13:54:28 +0530
comments: true
categories: 
- Ruby On Rails
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		<a href='https://github.com/MiniProfiler/rack-mini-profiler'>rack-mini-profiler</a> is a great tool for fine tuning our Ruby on Rails application. This gem will show all information about the queries in the web page we are loading. By checking this list, we can understand which part of our code takes the longest time and optimize that part of code.
		<br/>
	  <br/>
	  {% img /images/rack-mini-profiler.jpg 650 230  %}
	  <br/>
	  <br/>
		This is very useful to inspect the performance of HTML pages, but it does not provide any default ways to review the performance of API endpoints. After some searching, I found out that, by default, the rack-mini-profiler gem collects the previous JSON call and will show all previous requests in the next loading HTML page. Using this feature we can achieve profiling API endpoints.
		<br/>
		<br/>

		Following are the steps that I did to profile API request.
		<br/>
		<br/>
		1- Make an API request using Postman or another client.
		<br/>
		2- Reload an HTML page, I loaded 404.html page by hitting URL http://localhost:3000/404.html
		<br/>
		3- This web page will show all profile information of previous API call.

		<br/>
		<br/>
		{% img /images/api-rack-mini-profiler.png 650 230  %}
		<br/>
		<br/>
	</div>
</div>