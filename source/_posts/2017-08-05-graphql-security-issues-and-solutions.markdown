---
layout: post
title: "GraphQl Security issues and solutions"
date: 2017-08-05 12:49:42 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p>One of the main highlights of GraphQl is declarative fetching, the client can define the shape of the data he needed and GraphQL server will provide data in the same shape. But an evil client can exploit this advantage to make deeper nesting and complex queries and can do DDoS attack.<p>
    Example for a deeper nesting query:
{%codeblock lang:ruby%}
query {
  articles{
    title
    body
    comments{
      comment
      user{
        comments{
          user{
            comments
            {
              comment
              user{
                comments{
                  comment
                  user{
                    comments{
                      comment
                      user{
                        name
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
{%endcodeblock%}
GraphQL provide three confirations to avoid this issue. <br/><br/>

<strong>Timeout:</strong>
We can avoid queries utilizing server more than specified time by setting a timeout. If a query takes more time for execute, then after time out GraphQL will return the result of query up to that time and simply reject nonexcecuted part of the query.
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query QueryType
  mutation MutationType
end

GraphqlRubySampleSchema.middleware << GraphQL::Schema::TimeoutMiddleware.new(max_seconds: 10) do |err, query|
  Rails.logger.info("GraphQL Timeout: #{query.query_string}")
end
{%endcodeblock%}
{% img /images/graphql-timeout.png 1200 260  %}
{% img /images/graphql-timeout-1.png 1200 260  %}

<strong>Maximum Depth:</strong> Another solution for prevent deeply-nested queries is setting <strong>max_deplth</strong>. If our query depth is greater than max_depth we set, the server will simply reject full query and return an error message.
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query QueryType
  mutation MutationType

  max_depth 12
end
{%endcodeblock%}
{% img /images/graphql-maxdepth.png 1200 260  %}
<strong>Query Complexity:</strong> We can prevent complex queries by setting <strong>max_complexity</strong> in GraphQL schema. Each field in our type system has a complexity number so if our query complexity exceeds max_complexity, the server will reject query just like in the case of max_depth.
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query QueryType
  mutation MutationType

  max_depth 12
  max_complexity 100
end
{%endcodeblock%}
{% img /images/graphql-maxcomplexity2.png 1200 260  %}
{% img /images/graphql-maxcomplexity1.png 1200 260  %}
<p>By default complexity of field is 1, but we can configure constant complexity for fields in type system and also can set complexity dynamically by passing conditions </p>
{%codeblock lang:ruby%}
# Constant complexity:
field :comments, types[CommentType], complexity: 10

# Dynamic complexity:
field :top_comments, types[CommentType] do
  argument :limit, types.Int, default_value: 5
  complexity ->(ctx, args, child_complexity) {
    if ctx[:current_user].admin?
      # no limit for admin users
      0
    else
      # `child_complexity` is the value for selections
      # which were made on the items of this list.
      #
      # We don't know how many items will be fetched because
      # we haven't run the query yet, but we can estimate by
      # using the `limit` argument which we defined above.
      args[:limit] * child_complexity
    end
  }
end
{%endcodeblock%}
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
