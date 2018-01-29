---
layout: post
title: API Gateway First Application | Serverless
description: API Gateway First Application using Serverless Framework
keywords: nodejs, serverless, api_gateway
categories: [serverless, api gateway, node]
---

Here we will learn how to implement api-gateway. In last post, we just go through with the basics of [Serverless | Lambda Functions | API Gateway](https://thepandeysoni.org/2017/11/05/serverless-lambda-functions-api-gateway/). 
What we are going to use here:

* Node.js
* Serverless Framework
* AWS API Gateway

I assume, you all are aware with Node.js basics. If not, please have a look on my previous posts:

1. [Node.js Single File Application using Express Framework.](https://thepandeysoni.org/2016/05/02/node.js-single-file-application-using-express-framework/)
1. [CRUD Operations in Node.js Application (Expresss Framework) using mongodb.](https://thepandeysoni.org/2016/05/04/CRUD-operations-in-node.js-application-(expresss=framework)-using-mongodb/)

These articles will atleast give you an overview about the Node.js work-flow.

Here, we will create application which will do following things only:

1. Create new user via api-gateway function.

Project Structure will be like this:
There can be multilple other modules like user.

```js
+ functions
  + user.js
+ config.yml
+ serverless.yml
+ package.json
```

**1. Setting up Serverless Framework globallay to the system.**

```js

npm install -g serverless

```
We need to create serverless.yml file, this file is used, whenever we run:

```js

serverless deploy

```
Here, we kept aws config in serverless.yml and declared functions, which will create API end-point. Please take a look to the code, written in serverless.yml.

```js
service: user-service

provider:
  name: aws
  profile: default
  runtime: nodejs6.10
  region: ${self:custom.config.region}
  stage: ${self:custom.config.stage}
  memorySize: 128
  timeout: 20
  versionFunctions: false

functions:
  user-create:
    handler: functions/user.main
    events:
      - http:
          path: /
          method: post
          cors: true
custom:
  config: ${file(./config.yml)}
```

Give your own service name, i have choosen **_user-service_**.   
Provider properties details:  
 * name - We are using **_aws_**.  
 * profile - set profile on your system. Please check [link](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) for setup.  
 
 * runtime - AWS highest version supported till now | nodejs6.10  
 * region - In which region you want to deploy your functions. Please check [region list](https://docs.aws.amazon.com/general/latest/gr/rande.html).  
 
 * stage - it's upto you, I prefer dev | qc | prod.   
 * memorySize - It is optional, in MB, default is 1024. Minimum = 128 MB / Maximum = 3008 MB (with 64 MB increments). If the maximum memory use is exceeded, function invocation will be terminated. I keep it 128 MB.  
 * timeout - It is also optional, in seconds, default is 6. Maximum execution duration per request  300 seconds, I keep it 20 seconds max.  
 * versionFunctions - By default, the framework creates function versions for every deploy. This behavior is optional, and can be turned off in cases where you don't invoke past versions by their qualifier. I keep it as a false.  

If you are using AWS as a provider, all functions inside the service are AWS Lambda functions and all of the Lambda functions in your serverless service can be found in serverless.yml under the functions property. You can add as many functions as you want within this property. The code will look like this:

```js
functions:
  user-create:
    handler: functions/userCreate.main
    events:
      - http:
          path: users/create
          method: post
          cors: true
  user-get:
    handler: functions/userGet.main
    events:
      - http:
          path: users/get
          method: get
          cors: true
```
or
```js
functions:
  user-create:
    handler: functions/userCreate.main
    events:
      - http:
          path: users/create
          method: post
          cors: true
      - http:
          path: users/get
          method: get
          cors: true
```
Functions properties details:
 * handler - The handler property points to the file and module containing the code you want to run in your function.
 * events - All events in the service are anything in AWS that can trigger an AWS Lambda function, like an S3 bucket upload, an SNS topic, and HTTP endpoints created via API Gateway. Here, we are using HTTP endpoints created via API Gateway. The events property is an array, because it's possible for functions to be triggered by multiple events, as shown. You can set multiple Events per Function, as long as that is supported by AWS.

To deploy or update your Functions, Events and Infrastructure, run **_serverless deploy_**.  
Feel free to download the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to repository to get complete idea.   
[Github Link](https://github.com/pandeysoni/serverless-api-gateway)   
You can reach out to me for any doubt and suggestions. Please share this with others as well.  

Thanks!  
Happy Coding!!
