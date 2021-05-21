---
title: Vanilla js direct upload to S3
date: 2021-05-20
---

# Vanilla js direct upload to S3

I couldn't find a great guide for getting s3 direct uploadings working with vanilla js and `fetch` so here is one

I use [shrine](https://github.com/shrinerb/shrine) for the presigning but anything will work.

## S3 setup

Don't forget to set the [CORS stuff](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ManageCorsUsing.html#cors-example-2) and any bucket settings you want for your bucket, make sure to allow `PutObject` at least.

Here's my CORS config:

```json
[
    {
        "AllowedHeaders": [
            "Authorization",
            "x-amz-date",
            "x-amz-content-sha256",
            "content-type",
            "content-disposition"
        ],
        "AllowedMethods": [
            "GET",
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "x-amz-server-side-encryption",
            "x-amz-request-id",
            "x-amz-id-2",
            "ETag"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

## HTML

Here's the html, it's just a form with a file upload

```html
<form method="POST">
  <input type="file" />
</form>
```

## Javascript

Here's the good part, the whole point of this post. It's not the best code but it does work:

```js
const s3 = {
  upload: function(file, data) {
    let formData = new FormData();

    const keys = Object.keys(data.fields);
    keys.forEach(k => {
      formData.append(k, data.fields[k]);
    });
    formData.append("file", file);

    return fetch(data.url, {
      method: data.method,
      body: formData
    })
    .then(res => console.log(res))
    .catch(error => console.log(error))
  },

  presignedUrl: function(file) {
    return fetch('/s3-presigned-url', {
      method: 'post',
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        filename: file.name,
        contentType: file.type
      })
    })
    .then(response => response.json())
    .then(data => data);
  },

  uploadFrom: function(selector) {
    const input = document.querySelector(selector);
    input.addEventListener('change', (e) => {
      const file = e.target.files[0]

      this.presignedUrl(file)
          .then(data => {
            this.upload(file, data)
          })
    })
  }
}

s3.uploadFrom('input[type="file"]')
```

This is no frills, no upload indicator, no pause/resume. Nothing, just straight upload to s3.

I'll show you the backend but it's not really interesting unless you're using `shinerb`:

## Backend

Here's the shrine config:

```ruby
require "shrine"
require "shrine/storage/s3"

s3_options = {
  bucket:            ENV["AWS_S3_BUCKET"],
  access_key_id:     ENV["AWS_ACCESS_KEY_ID"],
  secret_access_key: ENV["AWS_SECRET_ACCESS_KEY"],
  region:            ENV["AWS_REGION"]
}

Shrine.storages = {
  cache: Shrine::Storage::S3.new(prefix: "cache", **s3_options),
  store: Shrine::Storage::S3.new(**s3_options),
}

Shrine.plugin :sequel
Shrine.plugin :cached_attachment_data
Shrine.plugin :restore_cached_data
Shrine.plugin :rack_file
```

And here's the hilariously short presign code:

```ruby
is "s3-presigned-url" do
  Shrine.storages[:store].presign(params["filename"], method: :post, content_type: params["contentType"])
end
```

Hopefully this helps someone out there!

Oh and if it does help you, buy me a coffee! No I'm kidding.
