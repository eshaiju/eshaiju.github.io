---
layout: post
title: "A GraphQL server implementation - Ruby On Rails"
date: 2017-05-06 14:55:58 +0530
comments: true
categories:
- Ruby On Rails
- GraphQL
- Ruby
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
  <p><a href="http://graphql.org/">GraphQL </a>is a query language for APIs developed by the Facebook team. This language gives clients the power to ask for exactly what they need and nothing more makes it easier to evolve APIs over time and enables powerful developer tools. Facebook open sourced it in 2015.</p>
  <div class="graphiqlVid">
    <video autoplay="" loop="" playsinline="" style="margin-bottom: 0px;">
      <source src="http://graphql.org/img/graphiql.mp4?x" type="video/mp4">
    </video><small>video courtesy: <a href="http://graphql.org/">graphql.org</a></small>
  </div>
<br/>
<p>A GraphQL request can be either <strong>a query</strong> (read operation) or <strong>a mutation </strong>(write operation). For both cases, the request is a simple string that a GraphQL service can interpret, execute, and resolve with data in a specified format.</p>

<strong>Main features of GraphQL are:</strong> <br/><br/>

<strong>Retrieve only the data your client needs in a single request - No over-fetching:</strong> <br/>
<p>Send a GraphQL query to your API with the fields you want to fetch and get only that set of fields in return. Apps using GraphQL are fast and stable because they control the data they get, not the server.</p>

<strong>Get many resources in a single round-trip:</strong>
<p>We can create queries with a different combination of datasets which will avoid multiple requests to fetch required data, thereby eliminating overheads.</p>

<strong>Describe what’s possible with a type system:</strong>
<p>GraphQL APIs have a single endpoint and it organizes API with requesting types and fields.</p>

<strong>Evolve your API without versions - maintainability:</strong>
<p>Add new fields and types to your GraphQL API without impacting existing queries. Aging fields can be deprecated and hidden from tools. By using a single evolving version, GraphQL APIs give apps continuous access to new features and encourage cleaner, more maintainable server code.</p>

<strong>GraphQL Queries:</strong>

<p>Here’s an example of a GraphQL query that a client can use to ask a server about the name and email of user #1:</p>
{%codeblock%}
{
  user(id: 1) {
    firstName,
    lastName,
    email
  }
}
{%endcodeblock%}
Here’s a possible JSON response for that query:
{%codeblock%}
{
  "data": {
    "user": {
      "firstName": "Shaiju",
      "lastName": "E",
      "email": "eshaiju@gmail.com"
     }
   }
}
{%endcodeblock%}
<strong>GraphQL server implementation in Ruby On Rails Application</strong>

<p>Here we are assuming, we have an existing ROR application with model Article and fields title and body.</p>
You can install graphql from RubyGems by adding to your application’s Gemfile:
{%codeblock Gemfile%}
# Gemfile
gem "graphql"
gem 'graphiql-rails', group: :development
{%endcodeblock%}
Then, run bundle install.<br/><br/>
Now you can get started with a few GraphQL generators:
{%codeblock%}rails g graphql:install{%endcodeblock%}

This will:
<ul><li>Set up a folder structure in app/graphql/</li>
<li>Add schema definition</li>
<li>Add a query type definition</li>
<li>Add a route and controller for executing queries</li>

After installing you can see your new schema by:
{%codeblock%}
bundle install
rails server
open localhost:3000/graphiql
{%endcodeblock%}
After this, you can build a GraphQL server by hand or GraphQL generators
<p>Here  I am building by hand.</p>

Define some types:  Add article_type.rb in 'types' folder which will define ArticleType
{%codeblock app/graphql/types/article_type.rb%}
module Types
  ArticleType = GraphQL::ObjectType.define do
    name "Article"
    field :id, types.Int
    field :title, types.String
    field :body, types.Int
    field :comments, types[CommentType]
  end
end
{%endcodeblock%}
Now we need to build schema, which is what we use to query
{%codeblock app/graphql/types/query_type.rb%}
module Types
  QueryType = GraphQL::ObjectType.define do
    name "Query"
    description "The query root of this schema"

    field :acticle do
      type ArticleType
      argument :id, !types.ID
      description "Find a Article by ID"
      resolve ->(obj, args, ctx) { Article.find_by_id(args["id"]) }
    end
  end
end
{%endcodeblock%}
This will query article by accepting ID as argument

Then, build a schema with QueryType as the query entry point:

Add following in  graphql_ruby_sample_schema.rb
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
# encoding: utf-8
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query Types::QueryType
end
{%endcodeblock%}
This schema is ready to serve GraphQL queries!. play around this query in GraphiQL.

<br/><br/>Here’s an example of a GraphQL query that a client can use to ask a server about the title  of article #1:
{%codeblock%}
query {
  acticle(id: 1){
    title
  }
}
{%endcodeblock%}
Here’s a possible JSON response for that query:
{%codeblock%}
{
  "data": {
    "acticle": {
      "title": "A GraphQL Server"
    }
  }
}
{%endcodeblock%}
{% img /images/graphql_query.png 790 260  %}
You can execute queries from a query string:
{%codeblock%}
query_string = "
{
  acticle(id: 1) {
    id
    title
  }
}"
result_hash = GraphqlRubySampleSchema.execute(query_string)

output:
# {
#   "data" => {
#     "acticle" => {
#        "id" => 1,
#        "title" => "A GraphQL Server"
#     }
#   }
# }
{%endcodeblock%}
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
