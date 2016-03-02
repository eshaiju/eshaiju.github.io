---
layout: post
title: "Spree - Add extra fields to product model with deface and decorator"
date: 2015-01-08 14:13:49 +0530
comments: true
categories: 
- Spree 
- Ruby On Rails
---



<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">

<a href="https://spreecommerce.com">Spree</a> is a fully-featured e-commerce solution that can be easily integrated into a Rails application. If you need to turn a Rails app into a store that sells products then Spree is one of the quickest ways to do this. We can customise all the built-in features in Spree and can also and new features and fields to Spree models.
<br/>
<br/>
In this post, I am discussing adding an extra field for the uploading company logo into products. For customising view of product form we can use <a href ="https://github.com/spree/deface">Deface</a>. To add <strong>has_attached_file</strong> relation to product model we use <strong>decorator</strong>.
<br/>

First we need to create migration for adding company logo field to <strong>spree_products</strong> table.
{%codeblock%}
class AddCompanyLogoToSpreeProducts < ActiveRecord::Migration
  def change
    add_attachment :spree_products, :company_logo
  end
end
{%endcodeblock%}
To add <strong>has_attached_file</strong> relation to product model, add product decorator to file <strong>app/model/spree/product_decorator.rb</strong>
{%codeblock%}
Spree::Product.class_eval do
  has_attached_file :company_logo, :styles => { :medium => "300x300>", :thumb => "100x100>" }, :default_url => "/images/:style/missing.png"
  validates_attachment_content_type :company_logo, :content_type => /\Aimage\/.*\Z/
end
{%endcodeblock%}
Now we only can add file upload filed to product form using deface. 
<br/>

Deface is a standalone Rails library that enables you to customize Erb templates without needing to directly edit the underlying view file. Deface allows you to use standard CSS3 style selectors to target any element (including Ruby blocks), and perform an action against all the matching elements
<br/>
Add deface code into <strong>app/overrides/company_logo_to_admin_product.rb</strong>
{%codeblock%}
Deface::Override.new(
    :virtual_path   => "spree/admin/products/_form",
    :name           => "company_logo_to_admin_product",
    :insert_bottom  => "[data-hook='admin_product_form_additional_fields']",
    :text           => "
			<p>
			      <%= f.label :company_logo %>
			      <%= f.file_field :company_logo %>
			</p>
"
)
{%endcodeblock%}
Thats it , now we can start uploading company logo to each product
	</div>
</div>