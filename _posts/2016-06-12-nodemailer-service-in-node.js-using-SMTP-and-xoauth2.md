---
layout: post
title: Nodemailer Service in Node.js using SMTP and xoauth2
description: Nodemailer Service in Node.js using SMTP and xoauth2
keywords: nodejs, nodemailer, express, smtp, xoauth2
categories: [nodemailer, node, express]
---

In my [previous post](/2012/09/15/node-module-to-manipulate-images-and-upload-to-rackspace/) I described how to use node-imager to upload images (with different presets) to rackspace cloudfiles. Today I have added support for amazon S3.

To use S3 as your storage, add the below in the imager config

```js
storage: {
  S3: {
    key: 'API_KEY',
    secret: 'SECRET',
    bucket: 'BUCKET_NAME'
  }
}
```

and specify `S3` while initializing imager

```js
var imager = new Imager(imagerConfig, 'S3')
```

---

## Resources:

* [node-imager on github](https://github.com/madhums/node-imager)
