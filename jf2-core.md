# Syntax

## Reserved Keywords
The following keywords are reserved and cannot be used as property names in vocabularies.

* type
* children
* content-type
* value

## Posts

### Post Objects
A post is composed of a "type" property, and one or more additional properties that describe the post.

The "type" property has a value that describes the vocabulary of this post. Common values include "entry", "card", etc. See [Microformats 2 vocabularies](http://microformats.org/wiki/microformats-2#v2_vocabularies) for the full list.

Any additional properties in the post object are considered part of the post's vocabulary.

### Post Properties

The list of valid post properties is defined by the vocabularies. This allows new vocabularies to be developed outside the development of the syntax.

Most values will be strings. If a property (such as `author`) references another object and wants to include other data about the object, the value of the property is a JSON object with a property "url" referencing the object and other properties that belong to the original object. Consumers that encounter an object with a URL on a different domain should not trust the embedded data and should fetch it from the external domain.

Values may also be arrays if the vocabulary allows for multiple values of the property.

### Example Post

```json
{
  "type": "entry",
  "published": "2015-10-20T15:49:00-0700",
  "url": "http://example.com/post/fsjeuu8372",
  "author": {
    "type": "card",
    "name": "Alice",
    "url": "http://alice.example.com",
    "photo": "http://alice.example.com/photo.jpg"
  },
  "name": "Hello World",
  "content": "This is a blog post"
}
```

```json
{
  "type": "entry",
  "published": "2015-10-20T15:49:00-0700",
  "url": "http://example.com/like/r23eugi02c",
  "author": {
    "type": "card",
    "name": "Alice",
    "url": "http://alice.example.com",
    "photo": "http://alice.example.com/photo.jpg"
  },
  "like-of": "http://bob.example.com/post/100"
}
```

### Author

An author is represented by the h-card vocabulary, and consists of a name, photo URL, URL to the author profile, and [others](http://microformats.org/wiki/h-card). This is represented by the following JSON.

```json
{
  "type": "card",
  "name": "Aaron Parecki",
  "photo": "http://aaronparecki.com/photo.jpg",
  "url": "http://aaronparecki.com/"
}
```

### HTML Content
By default, any string value is to be interpreted as literal plaintext. This means when displaying a string in an HTML page, it must be HTML escaped.

If the value of a property should be interpreted as HTML, it should be enclosed in an object noting its content type as follows.

```json
{
  "type": "entry",
  "content": {
    "content-type": "text/html",
    "value": "<b>Hello World</b>"
  }
}
```

#### Multiple URLs for video/audio/picture
Since HTML video/audio/picture tags may have multiple URLs, we need a way to convey this information in the JSON representation.

```html
<div class="h-entry">
  <video class="u-video" width="640" height="360" preload controls>
    <source src="sample_h264.mov" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"' />
    <source src="sample_ogg.ogv" type='video/ogg; codecs="theora, vorbis"' />
    <source src="sample_webm.webm" type='video/webm; codecs="vp8, vorbis"' />
  </video>
</div>
```

```json
{
  "type": "entry",
  "video": [
    {
      "content-type": "video/mp4",
      "url": "sample_h264.mov"
    },
    {
      "content-type": "video/ogg",
      "url": "sample_ogg.ogg"
    },
    {
      "content-type": "video/webm",
      "url": "sample_webm.webm"
    }
  ]
}
```

## Collections

Posts can live inside of collections. A collection may be a home page feed, or a feed of other posts such as a list of contacts, a list of things someone has liked, etc. There is no requirement that all posts in a collection need to be of the same type.

The collection may also have its own properties such as "name" or "author". 

```json
{
  "type": "feed",
  "url": "http://alice.example.com/collectionurl",
  "name": "Alice's Home Page",
  "author": {
    "type": "card",
    "name": "Alice",
    "url": "http://alice.example.com",
    "photo": "http://alice.example.com/photo"
  },
  "children": [
    { ... },
    { ... }
  ]
}
```

### Multiple items on a page
If an HTML page contains multiple top-level items, (most commonly found when a page contains a list of h-entrys), the parser creates an implicit top-level collection with no properties.

```json
{
  "children": [
    { ... },
    { ... }
  ]
}
```

## Alternative

Using `@type` and `@id` instead of `type` and `url` lets people use JSON-LD if they want, but can ignore it otherwise. This is how the [annotations group](http://www.w3.org/TR/annotation-model/#annotations) has written their spec.

```json
{
  "@type": "entry",
  "@id": "http://example.com/post/fsjeuu8372",
  "published": "2015-10-20T15:49:00-0700",
  "author": {
    "@type": "card",
    "@id": "http://alice.example.com",
    "name": "Alice",
    "photo": "http://alice.example.com/photo.jpg"
  },
  "name": "Hello World",
  "content": "This is a blog post"
}
```

## Deriving the Syntax

This syntax is derived from HTML with Microformats2, converted to JSON, converted to a simplified JSON. The examples below illustrate the process.

## Note

### HTML + Microformats
```html
<article class="h-entry">
  <h1 class="p-name">Hello World</h1>
  <p>Published by <a class="p-author h-card" href="http://example.com/">A. Developer</a>
     on <a href="http://example.com/2015/10/21" class="u-url"><time class="dt-published" datetime="2015-10-21T12:00:00-0700">October 21<sup>st</sup>, 2015</time></a>
 
  <p class="p-summary">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus imperdiet ultrices pulvinar.</p>
 
  <div class="e-content"><p>Donec dapibus enim lacus, <i>a vehicula magna bibendum non</i>. Phasellus id lacinia felis, vitae pellentesque enim. Sed at quam dui. Suspendisse accumsan, est id pulvinar consequat, urna ex tincidunt enim, nec sodales lectus nulla et augue. Cras venenatis vehicula molestie. Donec sagittis elit orci, sit amet egestas ex pharetra in.</p></div>
</article>
```

### Parsed Microformats JSON

```json
{
  "items": [
    {
      "type": [
        "h-entry"
      ],
      "properties": {
        "author": [
          {
            "type": [
              "h-card"
            ],
            "properties": {
              "name": [
                "A. Developer"
              ],
              "url": [
                "http:\/\/example.com\/"
              ]
            },
            "value": "A. Developer"
          }
        ],
        "name": [
          "Hello World"
        ],
        "summary": [
          "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus imperdiet ultrices pulvinar."
        ],
        "url": [
          "http:\/\/example.com\/2015\/10\/21"
        ],
        "published": [
          "2015-10-21T12:00:00-0700"
        ],
        "content": [
          {
            "html": "<p>Donec dapibus enim lacus, <i>a vehicula magna bibendum non<\/i>. Phasellus id lacinia felis, vitae pellentesque enim. Sed at quam dui. Suspendisse accumsan, est id pulvinar consequat, urna ex tincidunt enim, nec sodales lectus nulla et augue. Cras venenatis vehicula molestie. Donec sagittis elit orci, sit amet egestas ex pharetra in.<\/p>",
            "value": "Donec dapibus enim lacus, a vehicula magna bibendum non. Phasellus id lacinia felis, vitae pellentesque enim. Sed at quam dui. Suspendisse accumsan, est id pulvinar consequat, urna ex tincidunt enim, nec sodales lectus nulla et augue. Cras venenatis vehicula molestie. Donec sagittis elit orci, sit amet egestas ex pharetra in."
          }
        ]
      }
    }
  ]
}
```

### Simplified JSON

```json
{
  "type": "entry",
  "author": {
    "type": "card",
    "url": "http://example.com",
    "name": "A. Developer"
  },
  "url": "http://example.com/2015/10/21",
  "published": "2015-10-21T12:00:00-0700",
  "name": "Hello World",
  "summary": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus imperdiet ultrices pulvinar.",
  "content": "Donec dapibus enim lacus, a vehicula magna bibendum non. Phasellus id lacinia felis, vitae pellentesque enim. Sed at quam dui. Suspendisse accumsan, est id pulvinar consequat, urna ex tincidunt enim, nec sodales lectus nulla et augue. Cras venenatis vehicula molestie. Donec sagittis elit orci, sit amet egestas ex pharetra in."
}
```

## Context

current working json-ld context can be found at https://github.com/dissolve/jf2/blob/master/jf2-context.jsonld

