---
layout: post
title: "Deploying Ruby On Rails Application To  Cloud Application Platform  - Openshift"
date: 2014-07-27 14:44:50 +0530
comments: true
categories:
- Ruby On Rails
- Deployment
- Openshift
---
<div class='post'>
<div dir="ltr" style="text-align: left;" trbidi="on">
<span style="font-weight: bold;">OpenShift</span> is a Platform as a Service (PaaS) from RedHat. It’s great for deploying web applications as you can setup/scale/manage apps quickly without any hassle, just like Heroku. It is open source and written in Ruby.
<br/>
To get started create a free Account. You get 3 small gears (resource container/unit : one small gear is equivalent to 512 MB RAM and 1GB storage) for free. Once you are signed up, install the OpenShift RHC Client Tools by running these commands in a terminal 
<br/>
{%codeblock %}
gem install rhc
rhc setup
{%endcodeblock%}
We can deploy rails application by adding OpenShift as a remote repo. 
<br/>
1- Create a new application in oppenshift account, then get the git URL for your new application. This was shown to you when you created your application,through the web console
<br/>
2- Add your OpenShift repo as a remote repo
{%codeblock %}
git remote add openshift <OpenShift repo URL>
{%endcodeblock%}

3- Configure Database
<br/>
Since your database address may change, you will need to access it using an environment variable. A random username and password was also generated for you when your application was created. But you don't need to hard code it into your application, there are environment variables for that too!
Add this configuration to database.yml file

{%codeblock %}
production:
  adapter: mysql2
  encoding: utf8
  database: <%=ENV['OPENSHIFT_APP_NAME']%>
  pool: 5
  host: <%=ENV['OPENSHIFT_MYSQL_DB_HOST']%>
  port: <%=ENV['OPENSHIFT_MYSQL_DB_PORT']%>
  username: <%=ENV['OPENSHIFT_MYSQL_DB_USERNAME']%>
  password: <%=ENV['OPENSHIFT_MYSQL_DB_PASSWORD']%>
  socket: <%=ENV['OPENSHIFT_MYSQL_DB_SOCKET']%>
{%endcodeblock%}



4- Now that your app is configured properly, it's time to deploy it to OpenShift. To do that, simply run a git push
{%codeblock %}
git push openshift master
{%endcodeblock%}


If everything went well, your app should deploy and be accessible. If not, pay attention to the output from the git push, if anything failed, it will tell you there.
<br/>
 If your application require some persistent directory for your data then you can use directory (app-root/data/) you can access this directory in your application using enviroment variable OPENSHIFT_DATA_DIR
<br/>
5- Managing apps :
<br/>
To ssh into your server, type :
{%codeblock %}
rhc ssh app-name
{%endcodeblock%}

Navigate to App folder:
{%codeblock %}
cd app-root/repo
{%endcodeblock%}

Migrate Database:
{%codeblock %}
bundle exec rake db:migrate RAILS_ENV="production"
{%endcodeblock%}

 If you want to associate your own domain name eg. (www.yourdomain.com) with your openshift rails app url then you will need to create url alias as shown below 
{%codeblock %}
rhc alias add railsapp www.yourdomain.com
{%endcodeblock%}
then change cname records in your DNS provider account.


</div>
</div>