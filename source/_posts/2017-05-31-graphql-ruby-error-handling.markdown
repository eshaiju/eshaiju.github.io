---
layout: post
title: "GraphQL ruby error handling"
date: 2017-05-31 23:07:58 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p>GraphQL endpoints, we can expose errors as part of our schema. We should check <strong>errors</strong> fields to see if any errors in result data. For example if we query a field which is not existing in type system, we will get a error response. This type of errors is not supposed to be displayed to end users. It helps with debugging, error tracking etc.
{%codeblock lang:ruby%}
{
  "errors": [
    {
      "message": "Field 'user' doesn't exist on type 'Article'",
      "locations": [
        {
          "line": 5,
          "column": 5
        }
      ],
      "fields": [
        "query",
        "article",
        "user"
      ]
    }
  ]
}
{%endcodeblock%}
If a field's resolve function returns an ExecutionError, the error will be inserted into the response's <strong>errors</strong> key and the field will resolve to nil. It is often required to perform additional validation of the input parameters passed to GraphQL mutations, and provide user-friendly error messages in case validation fails or mutation cannot be completed successfully.
<br/>
For example, we could add errors to ArticleType:
{%codeblock app/graphql/types/article_type.rb%}
ArticleType = GraphQL::ObjectType.define do
  name "Article"
  field :title, types.String
  # ...
  field :errors, types[types.String] do
    resolve -> (obj, args, ctx) { obj.errors.full_messages }
  end
end
{%endcodeblock%}
Then, when clients create a article, they should check the errors field to see if it was successful:
{%codeblock lang:ruby%}
mutation {
  createArticle(article: {title: "GraphQL is Nice"}) {
    id
    title
    errors # in case the save failed
  }
}
{%endcodeblock%}
If errors are present (and id is null), the client knows that the operation was unsuccessful, and they can discover reason. If some part of our resolve function would raise an error, we can rescue it and add to the <strong>errors</strong> key by returning a GraphQL:: ExecutionError
{%codeblock app/graphql/mutations/article_mutations.rb%}
resolve -> (obj, args, ctx) {
  article_params = args["article"].to_h
  begin
    article = Article.create!(post_params)
    # on success, return the article:
    article
  rescue ActiveRecord::RecordInvalid => err
    # on error, return an error:
    GraphQL::ExecutionError.new("Invalid input for Article: #{article.errors.full_messages.join(", ")}")
  end
}
{%endcodeblock%}

If we don't want to <strong>begin ... rescue ... end</strong> in every field, we can wrap resolve functions in error handling.<br/> For example, we could make an object that wraps another resolver:

{%codeblock app/graphql/resolvers/rescue_form.rb%}
# Wrap field resolver `resolve_func` with a handler for `error_superclass`.
# `RescueFrom` instances are valid field resolvers too.
class RescueFrom
  def initialize(error_superclass, resolve_func)
    @error_superclass = error_superclass
    @resolve_func = resolve_func
  end

  def call(obj, args, ctx)
    @resolve_func.call(obj, args, ctx)
  rescue @error_superclass => err
    # Your error handling logic here:
    # - return an instance of `GraphQL::ExecutionError`
    # - or, return nil:
    nil
  end
end
{%endcodeblock%}
apply it to fields on an opt-in basis:
{%codeblock app/graphql/mutations/article_mutations.rb%}
field :create_article, ArticleType do
  # Wrap the resolve function with `RescueFrom.new(err_class, ...)`
  resolve RescueFrom.new(ActiveRecord::RecordInvalid, -> (obj, args, ctx) { ... })
end
{%endcodeblock%}
<br/>
Reference: <a href='http://graphql-ruby.org/'>GraphQL Ruby website</a>
  </div>
</div>
