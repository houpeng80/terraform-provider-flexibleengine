---
subcategory: "Object Storage Service (OSS)"
description: ""
page_title: "flexibleengine_s3_bucket_object"
---

# flexibleengine_s3_bucket_object

Provides a S3 bucket object resource.

## Example Usage

### Uploading a file to a bucket

```hcl
resource "flexibleengine_s3_bucket_object" "object" {
  bucket = "your_bucket_name"
  key    = "new_object_key"
  source = "path/to/file"
  etag   = md5(file("path/to/file"))
}

resource "flexibleengine_s3_bucket" "examplebucket" {
  bucket = "examplebuckettftest"
  acl    = "private"
}

resource "flexibleengine_s3_bucket_object" "examplebucket_object" {
  key        = "someobject"
  bucket     = flexibleengine_s3_bucket.examplebucket.bucket
  source     = "index.html"
}
```

### Server Side Encryption with S3 Default Master Key

```hcl
resource "flexibleengine_s3_bucket" "examplebucket" {
  bucket = "examplebuckettftest"
  acl    = "private"
}

resource "flexibleengine_s3_bucket_object" "examplebucket_object" {
  key                    = "someobject"
  bucket                 = flexibleengine_s3_bucket.examplebucket.bucket
  source                 = "index.html"
  server_side_encryption = "aws:kms"
}
```

## Argument Reference

-> **Note:** If you specify `content_encoding` you are responsible for encoding the body appropriately
  (i.e. `source` and `content` both expect already encoded/compressed bytes)

The following arguments are supported:

* `bucket` - (Required) The name of the bucket to put the file in.
* `key` - (Required) The name of the object once it is in the bucket.
* `source` - (Required) The path to the source file being uploaded to the bucket.
* `content` - (Required unless `source` given) The literal content being uploaded to the bucket.

* `acl` - (Optional) The [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl) to apply.
  Defaults to "private".

* `cache_control` - (Optional) Specifies caching behavior along the request/reply chain Read [w3c cache_control](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
  for further details.

* `content_disposition` - (Optional) Specifies presentational information for the object. Read [wc3 content_disposition](http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1)
  for further information.

* `content_encoding` - (Optional) Specifies what content encodings have been applied to the object and thus what decoding
  mechanisms must be applied to obtain the media-type referenced by the Content-Type header field.
  Read [w3c content encoding](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11) for further information.

* `content_language` - (Optional) The language the content is in e.g. en-US or en-GB.

* `content_type` - (Optional) A standard MIME type describing the format of the object data, e.g. application/octet-stream.
  All Valid MIME Types are valid for this input.

* `website_redirect` - (Optional) Specifies a target URL for [website redirect](http://docs.aws.amazon.com/AmazonS3/latest/dev/how-to-page-redirect.html).

* `etag` - (Optional) Used to trigger updates. The only meaningful value is `${md5(file("path/to/file"))}`.
  This attribute is not compatible with `kms_key_id`.

* `server_side_encryption` - (Optional) Specifies server-side encryption of the object in S3.
  Valid values are "`AES256`" and "`aws:kms`".

Either `source` or `content` must be provided to specify the bucket content.
These two arguments are mutually-exclusive.

## Attributes Reference

The following attributes are exported

* `id` - the `key` of the resource supplied above
* `etag` - the ETag generated for the object (an MD5 sum of the object content).
* `version_id` - A unique version ID value for the object, if bucket versioning
is enabled.
