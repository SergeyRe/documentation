---
layout: page.ejs
category: jekyll
position: 4
title: Retrieve fields values
---

Once you have obtained a record object, you can access the value associated to a field using its *Field ID*:

<div class="two">
  <div>![foo](/images/edit-field-dialog.png)</div>
  <div>![foo](/images/edit-field-button.png)</div>
</div>

In this case, the field ID is `title`, so we can use it as a method on the record object to retrieve its value:

```ruby
blog_post = dato.blog_posts.first     # get the first blog post of the collection

blog_post.title                       # => "Hello world!"
```

Each record also exposes some additional methods you can use:

```ruby
blog_post.id                  # returns the record ID:
                              # => "1242"

blog_post.item_type           # returns an object representing the model:
blog_post.item_type.id        # => "44"
blog_post.item_type.name      # => "Blog post"
blog_post.item_type.api_key   # => "blog_post"

blog_post.updated_at          # returns last modified date:
                              # => <Time value="2017-01-24 10:41:55 +0100">

blog_post.position            # returns its ordinal number in the collection
                              # (only if the model is sortable or a tree):
                              #
                              # => 13

blog_post.parent              # returns the parent record (only if the model is a tree)
blog_post.children            # returns the children records (only if the model is a tree)

blog_post.to_hash             # returns an hash containing the above, plus all the
                              # fields's values:
                              #
                              # => {
                              #   id: "1242",
                              #   item_type: "blog_post",
                              #   updated_at: <Time value="2017-01-24 10:41:55 +0100">,
                              #   title: "Hello world!",
                              #   ...
                              # }
```

Most field types return scalar values (integers, booleans, strings, etc.), but some other return more complex structures.

Keep in mind that the `.toMap()` method will limit the results to a maximum depth of 3. You can change this setting passing an explicit depth:

```
blog_post.to_hash(10)   // this will go down 10 levels deep!
```

---

### File attachment fields


*File attachment* fields expose the following methods. The `.url()` method is the most important one, as it returns the full URL of the file.

```ruby
blog_post.attachment.url       # returns the file URL:
                               # => "https://www.datocms-assets.com/123/12345-report.pdf"

blog_post.attachment.size      # returns the filesize in bytes:
                               # => 1489134

blog_post.attachment.format    # returns the extension:
                               # => "pdf"

blog_post.attachment.to_hash   # returns an hash containing all the above:
                               #
                               # => {
                               #   size: 1489134,
                               #   format: "pdf",
                               #   url: "https://www.datocms-assets.com/123/12345-report.pdf"
                               # }
```

---

### Image fields

*Image fields* share all the methods of *file attachment* fields, but they also expose some additional methods. The `.url()` method is the most important one, as it returns the full URL of the image, and can take a number of image transformation parameters (see the [Image Manipulation](./image-manipulation.html) chapter for all for the details).

```ruby
blog_post.cover_image.url        # returns the file URL:
                                 # => "https://www.datocms-assets.com/123/12345-heart.png"

blogPost.coverImage.url(         # returns the image URL, cropped at 150x150px:
  w: 150,                        # => "https://www.datocms-assets.com/123/12345-heart.png?w=150&h=150&fit=crop"
  h: 150,
  fit: "crop",
)    

blog_post.cover_image.size       # returns the filesize in bytes:
                                 # => 168131

blog_post.cover_image.format     # returns the extension:
                                 # => "png"

blog_post.cover_image.width      # returns the image width:
                                 # => 800

blog_post.cover_image.height     # returns the image height:
                                 # => 600

blog_post.cover_image.alt        # returns the image alternative text:
                                 # => "Heart icon"

blog_post.cover_image.title      # returns the image title:
                                 # => "We love our clients"

blog_post.cover_image.to_hash    # returns an hash containing all the above:
                                 #
                                 # => {
                                 #   size: 168131,
                                 #   format: "png",
                                 #   width: 800,
                                 #   height: 600,
                                 #   url: "https://www.datocms-assets.com/123/12345-heart.png"
                                 # }
```

---

### Image gallery fields

*Image gallery* fields simply return an array of image objects:

```ruby
blog_post.gallery.each do |image|
  image.title   # => "We love our clients"
  image.url     # => "https://www.datocms-assets.com/123/12345-heart.png"
end
```

---

### Color fields

*Color* fields expose the following methods:

```javascript
blog_post.color.red        # => 255
blog_post.color.green      # => 127
blog_post.color.blue       # => 0
blog_post.color.alpha      # => 1.0
blog_post.color.rgb        # => "rgb(255, 127, 0)"
blog_post.color.hex        # => "#ff7f00"

blog_post.color.to_hash    # => {
                           #  red: 255,
                           #  green: 127,
                           #  blue: 0,
                           #  alpha: 1.0,
                           #  rgb: "rgb(255, 127, 0)",
                           #  hex: "#ff7f00"
                           # }
```

---

### Geolocation fields

*Geolocation* fields expose the following methods:

```ruby
blog_post.position.latitude    # => 41.90278349999999
blog_post.position.longitude   # => 12.496365500000024

blog_post.position.to_hash     # => {
                               #  latitude: 41.90278349999999,
                               #  longitude: 12.496365500000024
                               # }
```

---

### Video fields

*Video* fields expose the following methods:

```ruby
blog_post.video.title          # => "Nyan Cat"
blog_post.video.url            # => "https://www.youtube.com/watch?v=QH2-TGUlwu4&t=11s"
blog_post.video.thumbnail_url  # => "https://i.ytimg.com/vi/QH2-TGUlwu4/hqdefault.jpg"
blog_post.video.provider       # => "youtube"
blog_post.video.provider_uid   # => "QH2-TGUlwu4"
blog_post.video.height         # => 344
blog_post.video.width          # => 459

blog_post.video.to_hash        # => {
                               #  title: "Nyan Cat",
                               #  url: "https://www.youtube.com/watch?v=QH2-TGUlwu4&t=11s",
                               #  thumbnail_url: "https://i.ytimg.com/vi/QH2-TGUlwu4/hqdefault.jpg",
                               #  provider: "youtube",
                               #  provider_uid: "QH2-TGUlwu4",
                               #  height: 344,
                               #  width: 459
                               # }
```

---

### SEO meta tags fields

*SEO meta tags* fields expose the following methods (although it's far easier to generate SEO meta tags [using the `.seo_meta_tags` method](/jekyll/seo.html):

```ruby
blog_post.seo.title         # => "Article title"
blog_post.seo.description   # => "Lorem ipsum dolor sit amet, consectetur..."
blog_post.seo.image         # => returns a full image object (see `Media fields` chapter)

blog_post.seo.to_hash       # => {
                            #   title: "Article title",
                            #   description: "Lorem ipsum dolor sit amet, consectetur...",
                            #   image: {
                            #     size: 168131,
                            #     format: "png",
                            #     width: 800,
                            #     height: 600,
                            #     url: "https://www.datocms-assets.com/123/12345-heart.png"
                            #   }
                            # }
```
