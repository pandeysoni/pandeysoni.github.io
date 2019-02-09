---
template: post
title: 'Amazon Cognito for Web based Authentication  '
slug: /2019/02/09/amazon-cognito-custom-auth-for-web-based-app
draft: true
date: 2019-02-09T09:43:32.340Z
description: Amazon Cognito for Web based Authentication
category: AWS Services
tags:
  - Amazon Cognito
  - Web Based Authentication
---
Hey Folks, I am going to explain _how I have used Amazon Cognito  with some additional Lambda Function_. Amazon Cognito is complete package of authentication, authorization, and user management for web and mobile apps. It's easy for users to sign in directly with a user name and password, or through a third party such as Facebook, Amazon, or Google.

![](/media/screen-shot-2019-02-09-at-3.56.13-pm.png)

Let's first walk through, how to setup Amazon Cognito - 

1. Login to AWS and go to the Cognito Service section.
2. Select Manage User Pool for this setup and you will have option to Create User Pool.
3. Provide unique pool name and there you will have two option **_Review defaults_** or **_Step through Settings_**, I have chosen **_Step through Settings_** and for **Attributes **I have chosen Email to login, keep remaining configuration as default.

![](/media/screen-shot-2019-02-09-at-4.29.34-pm.png)

5. Go to _**Domain Name**_ from sidebar and take amazon cognito domain or you can put your custom domain.

![](/media/screen-shot-2019-02-09-at-4.10.42-pm.png)

6. Select **App Clients **from Sidebar and give app client name, after this it will give you App clientId and App client secret.
7. Select _**App Client Settings**_ from Sidebar and fill the details as shown in image, in Callback URL provide a URL name where you want to be redirected after the login. 

![](/media/screen-shot-2019-02-09-at-4.17.32-pm.png)

8. Clicking on this format link you will see login page https://**domain**.auth.**region**.amazoncognito.com/signup?response_type=code&client_id=**clientId**&redirect_uri=**callbackURL**. _\
   **Make sure you are putting correct details for domain, region, clientId and callbackURL.**_
9. Cognito have provided add your Lambda Function in triggers , in my case I introduced Lambda Function after Post Authentication, which will check user's entry in database and after that it generate code in Uri.

You can reach out to me for any doubt and suggestions. Please share this with others as well.



Thanks!\
Happy Coding!!
