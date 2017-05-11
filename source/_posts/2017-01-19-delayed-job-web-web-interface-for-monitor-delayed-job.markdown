---
layout: post
title: "delayed_job_web - A resque inspired interface for delayed_job"
date: 2017-01-19 10:34:55 +0530
comments: true
categories:
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
  <a href="https://github.com/ejschmitt/delayed_job_web">delayed_job_web</a> is a gem which will provide the <a href="https://github.com/resque/resque">Resque</a> inspired interface for delayed_job. This gem will provide a nice interface for monitor all activities in delayed jobs instead of searching it in the database directly.
  <br/>
   <strong>Some features: </strong>
  <br/>
  <ul style="padding-left: 5%;">
    <li>Easily view jobs enqueued, working, pending, and failed.</li>
    <li>Queue any single job. or all pending jobs, to run immediately.</li>
    <li>Remove a failed job, or easily remove all failed jobs.</li>
    <li>Watch delayed_job operation with live ajax polling.</li>
    <li>Filter delayed_jobs by queue name.</li>
  </ul>
  <strong>Quick Start For Rails 5 Applications:</strong>
  <br/><br/>
  Add the dependency to your Gemfile and bundle it.

  {%codeblock%}
    gem 'delayed_job_web'

# For dependency resolution of 'delayed_job_web' gem
# More info - https://github.com/ejschmitt/delayed_job_web/issues/84
gem 'rack-protection', github: 'sinatra/sinatra'
gem 'sinatra', github: 'sinatra/sinatra'

  {%endcodeblock%}

  {% img /images/delayed_job_web.png 1200 260  %}
  <br/>
  Add the following route to your application for accessing the interface, and retrying failed jobs.
    {%codeblock%}
      get '/delayed_job' => DelayedJobWeb, :anchor => false
put '/delayed_job' => DelayedJobWeb, :anchor => false
post '/delayed_job' => DelayedJobWeb, :anchor => false
{%endcodeblock%}

You can authenticate web interface by adding something like this,

{%codeblock%}
  authenticate :user, ->(u) { u.super_admin? } do
  get '/delayed_job' => DelayedJobWeb, :anchor => false
  put '/delayed_job' => DelayedJobWeb, :anchor => false
  post '/delayed_job' => DelayedJobWeb, :anchor => false
end
{%endcodeblock%}
now web interface will be available only for super admin login.
  </div>
</div>
