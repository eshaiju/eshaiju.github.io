---
layout: post
title: "GraphQL Server - Nested query Implementation example in Ruby on Rails"
date: 2017-05-08 21:28:51 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p>One of the main advantages of GraphQL is, we can create queries with a different combination of datasets which will avoid multiple requests to fetch required data, thereby eliminating overheads. In this post, I am discussing how we can create nested datasets in GraphQL.

    <br /> <br />
    In the <a href="https://tech.eshaiju.com/blog/2017/05/06/a-graphql-server-implementation-ruby-on-rails/">previous post</a>, I mentioned about Article model and query to fetch an article. Here I'm going to fetch comments under the article. Assuming we have comments and user model with corresponding associations.</p>

    Now we need to add CommentType.
    {%codeblock app/graphql/types/comment_type.rb%}
CommentType = GraphQL::ObjectType.define do
  name "Comment"
  field :id, types.Int
  field :comment, types.String
  field :user, UserType
end
{%endcodeblock%}

    Here we are exposing commented user which is UserType. So we need to define that type also in user_type.rb
    {%codeblock app/graphql/types/user_type.rb%}
UserType = GraphQL::ObjectType.define do
  name "User"
  field :id, types.Int
  field :name, types.String
  field :email, types.String
end
{%endcodeblock%}

    Now we need to expose comments inside article model
    {%codeblock app/graphql/types/article_type.rb%}
ArticleType = GraphQL::ObjectType.define do
  name "Article"
  field :id, types.Int
  field :title, types.String
  field :body, types.String
  field :comments, types[CommentType]
end
{%endcodeblock%}
    <p>Here comments are an array of objects so we need to specify CommentType using <strong>types</strong> keyword. We can see in comment_type.rb we are not specifying types for UserType, as it is returning a single object. Since we defined association in, This will fetch all comments of the article by executing article.comments.</p>
Put below code in application.rb to autoload graphql and types folder like so:
{%codeblock config/application.rb%}
config.autoload_paths << Rails.root.join('app/graphql')
config.autoload_paths << Rails.root.join('app/graphql/types')
{%endcodeblock%}

    Here&rsquo;s an example of a GraphQL query that a client can use to ask a server about the title of the article, corresponding comments and commented user:
    {%codeblock lang:ruby%}
query {
  article(id: 1){
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
    Here’s a possible JSON response for that query:
    {%codeblock lang:ruby%}
{
  "data": {
    "article": {
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
        }
      ]
    }
  }
}
{%endcodeblock%}
    You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
