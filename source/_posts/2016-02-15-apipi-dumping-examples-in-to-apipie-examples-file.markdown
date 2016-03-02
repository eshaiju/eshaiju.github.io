---
layout: post
title: "APIPI Dumping Examples into apipie_examples file"
date: 2016-02-15 16:50:00 +0530
comments: true
categories: 
- Ruby On Rails
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	<a href='https://github.com/Apipie/apipie-rails'>Apipie-rails</a> is a DSL and Rails engine for documenting your RESTful API. In older versions of apipi-rails we can dump all our exapmles in to <strong>apipie_examples.yml</strong> file. From apipie-rails 0.2.0 onwards format of storing examples changed from YAML to JSON: the default location is at <strong>doc/apipie_examples.json</strong>.
	<br/><br/>
	Example:
	{%codeblock doc/apipie_examples.json%}
	{
	  "channels#index":[
	  {
	      "verb": "GET",
	      "path": "/api/v1/channels",
	      "query": null,
	      "code": 200,
	      "show_in_doc": 1,
	      "recorded": false,
	      "request_data": null,
	      "page": 1,
	      "response_data": {
	        "channels": [
	          {
	            "id": 1,
	            "name": "Channel 1",
	            "thumb_image_path": "/uploads/channel/thumb_image/1/channel1.png",
	            "language_name": "Tamil"
	          },
	          {
	            "id": 3,
	            "name": "Channel 3",
	            "thumb_image_path": "/uploads/channel/thumb_image/3/channel3.png",
	            "language_name": "Tamil"
	          }
	        ],
	        "top_channels": [
	          {
	            "id": 7,
	            "channel": {
	              "id": 2,
	              "name": "Channel 2",
	              "thumb_image_path": "/uploads/channel/thumb_image/2/channel2.png",
	              "language_name": "Tamil"
	            }
	          }
	        ],
	        "channels_count": 2
	      }
	  }],
	  "channels#show": [
	    {
	      "verb": "GET",
	      "path": "/api/v1/channels/:id",
	      "query": null,
	      "code": 200,
	      "show_in_doc": 1,
	      "recorded": false,
	      "request_data": null,
	      "page": 1,
	      "response_data": {
	        "channel": {
	          "id": 1,
	          "name": "suraj comedy",
	          "language_name": "Malayalam"
	        },
	        "videos": [
	          {
	            "id": 4,
	            "title": "Suraj Latest Comedy",
	            "description": "Malayalam Comedy Stage Show :",
	            "video_link": "https://www.youtube.com/watch?v=tGawZvf_UMA",
	            "view_count": 0,
	            "created_at": "2016-02-15T09:43:14.000Z",
	            "thumb_image_path": "/uploads/video/thumb_image/4/080.jpg"
	          }
	        ],
	        "top_videos": [
	          {
	            "id": 2,
	            "video": {
	              "id": 1,
	              "title": "Suraj-Shaji Latest Comedy",
	              "description": "Malayalam Comedy Stage Show : Pashanam Shaji Speaking pashanam shaji mazhavil manorama,Flowers tv Comedy Super Nite",
	              "video_link": "https://www.youtube.com/watch?v=tGawZvf_UMQ",
	              "view_count": null,
	              "created_at": "2016-02-15T07:48:22.000Z",
	              "thumb_image_path": nil
	            }
	          }
	        ]
	      }
	    }]            
	}
	{%endcodeblock %}
	{% img /images/channel_index.png 650 230  %}
	<br/>
	If you want to upgrade existing document in to newer version then The migration should be as easy as running:
{%codeblock %}
rake apipie:convert_examples
{%endcodeblock %}
This rake task will convert existing data from yml format in to json formatand store in doc/apipie_examples.json.
	</div>
</div>