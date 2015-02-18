---
layout: post
title: "Rspec Run specific set of tests using --tag option"
date: 2015-02-18 00:04:23 +0530
comments: true
categories: 
- Ruby On Rails
- Rspec
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
In my current project I wanted to avoid running a few set of slow tests every time. Tagging feature in rspec allow us to filter the examples to be run by tag. <br/><br/>

We can use the --tag (or -t) option to filter the examples by tags. The tag can be a simple name or a name:value pair. In the first case,
examples with :name => true will be filtered. In the second case, examples with :name => value will be filtered, where value is always a string.
<br/><br/>
Tags can also be used to exclude examples by adding a ~ before the tag. For example ~tag will exclude all examples marked with :tag => true and 
~tag:value will exclude all examples marked with :tag => value.
<br/><br/>
Given example show how to add tags to spec examples,
{%codeblock %}
describe "group with tagged specs" do
  it "example I'm working now", :focus => true do; end
  it "special example with string", :type => 'special' do; end
  it "special example with symbol", :type => :special do; end
  it "slow example", :skip => true do; end
  it "ordinary example", :speed => 'slow' do; end
  it "untagged example" do; end
end
{%endcodeblock%}

To run only examples with a simple tag, for examlple to run tests with a  tag focus
<br/>
run "<strong>rspec spec/ --tag focus</strong>"
<br/>
   Then the output should contain all the tests with tag :focus=>true
<br/>
To exclude examples with a name:value tag
<br/>
 run "<strong>rspec spec/ --tag ~speed:slow</strong>"
 <br/>
  Then the tests with tag speed:slow" will not be run.

<br/><br/>
Detailed documenation is given <a href="http://www.relishapp.com/rspec/rspec-core/v/2-4/docs/command-line/tag-option">here</a>
<br/><br/>
Thats it, Happy Testing....
	</div>
</div>