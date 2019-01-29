---
template: post
title: Serverless | Lambda Functions | API Gateway
slug: /2017/11/05/serverless-lambda-functions-api-gateway/
draft: false
date: '2017-11-05T22:40:32.169Z'
description: Serverless | Lambda Functions | API Gateway
category: Javascript
tags:
  - Serverless
  - Node.js
  - Lambda Function
---
![screenshot](/media/image1.jpg)

Now a days, I am working on AWS services (API Gateway, Lambda Function, Kinesis Stream, DynamoDb, S3 etc.). In this article, I will explain How to do Serverless Programming.
This is my first post on Serverless Programming. So, it will give an overview about:

* What is meant by Serverless?
* Why Serverless is trending?
* Which providers are in market to provide these services?
* Which framework to use to make infrastructure better?
* What is Lambda function and Why it is mentioned here?
* How is API Gateway related to Lambda?

Let’s take above questions one by one and take deep dive to find answer.  

_**What is meant by Serverless?**_\
“Serverless” → This does not imply →“there is no server”. Developers don’t need to care about server, only focus on development.  

_**Why Serverless is trending?**_\
Serverless is in trend because it dynamically manages the allocation of machine resources and that computing is economical. Serverless computing is a cloud computing execution model in which the cloud provider dynamically manages the allocation of machine resources. Pricing is based on the actual amount of resource consumed by an application, rather than on pre-purchased units of capacity.  

_**Which providers are in market to provide these services?**_

* Web Services
* Microsoft Azure
* IBM OpenWhisk
* Google Cloud Platform
* Kubeless
* Spotinst
* Webtasks
  I have used Amazon Web Services. Lambda Functions and API Gateway are services of amazon.  

_**Which framework to use to make infrastructure better?**_
[Serverless](https://serverless.com) is your toolkit for deploying and operating serverless architectures. Focus on your application, not your infrastructure.  

_**What is Lambda function and Why it is mentioned here?**_\
Lambda function is knows as 'Function as a Service (FaaS)'. AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume — there is no charge when your code is not running. With Lambda, you can run code for virtually any type of application or backend service — all with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability.  

_**How is API Gateway related to Lambda?**_\
Routes are defined in configuration and each route is associated with a Faas Function. When an API Gateway recieves a request, it finds the routing configuration matching the request and then calls the relevant Faas function. Typically, the API Gateway will allow mapping from http request parameters to input arguments for the FaaS function. Serverless framework makes working with API Gateway and Lambda significantly easier than using the first principles provided by AWS.  

In next article, I will explain about “Structure of Serverless Framework using Lambda and API Gateway.”  

You can reach out to me for any doubt and suggestions. Please share this with others as well.  

Thanks!\
Happy Coding!!
