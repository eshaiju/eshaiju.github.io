---
layout: post
title: "'standard' for the order of associations, scopes, includes, validations in a Rails Model"
date: 2015-01-16 09:52:36 +0530
comments: true
categories: 
- Ruby On Rails
---


<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	  Rails is all about 'Convention over Configuration'. There are community-driven <a href="https://github.com/bbatsov/ruby-style-guide">Ruby coding style guides</a>. But there is no such convention of following a 'standard' for the order of arranging associations, scopes, includes, validations in a Rails Model. But we can create one for our project and be consistent with it in all the models. This is what I follow. Thanks to this <a href="http://stackoverflow.com/a/16967431/2045617">stackoverflow post</a>.
{%codeblock%}
class Model < ActiveRecord::Base   
  #all mixins
  include Something
  extend Something

  #constants 
  MAX_LIMIT = 10

  #other stuff
  acts_as_taggable
  paginates
  attr_accessor :something 
  before_create :some_method

	searchable do
	  text :something
	end

  #associations
  has_many :something
  belongs_to :something_else

  #validations
  validate_presence_of :something

  #scopes
  scope :something

  #instance methods
  def instance_method
  end

  #class methods
  def self.method
  end

  #private methods
  private
  def method2
  end
end
{%endcodeblock%}
	</div>
</div>