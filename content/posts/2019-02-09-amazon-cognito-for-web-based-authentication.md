---
template: post
title: 'Amazon Cognito for Web based Authentication  '
slug: /2019/02/09/amazon-cognito-custom-auth-for-web-based-app
draft: false
date: 2019-02-09T09:43:32.340Z
description: >-
  Amazon Cognito is complete package of authentication, authorization, and user
  management for web and mobile apps. It's easy for users to sign in directly
  with a user name and password, or through a third party such as Facebook,
  Amazon, or Google ...
category: AWS Services
tags:
  - Amazon Cognito
  - Web Based Authentication
---
 Amazon Cognito is complete package of authentication, authorization, and user management for web and mobile apps. It's easy for users to sign in directly with a user name and password, or through a third party such as Facebook, Amazon, or Google.

![](/media/screen-shot-2019-02-09-at-3.56.13-pm.png)

Let's first walk through, how to setup Amazon Cognito - 

1. Login to AWS and go to the Cognito Service section.
2. Select Manage User Pool for this setup and you will have option to Create User Pool.
3. Provide unique pool name and there you will have two option **_Review defaults_** or **_Step through Settings_**, I have chosen **_Step through Settings_** and for _**Attributes**_ I have chosen Email to login, keep remaining configuration as default.

![](/media/screen-shot-2019-02-09-at-4.29.34-pm.png)

5. Go to _**Domain Name**_ from sidebar and take amazon cognito domain or you can put your custom domain.

![](/media/screen-shot-2019-02-09-at-4.10.42-pm.png)

6. Select _**App Clients**_ from Sidebar and give app client name, after this it will give you App clientId and App client secret.
7. Select _**App Client Settings**_ from Sidebar and fill the details as shown in image, in Callback URL provide a URL name where you want to be redirected after the login. 

![](/media/screen-shot-2019-02-09-at-4.17.32-pm.png)

8. Clicking on this format link you will see login page https://**domain**.auth.**region**.amazoncognito.com/signup?response__type=code&client_id=**clientId**&redirect_uri=**callbackURL**. _\
   **Make sure you are putting correct details for domain, region, clientId and callbackURL.**
9. In this, We haven't introduced Google or Facebook any other Identity Provider, so our signup page will look like this

![](/media/sign_up.png)

10. Next step, we will introduced How to introduced Google Identity Provider, for this Select _**Identity Provider **_from sidebar, you will see other options, Choose Google and put clientId and Client Secret generated by [Google Console](https://console.developers.google.com/apis/credentials)  , Make sure that you are allowing amazoncognito.com in _**OAuth Consent Screen **_and **_Authorized Redirect URIs_** is having this access domain, in my case it is https://test-auth.auth.us-west-2.amazoncognito.com/oauth2/idpresponse

11. Update App Client Setting with Enable Identity Provider for Google

![](/media/google_identity_provider.png)

Here is next look of Amzon Cognito Auth with Google Identity Provider 

![](/media/google_signup.png)

\
You can reach out to me for any doubt and suggestions.
