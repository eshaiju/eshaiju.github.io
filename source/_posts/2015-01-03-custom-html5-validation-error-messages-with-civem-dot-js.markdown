---
layout: post
title: "Custom HTML5 Validation Error Messages with Civem.js"
date: 2015-01-03 21:58:48 +0530
comments: true
categories: 
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
		HTML5 allows us to implement client-side form validation without any JavaScript coding. However, error messages are hard-coded to the browser. In my recent project I faced situation, I have to use custome error message and show error message in both english and arabic. I achived error message customisation with <a href='https://github.com/javanto/civem.js'>Civem.js </a> which let you easily change the message to whatever you wish.
        <br/>
		{% img /images/img-en.jpg 350 250  %}
		{% img /images/imgo-ar.jpg 333 250  %}
        
		<br/>
		
		Following are steps to add custom error messages to our project.
    <br/>
		1 - Grab the latest <a href='https://github.com/javanto/civem.js/downloads'>download</a>
		<br/>
		2 - Add the <strong>civem.js</strong> script to your page
		<br/>
		3 - Start using the custom error message attributes on your <strong>input</strong>, <strong>textarea</strong> and <strong>select</strong> elements
		<br/>
		4 - To customise required field error message use <strong>data-errormessage-value-missing</strong> attribute
		<br/>

{%codeblock%}
		 	<input type="text" required data-errormessage-value-missing="Something's missing" >
{%endcodeblock%}
		5 - To customise filed type mismatch error message, use <strong>data-errormessage-type-mismatch</strong> attribute

{%codeblock%}
 <input type="email" id="one" required data-errormessage-value-missing="Something's missing" data-errormessage-type-mismatch="Invalid!">
{%endcodeblock%}
		6 - For showing pattern missmatch, set custome error message on attribute <strong>data-errormessage-pattern-mismatch</strong>
<br/>
		there are some other attributes also, that we can use to set specific error messages 

{%codeblock%}
data-errormessage-too-long
data-errormessage-range-underflow
data-errormessage-range-overflow
data-errormessage-step-mismatch
data-errormessage-custom-error
{%endcodeblock%}
	</div>
</div>