---
layout: post
title: "Translate Active Record model name, attributes and error messages with Rails Internationalization (I18n) API"
date: 2015-01-25 18:17:05 +0530
comments: true
categories: 
- Ruby On Rails
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	The Ruby <strong>I18n</strong> gem which is shipped with Ruby on Rails provides an easy-to-use and extensible framework for translating your application to a single custom language other than English or for providing multi-language support in your application.
<br/>
<br/>
To translate model Project to another language (for example, to arabic), add model name in arabic with key <strong>one</strong> and pluralal of model name in arabic with key <strong>other</strong> in to <strong>config/locales/ar.yml</strong>.
<br/>
{%codeblock%}
ar:
  activerecord:
    models:
      project:
        one: مشروع
        other: مشاريع
      task:
	one: مهمة
        other: المهام	
    attributes:
      project: 
        title: لقب
		  # will translate Project attribute "title" as "لقب"
{%endcodeblock%}
that's it, now if you switch your application to Arabic you can see everywhere in the project is translated into <strong>مشروع</strong> and projects is translate into <strong>مشاريع</strong>. Project attribute title will be translated into <strong>لقبs</strong>.
<br/>
<br/>
Active Record validation error messages can also be translated easily. Active Record gives you a couple of namespaces where you can place your message translations in order to provide different messages and translation for certain models, attributes, and/or validations.
<br/><br/>
Consider a Project model with a validation for the title attribute like this:
{%codeblock%}
class Project < ActiveRecord::Base
  validates :title, presence: true
end
{%endcodeblock%}
The key for the error message in this case is <strong>:blank</strong>. So add message to be shown in following order to ar.yml
{%codeblock%}
activerecord:
    errors:
      models:
        project:
          attributes:
            title:
              blank : لا يمكن أن يكون البنك
{%endcodeblock%}
For more details check <a href="http://guides.rubyonrails.org/i18n.html#translations-for-active-record-models" >official rails guides</a>
	</div>
</div>