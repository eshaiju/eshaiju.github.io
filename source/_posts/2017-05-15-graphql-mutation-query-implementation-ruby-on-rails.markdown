---
layout: post
title: "GraphQL -Mutation query implementation - Ruby on Rails"
date: 2017-05-15 17:37:28 +0530
comments: true
categories:
- Ruby On Rails
- GraphQL
- Ruby
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p><strong>Mutation</strong> is a special type of query used to change data in the database like Creating, Editing or Deleting Records from a table or Store. These are the equivalent to the POST, PUT, PATCH and DELETE in HTTP/REST speak. Defining mutations is very similar to defining queries. The only difference is how you implement the logic inside the mutation. In mutation, we can control and specify the output data that API need to return after mutation procedure.</p>
    <p>In this article, I am Adding a mutation query to add comments to an article which we discussed in <a href="http://tech.eshaiju.in/blog/2017/05/09/solving-n-plus-1-query-in-graphql-using-graphql-batch/">previous example</a>.</p>
    To add mutations to your GraphQL schema, first we need to define a mutation type in mutations folder
    {%codeblock app/graphql/mutations/mutation_type.rb%}
MutationType = GraphQL::ObjectType.define do
  name "Mutation"
end
{%endcodeblock%}
Now we need to pass it into the schema
{%codeblock app/graphql/graphql_ruby_sample_schema.rb%}
GraphqlRubySampleSchema = GraphQL::Schema.define do
  query QueryType
  mutation MutationType
end
{%endcodeblock%}
Like QueryType, MutationType is a root of the schema. Members of MutationType are mutation fields. For GraphQL in general, mutation fields are identical to query fields except that they have side-effects (which mutate application state, eg, update the database).<br/><br/>
Since we created new folder for mutations, we have to tell Rails to autoload paths. Put below code in application.rb to autoload it.
{%codeblock config/application.rb.rb%}
config.autoload_paths << Rails.root.join('app/graphql/mutations')
{%endcodeblock%}
  <p>Now we need to define specific mutation query. Following are the process to define a mutation<br />- give operation a name <br />- declare its inputs<br />- declare its outputs<br />- declare the mutation procedure in resolve block.<br /> <strong>resolve</strong> should return a hash with a key for each of the `return_field`s</p>
  In out example, we need to define addComment mutation in mutation folder.
  {%codeblock app/graphql/mutations/add_comment_mutation.rb.rb%}
# encoding: utf-8
AddCommentMutation = GraphQL::Relay::Mutation.define do
  name "AddComment"

  input_field :articleId, !types.ID
  input_field :userId, !types.ID
  input_field :comment, !types.String

  return_field :article, ArticleType
  return_field :errors, types.String


  resolve ->(object, inputs, ctx) {
    article = Article.find_by_id(inputs[:articleId])
    return { errors: 'Article not found' } if article.nil?

    comments = article.comments
    new_comment = comments.build(user_id: inputs[:userId], comment: inputs[:comment])
    if new_comment.save
      { article: article }
    else
      { errors: new_comment.errors.to_a }
    end
  }
end
{%endcodeblock%}
    Here <strong>input_field</strong> specify the input params we can pass in the query. In <strong>return_field</strong>, we can specify the fields returning after the update. Inside resolve block, we define the business logic. and <strong>resolve</strong> should return a hash with a key for each of the return_fields.<br/><br/>
    After defining this, we need to add the mutation's derived field to the mutation type.
{%codeblock app/graphql/mutations/mutation_type.rb%}
MutationType = GraphQL::ObjectType.define do
  name "Mutation"
  # Add the mutation's derived field to the mutation type
  field :addComment, field: AddCommentMutation.field
end
{%endcodeblock%}

Now we can try this mutation in GraphiQL:
{%codeblock lang:ruby%}
mutation addComment{
  addComment(input: { comment: "New comment", articleId: 1, userId: 1})
  {
    article{
      id
      comments{
        comment
        user{
          name
        }
      }
    }
  }
}
{%endcodeblock%}
Here’s a possible JSON response for that query:
{%codeblock lang:ruby%}
{
  "data": {
    "addComment": {
      "article": {
        "id": 1,
        "comments": [
          {
            "comment": "Good article",
            "user": {
              "name": "Shaiju E"
            }
          },
          {
            "comment": "Keep going",
            "user": {
              "name": "Shaiju E"
            }
          },
          {
            "comment": "Another Comment",
            "user": {
              "name": "David"
            }
          },
          {
            "comment": "New Comment",
            "user": {
              "name": "Shaiju E"
            }
          },
          {
            "comment": "Another Comment from User 2",
            "user": {
              "name": "David"
            }
          },
          {
            "comment": "Another Comment from User 1",
            "user": {
              "name": "Shaiju E"
            }
          },
          {
            "comment": "TEST",
            "user": {
              "name": "Shaiju E"
            }
          },
          {
            "comment": "New comment",
            "user": {
              "name": "Shaiju E"
            }
          }
        ]
      }
    }
  }
}
{%endcodeblock%}
We can call the same query by passing inputs using variables
{%codeblock lang:ruby%}
mutation addComment($comments: AddCommentInput!){
  addComment(input: $comments){
    article{
      id
      comments{
        comment
        user{
          name
        }
      }
    }
  }
}

Query Variabbles

{
  "comments": {
    "comment": "New comment1",
    "articleId": 1,
    "userId": 1
  }
}
{%endcodeblock%}
<strong>$comments: AddCommentInput!</strong> will configure the variable <strong>$comments</strong> to take values from <strong>query variables</strong> section. <strong>input: $comments</strong> will pass $comments as input to mutation query.</p>
{% img /images/mutation.png 1200 260  %}
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
