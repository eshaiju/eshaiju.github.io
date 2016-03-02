---
layout: post
title: "Multiple language sites in Refinery CMS with i18n"
date: 2015-01-05 09:50:40 +0530
comments: true
categories:
- Refinery CMS
- Ruby On Rails
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		Refinery CMS is an Rails-based CMS that supports Rails 3.2 and 4.1. We can make Refinary CMS Multilingual with i18n Translations.
    <br/>
		
		For this add the gem to Gemfile
    <br/>
{%codeblock ruby %}
		gem 'refinerycms-i18n'
{%endcodeblock%}
		Run the generator <br/>
{%codeblock ruby %}
		rails g refinery:i18n
{%endcodeblock%}
		Change the language settings

		In Refinery's settings find I18n Translation Frontend Locales (Refinery) and add the ISO country name.
    <br/>
<strong>config/initializers/refinery/i18n.rb</strong>
{%codeblock ruby %}
     Refinery::I18n.configure do |config|
   config.default_locale = :en

  # config.current_locale = :en

  # config.default_frontend_locale = :en

   config.frontend_locales = [:en,:ar]

   config.locales = {:en=>"English", :ar=>"Arabic"}
   #, :nl=>"Nederlands", :pt=>"Português", :"pt-BR"=>"Português brasileiro", :da=>"Dansk", :nb=>"Norsk Bokmål", :sl=>"Slovenian", :es=>"Español", :it=>"Italiano", :de=>"Deutsch", :lv=>"Latviski", :ru=>"Русский", :sv=>"Svenska", :pl=>"Polski", :"zh-CN"=>"简体中文", :"zh-TW"=>"繁體中文", :el=>"Ελληνικά", :rs=>"Srpski", :cs=>"Česky", :sk=>"Slovenský", :ja=>"日本語", :bg=>"Български", :hu=>"Hungarian", :uk=>"Українська"}
end
{%endcodeblock%}
		That's it! Visit the Pages tab and you should see flags indicating the page language in the tree. 
<br/>
		{% img /images/1.png 600 250  %}
		<br/>

		Edit a page and you'll see the available languages at the top. Simply select one to add content for that language.
<br/>
{% img /images/2.png 600 250  %}
		{% img /images/3.png 600 250  %}
<br/>
Now we need to add multi language support for our extensions. For this we need to specify this when we create extension with <strong>--i18n </strong> attribute.

{%codeblock ruby %}
	rails g refinery:engine Service title:string description:text icon:image --i18n title description
{%endcodeblock%}

We can add link to toggle between languages in Frontend  web site using following code
{%codeblock%}
	<nav id='locale'>
	  <% ::Refinery::I18n.frontend_locales.each do |locale| %>
	    <%= link_to_if Globalize.locale.to_s != locale.to_s, locale, {:locale => locale} %>
	  <% end %>
</nav>
{%endcodeblock%}
	</div>
</div>