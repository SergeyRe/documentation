---
category: api-client
position: 3
title: Ruby client
layout: page.ejs
---

We released a Ruby gem to make it easy to programmatically read/create/edit/destroy DatoCMS records. Add this line to your application's Gemfile and then install the gem running `bundle install` within your terminal:

```ruby
gem 'dato'
```

<div class="note">
**Warning** Due to historical reasons and backward compatibility, the API exposes some different naming compared to the rest of the product: Models are called Item Types, while Records are called Items. Keep that in mind!
</div>

The first step is to require the DatoCMS gem, and initialize the client with the read-write API token you can find under the *Admin area > API tokens* section. Let's create an `import.rb` file with the following content:

```ruby
require "dato"

# create a DatoCMS client
client = Dato::Site::Client.new("YOUR_API_READWRITE_TOKEN")
```

Now, suppose we have an administrative area with an *Article* model, and we want to import a list of articles ie. from a JSON file:

<div class="small">
![foo](/images/import/article.png)
</div>

The first thing to know is the ID of the model itself. Let's add the following line to pretty print the existing models:

```ruby
require "dato"
require "pp"

# create a DatoCMS client
client = Dato::Site::Client.new("YOUR_API_READWRITE_TOKEN")

# inspect the list of models
pp client.item_types.all
```

Executing the script the following output appears:

```
$ ruby import.rb
[
  {
    "id"             => "7149",
    "name"           => "Article",
    "singleton"      => false,
    "sortable"       => false,
    "api_key"        => "article",
    "fields"         => ["27669", "27667", "27668"],
    "singleton_item" => nil
  }
]
```

We can also inspect the fields contained in the model:

```
# create a new Article record
pp client.fields.all("7149")

# Output:
#
# [
#   {
#     "id"          => "27667",
#     "label"       => "Title",
#     "field_type"  => "string",
#     "api_key"     => "title",
#     "hint"        => nil,
#     "localized"   => false,
#     "validators"  => {},
#     "position"    => 2,
#     "appeareance" => {"type"=>"title"},
#     "item_type"   => "7149"
#   },
#   {
#     "id"          => "27668",
#     "label"       => "Content",
#     "field_type"  => "text",
#     "api_key"     => "content",
#     "hint"        => nil,
#     "localized"   => false,
#     "validators"  => {},
#     "position"    => 3,
#     "appeareance" => {"type"=>"wysiwyg"},
#     "item_type"   => "7149"
#   },
#   {
#     "id"          => "27669",
#     "label"       => "Cover image",
#     "field_type"  => "image",
#     "api_key"     => "cover_image",
#     "hint"        => nil,
#     "localized"   => false,
#     "validators"  => {},
#     "position"    => 1,
#     "appeareance" => {},
#     "item_type"   => "7149"
#   }
# ]
```

Great, here there are our three fields. Let's create our article:

```ruby
# create a new Article record
pp client.items.create(
  item_type: "7149",
  title: "My first article!",
  content: "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed eiusmod.",
  cover_image: client.upload_image("http://i.giphy.com/NXOF5rlaSXdAc.gif")
)

# Output:
# {
#  "id"          => "43846",
#  "updated_at"  => "2017-03-20T13:25:11.707Z",
#  "is_valid"    => true,
#  "title"       => "My first article!",
#  "content"     => "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed eiusmod.",
#  "cover_image" => {
#    "path"   => "/932/1490016285-nxof5rlasxdac-gif",
#    "width"  => 432,
#    "height" => 250,
#    "format" => "gif",
#    "size"   => 5475424,
#    "alt"    => nil,
#    "title"  => nil
#  },
#  "item_type"=>"7149"
# }
```

As you can see, we use the helper method `client.upload_image` to pass DatoCMS the image to upload.

### List of client methods

Here's the complete list of methods available to you:

```ruby
### Records ###

# Create a new record
client.items.create(resource_attributes)

# Update an existing record
client.items.update(item_id, resource_attributes)

# Return a collection of items
client.items.all(filters = {})

# Return a specific item
client.items.find(item_id)

# Destroy an existing item
client.items.destroy(item_id)

# Helper methods to upload a file/image: to be used togheter with 
# `client.items.create` or `client.items.update`
client.upload_image(path_or_url)
client.upload_file(path_or_url)


### Fields ###

# Create a new field on the specified model
client.fields.create(item_type_id, resource_attributes)

# Update an existing field
client.fields.update(field_id, resource_attributes)

# Return all the fields of the specified model
client.fields.all(item_type_id)

# Return a specific field
client.fields.find(field_id)

# Destroy an existing field
client.fields.destroy(field_id)


### Models ###

# Create a new model
client.item_types.create(resource_attributes)

# Update an existing model
client.item_types.update(item_type_id, resource_attributes)

# Return all the models of the administrative area
client.item_types.all

# Return a specific model
client.item_types.find(item_type_id)

# Destroy an existing model
client.item_types.destroy(item_type_id)


### Editors ###

# Create a new editor
client.users.create(resource_attributes)

# Return all the editors of the administrative area
client.users.all

# Return a specific editor
client.users.find(user_id)

# Destroy an existing editor
client.users.destroy(user_id)
```

