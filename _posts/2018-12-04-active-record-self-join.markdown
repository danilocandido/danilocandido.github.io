---
lang: pt-br
layout: post
title:  "ActiveRecord and self-join association"
date:   2018-12-04 14:00:00 -0300
categories: jekyll update
---

## Objective
Build a model layer of an file system, creating files and directories and persisting into a SQL database.  
- We can have folders, sub-folders and files
- We'll also use Active Storage
- JS-Tree lib javascript

### Prerequisites
ruby >= 2.5

## Creating our rails project
We are going to skip test(We will use rspec), turbolinks, action-mailer and coffee-script
> rails new click-file -T --skip-turbolinks --skip-action-mailer --skip-coffee

### Configurations
Open the Gemfile to add gems

``` ruby
group :development, :test do
  gem 'rspec-rails', '~> 3.8'
  gem 'factory_bot_rails'
  gem 'rails-controller-testing'
end
```

Then it's time to add yarn dependencies
```
yarn add jquery
```

app/assets/javascripts/application.js
``` 
//= require jquery
```

### Let's create our model FileSystem
> rails generate model file_system parent:references name file:boolean

Don't forget to add index to the column name and put a default in the boolean column.

``` ruby
# db/migrate/20181204185142_create_file_systems.rb
class CreateFileSystems < ActiveRecord::Migration[5.2]
  def change
    create_table :systems do |t|
      t.references :parent, foreign_key: true
      t.string :name, index: true
      t.boolean :file, null: false, default: true

      t.timestamps
    end
  end
end
```

Now we'll edit our file_system.rb class

``` ruby
# app/models/file_system.rb
class FileSystem < ApplicationRecord
  # this is the self-join in Model
  belongs_to :folder, class_name: 'FileSystem', optional: true
  has_many :children, class_name: 'FileSystem', foreign_key: 'parent_id'

  has_one_attached :attached_file

  scope :roots, -> { where('parent_id is null') }

  validate :only_folder_should_have_children

  def text
    name || attached_file.filename
  end

  def children?
    children.exists?
  end

  def folder?
    !file
  end

  private

  def only_folder_should_have_children
    if file? && folder.try(:file?)
      errors.add(:parent_id, 'A file cannot have children')
    end
  end
end

```

## Now let's explain what we just did

### belongs_to :folder
A file or directory normally belongs to another folder.  
  - my_music (directory)
    - fear_od_the_dark.mp3 (file, parent=my_music)
    - fear_od_the_dark.mp3 (file, parent=my_music)


``` ruby
belongs_to :folder, class_name: 'FileSystem', optional: true
```
A folder/file belongs to a Model (FileSystem - itself)

### has_many :children
As a folder/directory our model can have children, that are sub-folders or files into it.
With `has_many` we did a relation with its children, in other words, models who has a parent (parent_id)

``` ruby
has_many :children, class_name: 'FileSystem', foreign_key: 'parent_id'
```

### your FileSystemController
``` ruby
# app/controllers/file_systems_controller.rb
class FileSystemsController < ApplicationController
  def index; end

  def show_files
    file_systems = FileSystem.roots
    render json: file_systems
  end
end
```

## Routes
``` ruby
Rails.application.routes.draw do
  root to: 'file_systems#index'

  resources :file_systems, only: :index
  get '/file_systems/show_modal/:id', to: 'file_systems#show_modal', as: :show_modal_upload
end
```

## The FrontEnd
In the frontend as I said above, we will use JS-Tree CND

add these scripts to your app/views/layouts/application.html.erb  

``` HTML
<head>
  <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/jstree/3.3.5/themes/default/style.min.css" />
  <script src="//cdnjs.cloudflare.com/ajax/libs/jstree/3.3.5/jstree.min.js"></script>
</head>
```

open your app/views/file_systems/index.html.erb

> show_files_file_systems_path is your route path

``` HTML
<div id="js_tree"></div>
<script>
  $(function() {
    $('#js_tree').jstree({
      'core' : {
        'data' : {
          "url" : "<%= show_files_file_systems_path %>",
          "dataType" : "json"
        }
      }
    });
  });
</script>
```

open http://localhost:3000  

<blockquote class="imgur-embed-pub" lang="en" data-id="kRcszOn"><a href="//imgur.com/kRcszOn">View post on imgur.com</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>


You can see the whole project on [GitHub](https://github.com/danilocandido/click-file)  

## Referências
[Check this project on GitHub](https://github.com/danilocandido/click-file)  
[Active Storage](https://edgeguides.rubyonrails.org/active_storage_overview.html)  
[Active Record Basics](https://guides.rubyonrails.org/active_record_basics.html)  