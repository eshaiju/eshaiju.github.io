---
layout: post
title: "Apipie-rails - Rails API documentation tool"
date: 2015-01-09 15:07:29 +0530
comments: true
categories: 
- Ruby On Rails
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		<a href="https://github.com/Apipie/apipie-rails">Apipie-rails</a> is a DSL and Rails engine for documenting your RESTful API. Instead of the traditional use of <strong>#comments</strong>, Apipie lets you describe the code, through the code. With Apipie you can specify the methods available to your API, describe every possible parameter. Apipie using Ruby syntax so no need to learn yet another syntax. The documentation is available from within your app (by default under the /apipie path.)
		<br/>
		<br/>
		<strong>
			Getting started
		</strong>
    <br/>
    <br/>
    Add Apipie to Gemfile
    {%codeblock%}
      gem 'apipie-rails'
    {%endcodeblock%}
  
  after bundle install, initialise Apipie
  
  {%codeblock%}
    rails g apipie:install
  {%endcodeblock%}

  Now confirgure the basic settings in file config/initializers/apipie.rb
{%codeblock%} 
Apipie.configure do |config|
  config.app_name                = "DemoApp"
  config.api_base_url            = "/api/v1"
  config.doc_base_url            = "/apidoc"
  # were is your API defined?
  config.api_controllers_matcher = "#{Rails.root}/app/controllers/api/v1/*.rb"
  config.app_info = "DemoApp REST API "
end
{%endcodeblock%} 

Now you can start documenting your resources and actions
<br/>
<br/>
<strong>Resource Description:</strong>
<br/>
You can describe a resource on the controller level. The description is introduced by calling <strong>resource_description do ... end</strong>.
<br/>

The following are some of keywords available (all are optional):
<br/>
<strong>resource_id</strong> - How the resource will be referenced in Apipie (paths, see command etc.); by default controller_name.downcase is used.
<br/>
<strong>name </strong> - Human readable name of resource. By default class.name.humanize is used.
<br/>
<strong>short </strong>- Short description of the resource (it's shown on both the list of resources, and resource details)
<br/>
Example is given below : 
{%codeblock%}
  resource_description do
    short "API for managing User Profile"
end
{%endcodeblock%}
{% img /images/API-documentation3.jpg 800 250  %}
To see more options refer <a href = "https://github.com/Apipie/apipie-rails#dsl-reference">here </a>
<br/>
<br>
<strong>
	Method Description
</strong>

<br/>
This using to describe methods available to your API.
<br/>
<strong>api</strong> - Describe how this method is exposed, and provide a short description. The first parameter is HTTP method (one of :GET/:POST/:PUT/:DELETE). The second parameter is the relative URL path which is mapped to this method. The last parameter is the methods short description. 
<br/>
{%codeblock%}
  api :GET, '/user', "Fetch User Profile"
{%endcodeblock%}

<strong>param</strong> - Look at Parameter description section for details.

{%codeblock%}
	param :user, Hash, :description => "User" do
  param :name, String, :desc => "Name", :required => true
  param :email, String, :desc => "Email", :required => false
  param :gender, String, :desc => "Gender (1: Male, 2: Female)", :required => true
  param :photo, ActionDispatch::Http::UploadedFile, :desc => "User Image", :required => false
  param :address, String, :desc => "Address", :required => false
end
{%endcodeblock%}
<strong>formats</strong> - Method level request / response formats.
{%codeblock%}
  formats ['json']
{%endcodeblock%}
<strong>error</strong> - Describe each possible error that can happen while calling this method. HTTP response code and description can be provided.
{%codeblock%}
	error :code => 401, :desc => "Unauthorized"
error :code => 404, :desc => "Not Found", :meta => {:anything => "you can think of"}
{%endcodeblock%}

<strong>description</strong> -Full method description, which will be converted into HTML by the chosen markup language processor.
{%codeblock%}
	description <<-EOS
	== Books
	This API is used to fetch all the books.
EOS
{%endcodeblock%}

<strong>example</strong> - Provide an example of the server response; whole communication or response type. It will be formatted as code.
<br>
To see more options refer <a href="https://github.com/Apipie/apipie-rails#method-description">here</a>

<br/>
<br/>
Example of a user controller with api documentation  is given below
{%codeblock%}
class Api::V1::UsersController < Api::ApiController

  resource_description do
    short "API for managing User Profile"
  end

  api :GET, '/user', "Fetch User Profile"
  description <<-EOS
    == Fetch User Profile
     This API is used to fetch the user profile details.
    === Authentication required
     Authentication token has to be passed as part of the request. It can be passed as parameter(auth_token) or HTTP header(AUTH-TOKEN). 
  EOS
  formats ['json']
  error :code => 404, :desc => "User Profile not yet created"

  def show
    if @user.nil?
      render :status => :not_found, :json => {:message => "User Profile not yet created"}
    else
      render :status => :ok, :json => {:user=>@user}
    end
  end


  api :PUT, '/user', "Update User Profile"
  description <<-EOS
    == Update User Profile
     This API is used to update the user profile details.
    === Authentication required
     Authentication token has to be passed as part of the request. It can be passed as parameter(auth_token) or HTTP header(AUTH-TOKEN). 
  EOS
  error :code => 406, :desc => "User Profile not yet created"
  formats ['json']
  param :user, Hash, :description => "User" do
    param :name, String, :desc => "Name", :required => true
    param :email, String, :desc => "Email", :required => false
    param :gender, String, :desc => "Gender (1: Male, 2: Female)", :required => true
    param :photo, ActionDispatch::Http::UploadedFile, :desc => "User Image", :required => false
    param :address, String, :desc => "Address", :required => false
  end

  def user_profile
    if @user.nil?
      render :status => :not_acceptable, :json => {:message => "User not exists"}
    else
      @user.update_attributes(users_params)
      @user.save!(:validate=>false)
      render :status => :ok, :json => {:user =>@user,:message => "User updated"}
    end
  end

  private

  def users_params
    params.require(:user).permit(:name, :email, :gender,  :photo,:address)
  end
end
{%endcodeblock%}

{% img /images/API-documentation.jpg 800 250  %}
{% img /images/API-documentation-2.jpg 800 250  %}
<br/>
we can add example data in <strong>doc/apipie_examples.yml</strong>
{%codeblock%}
users#show:
- verb: :GET
  path: /api/v1/user
  query:
  code: 200
  show_in_doc: 1
  recorded: false
  request_data:
  response_data:
    "user":
        "id": 3
        "email": "eshaiju@gmail.com"
        "name": "Shaiju E"
        "gender": "male"
        "photo_path": "http://localhost:3000/system/users/photos/000/000/003/medium/10297568_10152483241776742_8973517135655971471_s.jpg?1403523880"
        "address": ""
{%endcodeblock%}
	</div>
</div>