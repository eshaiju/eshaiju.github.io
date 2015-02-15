---
layout: post
title: "Implement Dependent select in Active Admin"
date: 2015-02-15 14:53:50 +0530
comments: true
categories: 
- Ruby On Rails
- ActiveAdmin
---

<div class='post'>
	<div dir="ltr" style="text-align: left;" trbidi="on">
	One of my prevois project, I needed to populate product catalogs in second select box depends on the product selected in first select box in active admin input form. I implemented this by refering one of the example by <a href="https://github.com/abhidsm">@abidsm</a> in his github <a href="https://github.com/abhidsm/dependent-select">repo</a>. 
<br/>
<br/>
<strong>Implementation</strong>
<br/>
<br/>
First we need to add <a href="https://raw.githubusercontent.com/abhidsm/dependent-select/master/dependent_select.js">dependent_select.js</a> file.
<br/>

Now we need to add logic to <stong>catalogs_product.rb</stong> in admin folder.
{%codeblock catalogs_product.rb%}
ActiveAdmin.register CatalogsProduct do
  form do |f|
    f.inputs "Details" do
      f.input :product, :as => :select, :collection => Product.all.collect {|product| [product.name, product.id] }
      f.input :catalog, :as => :select, :input_html => {'data-option-dependent' => true, 'data-option-url' => '/products/:catalogs_product_product_id/catalogs', 'data-option-observed' => 'catalogs_product_product_id'}, :collection => (resource.product ? resource.product.category.catalogs.collect {|catalog| [catalog.attr_name, catalog.id]} : []) 
    end
    f.actions
  end
end
{%endcodeblock%}
Here <strong>'data-option-dependent' => true</strong> will bind depentent select js to catalog input field.
<br/><br/>
<strong>'data-option-observed' => 'catalogs_product_product_id'</strong> specify that, the catalog field is depending on input in product field. <strong>catalogs_product_product_id</strong> is the id of the product field in this form. 
<br/><br/>
Now we need to specify the json method which will populate catalogs data depends on the product is selected in parent input select. 
<br/>
<strong>'data-option-url' => '/products/:catalogs_product_product_id/catalogs'</strong>
<br/><br/>
Now we need to add index method with json output in catelogs controller.
{%codeblock catalogs_controller.rb%}
class CatalogsController < ApplicationController
  respond_to :json

  def index
    if params[:product_id]
      product = Product.find_by_id(params[:product_id])
      @catalogs = product.category.catalogs
    else
      @catalogs = Catalog.all
    end
    render :json => @catalogs.collect {|catalog| {:id => catalog.id, :name => catalog.attr_name} }
  end
end
{%endcodeblock%}
{%codeblock routes.rb%}
DependentSelect::Application.routes.draw do
  root :to => 'pages#main'
  resources :products do
    resources :catalogs
  end
end
{%endcodeblock%}

Thats it now you can see catelog select box will be repopulated when we select a product in product select box. This can be implemented in any of formastic input forms.
	</div>
</div>