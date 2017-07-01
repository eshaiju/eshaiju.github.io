---
layout: post
title: "DRY GraphQL  definitions using interfaces"
date: 2017-06-09 11:10:47 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
  <p>We can make GraphQL Types and mutations DRY using interfaces. An Interface is an abstract type that contains a collection of types which implement some of the same fields. We can avoid specifying the same set of fields in different GraphQL Types and mutations by defining an interface and using in sharing Types and mutations.</p>
<p>Interfaces can have fields, defined with a field, just like a GraphQL object type. Objects which implement this field inherit field definitions from the interface. An object type can override the inherited definition by redefining that field.</p>
<p>For example, active record time stamps are common fields in Rails models. So we can avoid declaring these fields in all object types by declaring an interface <strong>ActiveRecordTimestamp</strong> with these fields and using it our object types.</p>
We can define ActiveRecordTimestamp like this.

{%codeblock app/graphql/interfaces/active_record_timestamp.rb%}
ActiveRecordTimestamp = GraphQL::InterfaceType.define do
  name 'ActiveRecordTimestamp'
  field :createdAt, types.String, property: :created_at
  field :updatedAt, types.String, property: :updated_at
end
{%endcodeblock%}

Since we created a new folder for interfaces, we have to tell Rails to autoload paths. Place below code in application.rb to autoload it.
{%codeblock config/application.rb%}
config.autoload_paths << Rails.root.join('app/graphql/interfaces')
{%endcodeblock%}

Now we can add defined interfaces in our object Type using <strong>interfaces</strong> keyword.

{%codeblock app/graphql/types/article_type.rb%}
ArticleType = GraphQL::ObjectType.define do
  name "Article"
  interfaces [ActiveRecordTimestamp]

  field :id, types.Int
  field :title, types.String
  field :body, types.String
  field :comments, types[CommentType]
end
{%endcodeblock%}

Example for including multiple interfaces in Ruby object type.

{%codeblock app/graphql/types/comment_type.rb%}
CommentType = GraphQL::ObjectType.define do
  name "Comment"

  # multiple interfaces included using comma.
  interfaces [ActiveRecordTimestamp, GraphQL::Relay::Node.interface]
  field :id, types.Int
  field :comment, types.String
  field :user, UserType
end
{%endcodeblock%}

Now, this active record time stamp will be available in both above-mentioned object types.

<p>We can use return_interfaces to define and reuse return types in different mutation definitions. The result of the resolve block will be passed to the field definitions in the interfaces, and both interface-specific and mutation-specific fields will be available to clients.</p>

For example, we can define a interface which will define notification of a mutation.
{%codeblock app/graphql/interfaces/mutation_result.rb%}
MutationResult = GraphQL::InterfaceType.define do
  name "MutationResult"
  field :success, !types.Boolean
  field :notice, types.String
  field :errors, types[ValidationError]
end
{%endcodeblock%}


{%codeblock app/graphql/mutations/article_mutations.rb%}
# encoding: utf-8
CreateArticle = GraphQL::Relay::Mutation.define do
  # ...
  return_field :title, types.String
  return_field :body, types.String
  return_interfaces [MutationResult],

  # clientMutationId will also be available automatically
  resolve ->(obj, input, ctx) {
    article, notice = Article.create_with_input(...)
    {
      success: article.persisted?
      notice: notice
      title: article.title
      errors: article.errors
    }
  }
end
{%endcodeblock%}
   </div>
</div>
