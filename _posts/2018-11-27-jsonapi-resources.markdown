---
lang: pt-br
layout: post
title:  "Rails-API and JSONAPI-Resources"
date:   2018-11-13 20:00:00 -0300
categories: jekyll update
---

## What's is *JSONAPI-Resources*?
JSONAPI-Resources is a gem that implements the pattern [json:api](https://jsonapi.org/), a specification for building apis in json.
It's easier to put in your Rails application.

## Pre-requisites
ruby >= 2

## Let's start
We will use rails-api only and skip tests(because RSspec will be used)  
> rails new json-api --api -T
> cd json-api  
> bundle install

Open your Gemfile and add these gems
> gem 'jsonapi-resources'   
  gem 'rack-cors'  

> group :development, :test do
    gem 'rspec-rails', '~> 3.8'
    gem 'factory_bot_rails'
  end

then run `bundle install` again to complete the instalation dependencies

![rails image server started](https://edgeguides.rubyonrails.org/images/getting_started/rails_welcome.png  "rails s" )

## The project
Change the inheritance from ApplicationController from `ActionController::API` to `JSONAPI::ResourceController`. 

Your controller should be like this replace `ActionController::API`
``` ruby
class ApplicationController < JSONAPI::ResourceController
  protect_from_forgery with: :null_session, if: Proc.new {|c| c.request.format.json? }
end
```

Generate a model
> rails g model page content
> rails db:migrate

Generate a controller 
> rails generate jsonapi:controller api::v1::page

app/controllers/api/v1/pages_controller.rb
here you'll have to use `ApplicationController` instead of `JSONAPI::ResourceController`

``` ruby
class Api::V1::PagesController < ApplicationController
end
```

## Create our resources directory
Create a resource for our model Page. But first create a directory.

> mkdir -p app/resources/api/v1

### create the resource itself
page_resource.rb

app/resources/api/v1/page_resource.rb
``` ruby
class Api::V1::PageResource < JSONAPI::Resource
  attributes :content
  model_name 'Page'
end
```

config/routes.rb
``` ruby
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do
      jsonapi_resources :pages, only: [:index, :create]
    end
  end
end
```

config/initializers/cors.rb
``` ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```

# Routes
rails routes
> GET   /api/v1/pages(.:format)  api/v1/pages#index
> POST  /api/v1/pages(.:format)  api/v1/pages#create

## calling api
  Header
      Content-Type: application/vnd.api+json
      Accept: application/vnd.api+json

GET  localhost:3000/api/v1/pages

--

POST localhost:3000/api/v1/pages
  Header
    Content-Type: application/vnd.api+json
    Accept: application/vnd.api+json

  body  
  ```
    {
      "data": {
        "type": "pages",
        "attributes": {
          "content": "http://en.wikipedia.org/"
        }
      }
    }
  ```


## Referências
[Json:api specification](https://jsonapi.org/)  
[Json API Ruby Implementation](https://github.com/cerebris/jsonapi-resources) 
[JSON API Easy Mode](https://www.hark.bz/blog/post/json-api-easy-mode/) 