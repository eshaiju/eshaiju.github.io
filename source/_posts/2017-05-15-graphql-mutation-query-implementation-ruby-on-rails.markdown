---
layout: post
title: "GraphQL -Mutation query implementation - Ruby on Rails"
date: 2017-05-15 17:37:28 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p><strong>Mutation</strong> is a special type of query used to change data in the database like Creating, Editing or Deleting Records from a table or Store. These are the equivalent to the POST, PUT, PATCH and DELETE in HTTP/REST speak. Defining mutations is very similar to defining queries. The only difference is how you implement the logic inside the mutation. In mutation, we can control and specify the output data that API need to return after mutation procedure.</p>
    <p>In this article, I am Adding a mutation query to add comments to an article which we discussed in <a href="https://tech.eshaiju.com/blog/2017/05/09/solving-n-plus-1-query-in-graphql-using-graphql-batch/">previous example</a>.</p>
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
{%codeblock config/application.rb%}
config.autoload_paths << Rails.root.join('app/graphql/mutations')
{%endcodeblock%}
  <p>Now we need to define specific mutation query. Following are the process to define a mutation<br />- give operation a name <br />- declare its inputs<br />- declare its outputs<br />- declare the mutation procedure in resolve block.<br /> <strong>resolve</strong> should return a hash with a key for each of the <strong>return_fields</strong></p>
  In out example, we need to define <strong>CommentMutations</strong> in mutations folder.
  {%codeblock app/graphql/mutations/comment_mutations.rb%}
# encoding: utf-8
module CommentMutations
  Create = GraphQL::Relay::Mutation.define do
    name "AddComment"

    # Define input parameters
    input_field :articleId, !types.ID
    input_field :userId, !types.ID
    input_field :comment, !types.String

    # Define return parameters
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
end
{%endcodeblock%}
    Here <strong>input_field</strong> specify the input params we can pass in the query. In <strong>return_field</strong>, we can specify the fields returning after the update. Inside resolve block, we define the business logic. and <strong>resolve</strong> should return a hash with a key for each of the return_fields.<br/><br/>
    After defining this, we need to add the mutation's derived field to the mutation type.
{%codeblock app/graphql/mutations/mutation_type.rb%}
MutationType = GraphQL::ObjectType.define do
  name "Mutation"
  # Add the mutation's derived field to the mutation type
  field :addComment, field: CommentMutations::Create.field
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

Lets write another example for updation mutation. If we want to update a comment, we need to write UpdateComment mutation in comment_mutations.rb

{%codeblock app/graphql/mutations/comment_mutations.rb%}
# encoding: utf-8
module CommentMutations
  Create = GraphQL::Relay::Mutation.define do
    name "AddComment"

    # Define input parameters
    input_field :articleId, !types.ID
    input_field :userId, !types.ID
    input_field :comment, !types.String

    # Define return parameters
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

  Update = GraphQL::Relay::Mutation.define do
    name "UpdateComment"

    # Define input parameters
    input_field :id, !types.ID
    input_field :comment, types.ID
    input_field :userId, types.ID
    input_field :articleId, types.ID

    # Define return parameters
    return_field :comment, CommentType
    return_field :errors, types.String


    resolve ->(object, inputs, ctx) {
      comment = Comment.find_by_id(inputs[:id])
      return { errors: 'Comment not found' } if comment.nil?

      valid_inputs = ActiveSupport::HashWithIndifferentAccess.new(inputs.instance_variable_get(:@original_values).select { |k, _| comment.respond_to? "#{k}=".underscore }).except(:id)
      if comment.update_attributes(valid_inputs)
        { comment: comment }
      else
        { errors: comment.errors.to_a }
      end
    }
  end
end
{%endcodeblock%}
Main defference here is, we need to create <strong>valid_inputs</strong>. This will allow us mass assignment with update attributes with valied fields which we passed.<br/><br/>
After defining this, we need to add the mutation's derived field to the mutation type.
{%codeblock app/graphql/mutations/mutation_type.rb%}
MutationType = GraphQL::ObjectType.define do
  name "Mutation"
  # Add the mutation's derived field to the mutation type
  field :addComment, field: CommentMutations::Create.field
  field :updateComment, field: CommentMutations::Update.field
end
{%endcodeblock%}

Mutation for delete a comment and return post and deleted comment ID
{%codeblock app/graphql/mutations/comment_mutations.rb%}
# encoding: utf-8
module CommentMutations
  Destroy = GraphQL::Relay::Mutation.define do
    name 'DestroyComment'
    description 'Delete a comment and return post and deleted comment ID'

    # Define input parameters
    input_field :id, !types.ID

    # Define return parameters
    return_field :deletedId, !types.ID
    return_field :article, ArticleType
    return_field :errors, types.String

    resolve ->(_obj, inputs, ctx) {
      comment = Comment.find_by_id(inputs[:id])
      return { errors: 'Comment not found' } if comment.nil?

      article = comment.article
      comment.destroy

      { article: article.reload, deletedId: inputs[:id] }
    }
  end

  # Other mutations defined here....
end
{%endcodeblock%}

{%codeblock app/graphql/mutations/mutation_type.rb%}
MutationType = GraphQL::ObjectType.define do
  name "Mutation"
  # Add the mutation's derived field to the mutation type
  field :addComment, field: CommentMutations::Create.field
  field :updateComment, field: CommentMutations::Update.field
  field :destroyComment, field: CommentMutations::Destroy.field
end
{%endcodeblock%}
Now we can try this mutation in GraphiQL:
{%codeblock lang:ruby%}
mutation destroyComment($comment: DestroyCommentInput!){
  destroyComment(input: $comment){
    errors
    article{
      id
      comments{
        id
        comment
      }
    }
  }
}

Query Variabbles

{
  "comment": {
    "id": 3
  }
}
{%endcodeblock%}
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
