---
layout: post
title: "Two ways to Avoid Cross-origin resource sharing issue in development environment"
date: 2015-01-22 16:39:22 +0530
comments: true
categories: 
---


<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		When you are developing a Front End Application using Backbone.js, Angular.js or any other MV* frameworks or making an AJAX call, you might have faced the issue with <strong>Cross-origin resource sharing (CORS)</strong>. You will face this issue if you are accessing the hosted web services (API) from your development environment because the domains are different.
<br/>
<br/>
We can solve the CORS issue in different ways. The best solution is to implement reverse proxy in the development environment. Here is a sample virtual host file which has reverse proxy implemented:
{%codeblock%}
<VirtualHost *:80> 
    ServerName front_end_app.eshaiju-desktop.com 
    DocumentRoot /home/eshaiju/Projects/front_end_app/public 
    <Directory /home/eshaiju/Projects/front_end_app/public> 
       AllowOverride all    
       Options -MultiViews  
    </Directory> 
       
    LoadModule proxy_module modules/mod_proxy.so 
    ProxyRequests Off 
  
    <Proxy *> 
      Order deny,allow 
      Allow from all 
    </Proxy> 
  
    ProxyPass /api http://eshaiju.in/api 
    ProxyPassReverse /api http://eshaiju.in/api 
             
ErrorLog /var/log/apache2/error.log 
               
# Possible values include: debug, info, notice, warn, error, crit, 
# alert, emerg. 
LogLevel warn 
     
CustomLog /var/log/apache2/access.log combined 
</VirtualHost>
{%endcodeblock%}
From your app if you start accessing the <strong>/api</strong> URL it will be accessed from the proxy URL. 
<br/>
<br/>
There is anohter simple way to avoid this security check in developement environment. Disable cross-domain security check for AJAX call in Google Chrome for deveopment environment.
<br/>
Start Google Chrome with no security from command line, OSX:
<br/>
{%codeblock%}
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --disable-web-security
{%endcodeblock%}
Start Google Chrome from command line, Ubuntu/Linux:
{%codeblock%}
google-chrome --disable-web-security
{%endcodeblock%}
Start Chromium from command line, Ubuntu/Linux:
{%codeblock%}
chromium-browser --disable-web-security
{%endcodeblock%}

For Windows:
<br/>
1 - Create a shortcut to Chrome on your desktop. 
<br/> 
2 - Right-click on the shortcut and choose Properties, then switch to “Shortcut” tab. In the “Target” field, append the following:  <strong>–args –disable-web-security</strong>.
<br/>
3 - Restart Chrome
<br/>
<br/>
After this chrome will open with all securty disabled. So AJAX call will work without any problem. 
 
	</div>
</div>