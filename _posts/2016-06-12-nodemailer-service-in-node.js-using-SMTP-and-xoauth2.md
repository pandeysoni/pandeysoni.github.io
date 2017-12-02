---
layout: post
title: Nodemailer Service in Node.js using SMTP and xoauth2
description: Nodemailer Service in Node.js using SMTP and xoauth2
keywords: nodejs, nodemailer, express, smtp, xoauth2
categories: [nodemailer, node, express]
---

Here, we will learn how to send a mail in Node.js using Nodemailer Package. What we are planning to use here:

* Node.js
* Express Framework
* Nodemailer Package (version — “^0.7.1”)

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

```js
+ server
 + config
    + config.js
    + routes.js
+ server.js
+ package.json
+ README.md
```

Here, we don’t save data in database, so we are not using any database. You can also have a look to complete [MEAN Structure](http://thepandeysoni.org/2016/05/07/CRUD-Operation-with-UI-integration-(Angular.js)/).

**_1. Setting up Server using Express Framework._**

```js
/**
 * Module dependencies
 */
const express = require('express')
const bodyParser = require('body-parser')
const app = express()
const port = process.env.PORT || 3000
const router = express.Router()

app.use(router);
//load client folder
//bodyparser to use for request and respnse and set limit in request body data
router.use(bodyParser.urlencoded({ limit: '52428800', extended: true }));
router.use(bodyParser.json({limit: '52428800'}));

// Bootstrap routes
require('./server/config/routes')(router);

app.listen(port);
console.log('Express app started on port ' + port);
```

First of all, we required all the module dependencies. After that we used middleware instances **_body-parser_**, express.

**_2. Setting up Nodemailer using SMTP_**

```js
const nodemailer = require('nodemailer')
const config = require('./config')

// create reusable transporter object using the default SMTP transport
var transporter = nodemailer.createTransport("SMTP", {
    service: "Gmail",
    auth: {
        user: config.mailUser,
        pass: config.mailPass
    }
});

module.exports = function (app) {
    // send mail with defined transport object
    app.post('/send', function(req, res){  
        // setup e-mail data with unicode symbols
        var mailOptions = {
            from: ''+config.mailUser+'', // sender address
            to: req.body.receivers, // list of receivers
            subject: req.body.subject, // Subject line
            text: req.body.text, // plaintext body
            // html: '<b>Hello world 🐴</b>' // html body
        }; 
        transporter.sendMail(mailOptions, function(error, info){
            if(error){
                return res.send(error);
            }
            return res.send("mail send successfully");
        }); 
    })
};
```
We need to install nodemailer 0.7.1 version as a dependency.
To use Gmail you may need to configure [“Allow Less Secure Apps”](https://myaccount.google.com/lesssecureapps) in your Gmail account unless you are using 2FA in which case you would have to create an [Application Specific](https://accounts.google.com/signin/v2/sl/pwd?service=accountsettings&passive=1209600&osid=1&continue=https%3A%2F%2Fmyaccount.google.com%2Fapppasswords&followup=https%3A%2F%2Fmyaccount.google.com%2Fapppasswords&rart=ANgoxccAm2w1A0J-N26zWpCeX4LDTofbTAhWrEoFiqtsOgoxrhEWqXZy9OD3st7E4672IoO7MNDn8qvOk2aAV6RstlZxCEfvpg&authuser=0&flowName=GlifWebSignIn&flowEntry=ServiceLogin) password. You also may need to unlock your account with [”Allow access to your Google account”](https://accounts.google.com/b/0/DisplayUnlockCaptcha) to use SMTP. Copyright © [Nodemailer](https://github.com/nodemailer/nodemailer)

```js
npm install nodemailer@0.7.1 -- save
```

and after that create transporter object using SMTP Transport. We are defined sender username and password in config file, you can change according to your gmail setup.

```js
/**Add all configuration settings here*/
module.exports = {
  mailUser: senderEmail,
  mailPass: senderPass
}
```
We are parsing body data in mailOptions in this format:

```js
{
   "subject": "Nodemailer Test using SMTP",
   "receivers": "sonipandey.71@gmail.com",
   "text":"Hey, You Recieved message from your nodemailer package"
}
```
Transporter Object have one function named sendMail, which will send data using mailOptions. 
**_3. Nodemailer setup using xoauth2._**
Here, transporter object will be change only

```js
/**Module Dependency*/
const xoauth2 = require('xoauth2')
var smtpTransport = nodemailer.createTransport("SMTP",{
        service:"Gmail",
        auth:{
            XOAuth2: {
                user: config.mailUser,
                clientId: config.clientId,
                clientSecret: config.clientSecret,
                refreshToken: config.refreshToken
            }
        }
});
```
We need to install xoauth2 and need to setup clientId, clientSecret and refreshToken.

**_Step I: Obtain OAuth 2.0 credentials at Google Developers Console._**
As stated here, you should:
1. Go to the Google Developers Console.
2. Select a project, or create a new one.
3. In the sidebar on the left, expand APIs & auth. Next, click APIs. Select the Enabled APIs link in the API section to see a list of all your enabled APIs. Make sure that the “Gmail API” is on the list of enabled APIs. If you have not enabled it, select the Gmail API from the list of APIs (under Google Apps APIs), then select the Enable API button for the API.
4. In the sidebar on the left, select Credentials.
5. If you haven’t done so already, create your project’s OAuth 2.0 credentials by clicking Create new Client ID, and providing the information needed to create the credentials.

![screenshot](./images/nodemailer/image1.png?raw=true)

6. Look for the Client ID and Client secret in the table associated with each of your credentials.

![screenshot](./images/nodemailer/image2.png?raw=true)

PAY SPECIAL ATTENTION TO specifying https://developers.google.com/oauthplayground as a Redirect URI when you create a new User in the console. Otherwise, you will have an error.
**_Step II: Obtain the refresh token at Google OAuth2.0 Playground_**
1. Go to the Google Oauth2.0 Playground.
2. Click the Gear Button on the right-top. Set your Client ID and Client Secret obtained from theGoogle Developers Console, and select Access token location as Authorization header w/ Bearer prefix. Close this configuration overlay.

![screenshot](./images/nodemailer/image3.png?raw=true)

3. Set up the scopes. Use https://mail.google.com/ as it’s the one need by nodemailer. Then click the Authorize APIs button.

![screenshot](./images/nodemailer/image4.png?raw=true)

4. After OAuth2.0 authorization, exchange authorization code for tokens and your refresh token is ready-to-use.

![screenshot](./images/nodemailer/image5.png?raw=true)

and setup your configuration here:

```js
/**Add all configuration settings here*/
module.exports = {
	mailUser: senderEmail,
	mailPass: senderPass
	clientId: clientId,
	clientSecret: clientSecret,
	refreshToken: refreshToken
}
```

Feel free to clone the full code for this post to see the full picture of how everything works together and customize it for your own needs and mark it starred, if it useful. Have a look to repository for complete working code.
[source code on github](https://github.com/pandeysoni/Nodemailer-Service-in-Node.js-using-SMTP-and-xoauth2) 
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!  
Happy Coding!!
