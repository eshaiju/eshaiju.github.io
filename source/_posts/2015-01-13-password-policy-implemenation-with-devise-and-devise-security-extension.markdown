---
layout: post
title: "Password policy implementation with Devise and Devise Security Extension"
date: 2015-01-13 14:31:48 +0530
comments: true
categories: 
- Ruby On Rails
---


<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	 One of my recent project was in banking domain. So I had to implement password policies, for that I used <a href="https://github.com/plataformatec/devise">devise </a> gem.
	 <br/>
	 <br/>
	  <strong>Password policies :</strong><br/>
	  1 - Enforce password history - 5 password should be remembered<br/>
		2 - maximum password age - 30 days<br/>
		3 - minimum password length - 10 letters  <br/>
		4 - password must meet complexity requirements - Should be a combination of letters, numbers and symbols<br/>
		5 - Account lockout threshold -5 <br/>
		6 - Account lockout duration - 30 minutes<br/>
		7 - Email validation - Accept only emails of allowed set of domains <br/><br/>
		Most of the requirents mentioned above are achivable with simple cofigurations in Devise initializer. But to implement Password expirable, Password archivable and password complexity requirements check I used  security extension <a href="https://github.com/phatworx/devise_security_extension"> devise_security_extension</a>.
		<br/><br/>
		In this post I assume that we already had devise setup in our project. Now we need to add <strong>devise_security_extension </strong> in to our project.
{%codeblock%}
	gem 'devise_security_extension'
{%endcodeblock%}
After you installed Devise Security Extension you need to run the generator:
{%codeblock%}
	rails generate devise_security_extension:install
{%endcodeblock%}
The generator will inject the available configuration options into the existing Devise initializer.When you are done, you are ready to add Devise Security Extension modules on top of Devise modules to any of your Devise models.
{%codeblock%}
	devise :password_expirable, :password_archivable, :expirable, :lockable
{%endcodeblock%}
I added this in <strong>User</strong> model
{%codeblock%}
	class User < ActiveRecord::Base
	  devise :database_authenticatable, :registerable, :confirmable,
	         :recoverable, :rememberable, :trackable, :validatable,
	         :password_expirable, :password_archivable, :expirable, :lockable
end
{%endcodeblock%}
<strong>1 - Enforce password history</strong>
<br/>
Uncomment configuration password_archiving_count and deny_old_passwords in Devise initializer and also add a migration creating <strong>old_passwords</strong> tables.
{%codeblock%}

Devise.setup do |config|
  # How often save old passwords in archive
   config.password_archiving_count = 5

  # Deny old password (true, false, count)
   config.deny_old_passwords = true
end
{%endcodeblock%}

{%codeblock%}
create_table :old_passwords do |t|
  t.string :encrypted_password, :null => false
  t.string :password_salt
  t.string :password_archivable_type, :null => false
  t.integer :password_archivable_id, :null => false
  t.datetime :created_at
end
add_index :old_passwords, [:password_archivable_type, :password_archivable_id], :name => :index_password_archivable
{%endcodeblock%}

<strong>2 - maximum password age - 30 days</strong>
<br/>
Uncomment configuration expire_password_after and change to 1.months and also add a migration to store .
{%codeblock%}
  # config.expire_password_after = 1.months
{%endcodeblock%}
{%codeblock%}
create_table :the_resources do |t|
  # other devise fields
  t.datetime :password_changed_at
end
add_index :the_resources, :password_changed_at
{%endcodeblock%}
Replace our devise model with <strong>the_resources</strong>.
{%codeblock%}
create_table :users do |t|
  # other devise fields
  t.datetime :password_changed_at
end
add_index :users, :password_changed_at
{%endcodeblock%}
<strong>3 - minimum password length - 10 letters</strong>  
{%codeblock%}
  # Range for password length.
config.password_length = 10..128
{%endcodeblock%}
<strong>4 - password must meet complexity requirements - Should be a combination of letters, numbers and symbols</strong>
   <br/>Add password_regex with Regular expression which satisfies our needs.
{%codeblock%}
   # Need 1 char of A-Z, a-z and 0-9 and special charactor
config.password_regex = /(?=.*[A-Z])(?=.*[a-z])(?=.*[0-9])(?=.*[\W])/
{%endcodeblock%}
<strong>5 - Account lockout threshold -5 </strong>
{%codeblock%}
  # Number of authentication tries before locking an account if lock_strategy
# is failed attempts.
config.maximum_attempts = 5
{%endcodeblock%}
<strong>6 - Account lockout duration - 30 minutes</strong>
{%codeblock%}
  # Time interval to unlock the account if :time is enabled as unlock_strategy.
config.unlock_in = 30.minutes
{%endcodeblock%}
<strong>7 - Email validation - Accept only emails of allowed set of domains</strong>
<br/>
  For this, I added a custom validator which will check the domain name of user entered an email.
{%codeblock%}
validate :presence_of_domain_in_email

def presence_of_domain_in_email
   email_domain = email.split("@").last.downcase
   allowed_domains = User.allowed_domains
   unless allowed_domains[email_domain]
       errors.add :email, "This email domain is not valid. "
   end
end
  
def self.allowed_domains
  {
      'gmail.com' =>'gmail.com',
      'example.com' => 'example.com',
      'example1.in' =>'example1.in'

  }
end
{%endcodeblock%}
	</div>
</div>