---
layout: post
title: "preload has_many associations in graphql-ruby using graphql-preload"
date: 2017-10-15 17:11:06 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
<p>In the previous post about <a href='https://tech.eshaiju.com/blog/2017/05/09/solving-n-plus-1-query-in-graphql-using-graphql-batch/'>solving N+1 query in graphQL using graphql-batch</a>, we discussed batching queries using the <strong> graphql-batch</strong> gem. This works great for belongs_to relationships, but it is not useful for preloading has_many associations. For example, if you wanted the comments for each article, it would again produce N+1 queries. Luckily I found a gem <a href='https://github.com/ConsultingMD/graphql-preload'> graphql-preload</a> that solves this problem. The graphql-preload gem is created based on a <a href='https://gist.github.com/theorygeek/a1a59a2bf9c59e4b3706ac68d12c8434'>gist</a> by @theorygeek. This gem depends on the graphql-batch gem. so we need to keep graphql-batch settings in the schema for graphql-preload work. detailed explanation about graphql batch is given <a href='https://tech.eshaiju.com/blog/2017/05/09/solving-n-plus-1-query-in-graphql-using-graphql-batch'>here</a>.
</p>
<strong>Installation</strong>
<br/><br/>
Add this line to your application's Gemfile:
{%codeblock Gemfile%}
gem 'graphql-preload'
{%endcodeblock%}
Add <strong>enable_preloading</strong> to the schema file to be able to use this functionality.

{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query QueryType
  mutation MutationType

  use GraphQL::Batch
  enable_preloading
end
{%endcodeblock%}
Now, for the comments field, for example, we could write it like this:

{%codeblock app/graphql/types/article_type.rb%}
ArticleType = GraphQL::ObjectType.define do
  name "Article"

  field :comments, types[CommentType] do
    preload :comments
  end
end
{%endcodeblock%}
We can now throw a query like this at our API without the server with N+1 query problem for has_many association also.
{%codeblock lang:ruby%}
query {
  articles{
    id
    title
    body
    comments{
      comment
      user{
        name
      }
    }
  }
}
{%endcodeblock%}
Before preload
{% img /images/before_preload.png 1200 260 %}
After preload
{% img /images/after_preload.png 1200 260 %}
  </div>
</div>
