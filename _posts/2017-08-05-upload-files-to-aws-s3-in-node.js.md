---
layout: post
title: Upload files to AWS S3 in Node.js
description: Upload files to AWS S3 in Node.js
keywords: node.js, express, s3
categories: [node.js, aws s3, express]
---

![screenshot](../../../../images/aws-s3/image1.jpg?raw=true)

Hello Guys!
I have received very good response from my previous articles, which encourages me to write more about technology stuff. This time, I too have something interesting to share with you. Now a days, I am working on AWS services (API Gateway, Lambda Function, DynamoDb, S3 etc.). In this story, I will explain How to upload files to AWS server (S3).
What we are going to use here:

* Node.js
* Express Framework
* AWS Service

I assume, you all are aware with Node.js, Express Framework and Mongodb. If not, please have a look on my previous posts:

1. [Node.js Single File Application using Express Framework.](http://thepandeysoni.org/2016/05/02/node.js-single-file-application-using-express-framework/)
1. [CRUD Operations in Node.js Application (Expresss Framework) using mongodb.](http://thepandeysoni.org/2016/05/04/CRUD-operations-in-node.js-application-(expresss=framework)-using-mongodb/)

These articles will atleast give you an overview about it.

**_What is Nodemailer Package?_**
Send e-mails with Node.JS — easy as cake! [Nodemailer](http://nodemailer.com/)
Copyright © Nodemailer
Here, we will create application which will do following things only:
1. Nodemailer setup using SMTP (here you will use username and password).
2. Nodemailer setup using xoauth2 (here you will need user, clientId, clientSecret, refreshToken, accessToken).
Project Structure will be like this:

1. Create your AWS account or Use IAM user (Why Need this ? => To get Access Key ID and Secret Access Key → will be required in development)
2. How to find your AWS Access Key ID and Secret Access Key?
Please go through How to find your AWS Access Key ID and Secret Access Key, and attach AmazonS3FullAccess policy to user. Please let me know if you have any doubt related to AWS configuration settings.
3. Create one config.json file, please check AWS Regions and Endpoints, to know more about regions. Keep this config.json file in root folder of the project.

```js
{ 
"accessKeyId": "YourAccessKey", 
"secretAccessKey": "YourSecretAccessKey",
"region": "us-west-1" 
} 
```

In this article, we are more focussed on file upload to AWS S3, and skip the details of Node.js (Express). To begin with, clone Node-Express-Boilerplate repository to start project in Node.js (Express Framework) and in root folder run:

```js
npm install && node server.js
```

You can see the port number in command prompt after sucessfull run. You can change the settings in server/config/config.js file.
Let’s move to route file into server folder, and define route path to use for API, as shown below:

```js
const Upload = require('./upload/upload.server.controller')
const multipart = require('connect-multiparty')
const multipartMiddleware = multipart()
module.exports = (app) => {
// API Server Endpoints
    app.get('/', Upload.displayForm)
    app.post('/upload', multipartMiddleware, Upload.upload)
}
```

We used multipart to get data into req.files and used multipart as a middleware. displayForm function used to show form to upload file and on submit call /upload post method.
Next, controller file: functional part to upload file to s3.

```js
const AWS = require('aws-sdk')
const async = require('async')
const bucketName = "pandeysoni"
const path = require('path')
const fs = require('fs')
let pathParams, image, imageName;

/** Load Config File */
AWS.config.loadFromPath('config.json')

/** After config file load, create object for s3*/
const s3 = new AWS.S3({region: 'us-west-1'})
const createMainBucket = (callback) => {
	// Create the parameters for calling createBucket
	const bucketParams = {
	   Bucket : bucketName
	};                    
	s3.headBucket(bucketParams, function(err, data) {
	   if (err) {
	   	console.log("ErrorHeadBucket", err)
	      	s3.createBucket(bucketParams, function(err, data) {
			   if (err) {
			   	console.log("Error", err)
			      callback(err, null)
			   } else {
			      callback(null, data)
			   }
			});
	   } else {
	      callback(null, data)
	   }
	})                             
}

const createItemObject = (callback) => {
  const params = { 
        Bucket: bucketName, 
        Key: `${imageName}`, 
        ACL: 'public-read',
        Body:image
    };
	s3.putObject(params, function (err, data) {
		if (err) {
	    	console.log("Error uploading image: ", err);
	    	callback(err, null)
	    } else {
	    	console.log("Successfully uploaded image on S3", data);
	    	callback(null, data)
	    }
	})  
}
exports.upload = (req, res, next) => {
	var tmp_path = req.files.file.path;
    // console.log("item", req.files.file)
	var tmp_path = req.files.file.path;
	image = fs.createReadStream(tmp_path);
    imageName = req.files.file.name;
    async.series([
        createMainBucket,
        createItemObject
        ], (err, result) => {
        if(err) return res.send(err)
        else return res.json({message: "Successfully uploaded"}) 
    })
}
exports.displayForm = (req, res) => {
    res.writeHead(200, {
        "Content-Type": "text/html"
    });
    res.write(
        '<form action="/upload" method="post" enctype="multipart/form-data">' +
        '<input type="file" name="file">' +
        '<input type="submit" value="Upload">' +
        '</form>'
    );
    res.end();
};
```
Upload post method call upload function of controller, where we create bucket(if not exist) and upload that file into that bucket. I have defined

```js
const bucketName = "pandeysoni"
```

**NOTE:**
**_Bucket name must not contain uppercase characters._**  
**_Bucket name must start with a lowercase letter or number._**  
**_Bucket name must be between 3 and 63 characters long._**  
**createMainBucket Function:**  
S3.headBucket: check if bucket already exists or not. If bucket does not exist, then we will go to error part and create bucket with the defined name of bucket in bucketName. if bucket already exists, then this function will not create any bucket.  
**createItemObject Function:**  
params variable contains- Bucket, Key, ACL, and Body.  
**Bucket** — bucket name, where you would like to keep your file.  
**Key** — file name (it will overwrite, if file name already exists. So please note that your file name should be unique to keep all records to S3).  
**ACL** — defines accessibility permission to read the file.  
**Body** — file data.  
and use putObject method to upload file to s3.   
Have a look to repository for complete working code, clone repository and run  

```js
npm install & node server
```
You can see the port number in command prompt after sucessfull run. Access [localhost:3000](localhost:3000)

Feel free to clone the full code for this post to see the full picture of how everything works together and customize it for your own needs and mark it starred, if it useful. Have a look to repository for complete working code.  
[source code on github](https://github.com/pandeysoni/file-upload-s3)   
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!  
Happy Coding!!