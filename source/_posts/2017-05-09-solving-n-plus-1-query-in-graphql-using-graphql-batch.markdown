---
layout: post
title: "Solving N+1 query in GraphQL using graphql-batch"
date: 2017-05-09 23:40:03 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p>One of the most important pain points in GraphQL is the problem thats commonly referred to as N+1 SQL queries. GraphQL query fields are designed to be stand-alone functions, and resolving those fields with data from a database might result in a new database request per resolved field.</p>
    <p>For a simple RESTful API endpoint logic, it's easy to analyze, detect, and solve N+1 issues by enhancing the constructed SQL queries. For GraphQL dynamically resolved fields, it's not that simple.</p>
    For example, Consider the GraphQL query to fetch article, its comments and commented user which I mentioned in <a href="http://tech.eshaiju.in/blog/2017/05/08/graphql-server-nested-query-implemenation-example-in-ruby-on-rails/">previous post</a>.
    {%codeblock lang:ruby%}
query {
  acticle(id: 1){
    title
    comments{
      comment
      user {
        id
        name
      }
    }
  }
}
{%endcodeblock%}
Output Response
{%codeblock lang:ruby%}
{
  "data": {
    "acticle": {
      "title": "A GraphQL Server",
      "comments": [
        {
          "comment": "Good article",
          "user": {
            "id": 1,
            "name": "Shaiju E"
          }
        },
        {
          "comment": "Keep going",
          "user": {
            "id": 1,
            "name": "Shaiju E"
          }
        },
        {
          "comment": "Another Comment",
          "user": {
            "id": 2,
            "name": "David"
          }
        },
        {
          "comment": "New Comment",
          "user": {
            "id": 1,
            "name": "Shaiju E"
          }
        },
        {
          "comment": "Another Comment from User 2",
          "user": {
            "id": 2,
            "name": "David"
          }
        },
        {
          "comment": "Another Comment from User 1",
          "user": {
            "id": 1,
            "name": "Shaiju E"
          }
        },
        {
          "comment": "TEST",
          "user": {
            "id": 1,
            "name": "Shaiju E"
          }
        }
      ]
    }
  }
}
{%endcodeblock%}
{% img /images/graphql_before.png 1200 260  %}
This will fire user query in for each comment. Total 7 instead of 1 for fetch 2 user. In Rest API we can solve this issue by eager loading users while fetching comments. But GraphQL query fields are designed to be stand-alone functions, and not aware of other functions.
<p>Facebook introduced DataLoader to solve this problem in Javascript projects. Shopify created <a href='https://github.com/Shopify/graphql-batch' >GraphQL::Batch</a> to solve this N+1 problem in ruby.<br />GraphQL::Batch Provides an executor for the graphql gem which allows queries to be batched. This is a flexible toolkit for lazy resolution with GraphQL.</p>
Installation
{%codeblock lang:ruby%}
gem 'graphql-batch'
{%endcodeblock%}
Now we need to define a custom loader, which is initialized with arguments that are used for grouping and a perform method for performing the batch load.
{%codeblock app/graphql/record_loader.rb%}
require 'graphql/batch'
class RecordLoader < GraphQL::Batch::Loader
  def initialize(model)
    @model = model
  end

  def perform(ids)
    @model.where(id: ids).each { |record| fulfill(record.id, record) }
    ids.each { |id| fulfill(id, nil) unless fulfilled?(id) }
  end
end
{%endcodeblock%}
Now we need to GraphQL::Batch as a plugin in your schema
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query Types::QueryType
  mutation Mutations::MutationType

  use GraphQL::Batch
end
{%endcodeblock%}
In our comments api example, we need to use above initialized RecordLoader for lazily execute User query
{%codeblock app/graphql/types/comment_type.rb%}
module Types
  CommentType = GraphQL::ObjectType.define do
    name "Comment"
    field :id, types.Int
    field :comment, types.String
    field :user, -> { UserType } do
      resolve -> (obj, args, ctx) {
        RecordLoader.for(User).load(obj.user_id)
      }
    end
  end
end
{%endcodeblock%}
Here,  <strong>resolve -&gt; (obj, args, ctx) {RecordLoader.for(User).load(obj.user_id) }</strong> will make user fetching lazy loading there by solve N+1 query problem.
{% img /images/graphql_example.png 1200 260  %}
Before:
{% img /images/graphql_before.png 1200 260  %}
After
{% img /images/graphql_after.png 1200 260  %}
<p>More information about GraphQL::Batch is available in <a href='https://github.com/Shopify/graphql-batch' >Gem Documentation</a><br/>
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.</p>
  </div>
</div>
