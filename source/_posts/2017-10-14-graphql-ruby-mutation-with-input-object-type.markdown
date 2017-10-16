---
layout: post
title: "GraphQL Ruby Mutation with input object type"
date: 2017-10-14 23:52:38 +0530
comments: true
categories:
- GraphQL
- Ruby
- Ruby On Rails
---

<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
  <p>In the previous blog post about <a href='http://tech.eshaiju.in/blog/2017/05/15/graphql-mutation-query-implementation-ruby-on-rails/'>GraphQl mutation</a>, we specified each field in input as input_field. This method will do the job but it has some issues.</p>
  Consider an example mutation for saving an article. Here we used the input_field method without InputObjectType.
{%codeblock app/graphql/mutations/article_mutations.rb%}
# encoding: utf-8
module ArticleMutations
  Create = GraphQL::Relay::Mutation.define do
    name 'AddArticle'

    input_field :title, !types.String
    input_field :body, !types.String

    # Define return parameters
    return_field :article, ArticleType
    return_field :errors, types.String

    resolve lambda { |object, inputs, ctx|
      article = Article.new(title: inputs[:title], body: inputs[:body])

      if article.save
        { article: article }
      else
        { errors: article.errors.to_a }
      end
    }
  end
end
{%endcodeblock%}

{%codeblock lang:ruby%}
mutation addArticle{
  addArticle(input: { title: "GraphQL mutation", body: " This article is about graphql mutation using  InputObjectType" })
  {
    article{
      id
      title
      body
    }
  }
}
{%endcodeblock%}

For saving this we need to instantiate article object by assigning each and every input params like this.
{%codeblock app/graphql/mutations/article_mutations.rb%}
article = Article.new(title: inputs[:title], body: inputs[:body])
{%endcodeblock%}
This method become ugly if our object contain lots fields.
We can define and utilize mutation input params in the better way using <strong>InputObjectType</strong>. If we can specify article object as input_field instead of each field we can save object like below.
{%codeblock app/graphql/mutations/article_mutations.rb%}
article = Article.new(inputs[:article].to_h)
{%endcodeblock%}
which is maintanable and easy to read.
Lets see how we can achive this.
<br/>
The first step is to define InputObjectType and declare that input object type as input_field. Here we are going to create InputObjectType in another folder inside our graphql folder for maintainability.

{%codeblock app/graphql/input_objects/article_input_object_type.rb%}
ArticleInputObjectType = GraphQL::InputObjectType.define do
  name 'ArticleInput'
  input_field :title, !types.String
  input_field :body, !types.String
end
{%endcodeblock%}
Since we created new folder for <strong>input_objects</strong>, we have to tell Rails to autoload paths. place below code in <strong>application.rb</strong> to autoload it.

{%codeblock config/application.rb%}
config.autoload_paths << Rails.root.join('app/graphql/input_objects')
{%endcodeblock%}
Now we can declare <strong>ArticleInputObjectType</strong> as input_field inside our mutation and use declared input_field inside our mutation resolver to save the article. So final mutation definition will look like this.

{%codeblock app/graphql/mutations/article_mutations.rb%}
# encoding: utf-8
module ArticleMutations
  Create = GraphQL::Relay::Mutation.define do
    name 'AddArticle'

    # Define input parameters
    input_field :article, !ArticleInputObjectType

    # Define return parameters
    return_field :article, ArticleType
    return_field :errors, types.String

    resolve lambda { |object, inputs, ctx|
      article = Article.new(inputs[:article].to_h)

      if article.save
        { article: article }
      else
        { errors: article.errors.to_a }
      end
    }
  end
end
{%endcodeblock%}

That's it, we are done, now we can save new article using addArticle mutation.
{%codeblock lang:ruby%}
mutation addArticle{
  addArticle(input: { article: { title: "GraphQL mutation", body: " This article is about graphql mutation using  InputObjectType" } })
  {
    article{
      id
      title
      body
    }
  }
}
{%endcodeblock%}
{% img /images/graphql-mutation.png 1200 260  %}
You can see sample code <a href="https://github.com/eshaiju/graphql-ruby-sample">here</a>.
  </div>
</div>
