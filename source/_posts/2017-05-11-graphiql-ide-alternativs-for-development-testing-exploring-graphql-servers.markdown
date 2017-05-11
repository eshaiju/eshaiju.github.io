---
layout: post
title: "GraphiQL IDE alternativs for Development/Testing/Exploring GraphQL Servers"
date: 2017-05-11 20:17:26 +0530
comments: true
categories:
- GraphQL
- GraphiQL
---


<div class='post'>
  <div dir="ltr" style="text-align: left;" trbidi="on">
    <p><a href='https://github.com/graphql/graphiql'>GraphiQL</a> is a graphical interactive in-browser GraphQL IDE. This is a great tool for testing GraphQL endpoints and also for Exploring documentation about GraphQL Servers. There are some limitations for default GraphiQL tool, It does not provide any option for saving queries like we have in postman and other API clients. It only saves the latest query in local storage. Another issue with GraphiQL is, it not providing any option for pass headers into GraphQL queries. It is very critical for the testing of application which needs to pass auth-token or some other headers. In this post Iam exploring few alternativs.</p>
    <strong>GraphQL IDE</strong>
    <p><a href='https://github.com/redound/graphql-ide'>GraphQL IDE</a> is n extensive IDE for exploring GraphQL API's. It allows manage projects, import/export, store queries, toggle query history, passing custom headers and setting environment variables for dynamic headers. Currently, it is only available for MacOS. Window / Linux version of this application is under development but can build the binary.</p>
    Execute queries
    {% img /images/graphql-ide-1.png 1200 260  %}
    Custom headers
    {% img /images/graphql-ide-2.png 1200 260  %}
    Environment variables for dynamic headers
    {% img /images/graphql-ide-3.png 1200 260  %}
    Manage projects
    {% img /images/graphql-ide-4.png 1200 260  %}
    <br/>
    <strong>GraphiQL.app</strong>
    <p><a href='https://github.com/skevy/graphiql-app'>GraphiQL.app</a> is light, Electron-based wrapper around GraphiQL. This Provides a tabbed interface for editing and testing GraphQL queries/mutations with GraphiQL. It is only available for MacOS. If you have Homebrew installed on OSX, you can install it by:</p>
    {%codeblock%}
    brew cask install graphiql
    {%endcodeblock%}
    {% img /images/graphiql-app.png 1200 260  %}
    <br/>
    <strong>GraphiQL Feen(Chrome Extension)</strong>
    <p><a href='https://chrome.google.com/webstore/detail/graphiql-feen/mcbfdonlkfpbfdpimkjilhdneikhfklp'>GraphiQL Feen</a> is a Chrome Extension that allows you to explore and test GraphQL endpoints. It utilizes the Popular GraphiQL component. It</p>
    <p>Features are: <br /> Save/Load Queries and variables to build a library of useful queries.<br />* Save/Select Server Definitions so you can have different settings for different servers.<br />* GET/POST/Multi-part GraphQL requests supported!<br /> * Authorization defined so cookies forwarded to the domain<br />* Define Headers that will be sent with the request, Headers can even override existing Request headers:<br /> * Define the Origin: header for CORS requests to allow your server to process correctly.<br /> * Define CSRF token headers<br /> * Override all Cookies so you can pass authentication information with your requests.<br />* EXPORT your entire application state to a JSON file<br />* IMPORT saved state so you can restore your state </p>
    {% img /images/graphql-feen.png 1200 260  %}
  </div>
</div>
