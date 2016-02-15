---
layout: post
title: "Rails 5 'belongs_to' assosiation default to required true"
date: 2016-02-15 17:46:29 +0530
comments: true
categories: 
- Ruby On Rails, Rails 5
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	5.0.0.beta2 version is released. One of the change I noticed is <strong>belongs_to</strong> assosiation by default required is true, that means if you create a object by setting foreign key to be nil it will trow a <strong>must exist</strong> validation error. This change will have some impact if you are migrating an existing rails 4 application in to rails 5 and foreign key is not mandatory in your application.
	<br/>
  For example, if your User model has city_id as forign key, then if you create user without adding city_id then rails 5 will trow validation error.
  {%codeblock user.rb%}
  class User < ApplicationRecord
  belongs_to :city
end

  {%endcodeblock%}

   {%codeblock%}
 2.3.0 :003 > user = User.create(name:'Shaiju')
   (0.3ms)  BEGIN
   (0.2ms)  ROLLBACK
 => #<User id: nil, name: "Shaiju", city_id: nil, created_at: nil, updated_at: nil> 
2.3.0 :004 > user.errors
 => #<ActiveModel::Errors:0xa8d9a00 @base=#<User id: nil, name: "Shaiju", city_id: nil, created_at: nil, updated_at: nil>, @messages={:city=>["must exist"]}, @details={:city=>[{:error=>:blank}]}> 

  {%endcodeblock%}
  If we want to turn of this behaviour we can use <strong>:optional => true </strong>. When set to true, the association will not have its presence validated.

  {%codeblock user.rb%}
  class User < ApplicationRecord
  belongs_to :city,optional: true 
end
  {%endcodeblock%}
  {%codeblock%}
2.3.0 :001 > user = User.create(name:'Shaiju')
   (0.3ms)  BEGIN
  SQL (25.2ms)  INSERT INTO `users` (`name`, `created_at`, `updated_at`) VALUES ('Shaiju', '2016-02-15 12:25:55', '2016-02-15 12:25:55')
   (81.6ms)  COMMIT
 => #<User id: 1, name: "Shaiju", city_id: nil, created_at: "2016-02-15 12:25:55", updated_at: "2016-02-15 12:25:55"> 
  {%endcodeblock%}
 you can turn off this in entire application by setting Rails.application. config.active_record.belongs_to_required_by_default = false in <strong>config/initializers/active_record_belongs_to_required_by_default.rb</strong> file.
 {%codeblock config/initializers/active_record_belongs_to_required_by_default.rb %}
 # Be sure to restart your server when you modify this file.

# Require `belongs_to` associations by default. This is a new Rails 5.0
# default, so it is introduced as a configuration option to ensure that apps
# made on earlier versions of Rails are not affected when upgrading.
Rails.application.config.active_record.belongs_to_required_by_default = false

 {%endcodeblock%}
	</div>
</div>