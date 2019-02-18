---
template: post
title: ' User Authentication using JWT (JSON Web Token) in Node.js (Express Framework)'
slug: >-
  2016/05/22/user-authentication-using-JWT-(JSON-Web-Token)-in-Node.js-(express=framework)
draft: false
date: '2016-05-22T22:12:03.284Z'
description: >-
  Here we will learn how to implement user authentication using JWT(JSON Web
  Token) strategy. What we are going to use here: Node.js, Express Framework,
  Mongodb and JWT. I assume, you all are aware with Node.js, Express Framework
  and Mongodb. If not, please have a look on my previous posts: ...
category: Javascript
tags:
  - JWT
  - Node.js
  - Express
---
Here we will learn how to implement user authentication using JWT(JSON Web Token) strategy. What we are going to use here:

* Node.js
* Express Framework
* Mongodb
* JWT

I assume, you all are aware with Node.js, Express Framework and Mongodb. If not, please have a look on my previous posts:

1. [Node.js Single File Application using Express Framework.](http://thepandeysoni.org/2016/02/05/node.js-single-file-application-using-express-framework/)
2. [CRUD Operations in Node.js Application (Expresss Framework) using mongodb.](http://thepandeysoni.org/2016/04/05/CRUD-operations-in-node.js-application-(expresss=framework)-using-mongodb/)

These articles will atleast give you an overview about it.

Here we will learn how to implement token based user authentication.

1. Register new user. 
2. One email address can be registered only once.
3. After registration a verification link is send to user’s email address.
4. Verify account by clicking on link send to user’semail address and on successful verification user will be redirected to login page.
5. If you are not verified your account after creation, you will not be allowed to land to dashboard after success login.
6. User can resend password to registered email address in case user forget.
   Project Structure will be like this:

```js
+ server
 + user(there can be multilple other modules like user)
    + user.server.controller.js
    + user.server.model.js
    + user.server.route.js
 + config
    + config.js
    + db.js
 + routes.js
+ server.js
+ package.json
+ README.md
```

You can also have a look to complete [MEAN Structure](http://thepandeysoni.org/2016/05/07/CRUD-Operation-with-UI-integration-(Angular.js)/).

**1. Setting up Server using Express Framework**

```js
const express = require('express')
const Routes = require('./server/routes')
const config = require('./server/config/config')
const bodyParser = require('body-parser')
const app = express()
const db = require('./server/config/db')
const path = require('path')

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
app.use(express.static(path.join(__dirname, 'client/')));

app.get('*', function(req, res) {
    res.sendFile(path.resolve('client/index.html')); // load the single view file (angular will handle the page changes on the front-end)
});
/** load routes*/

require('./server/routes')(app);

var port = config.server.port;

app.listen(process.env.PORT || port);

console.log('App started on port ' + port);
```

First of all, we required all the module dependencies. After that we used middleware instances body-parser, cookie-parser and express-session. We used Mongodb to store database. You can see we loaded db file, so our connection will established, when we will start our server.

```js
const db = require(‘./server/config/db’)
```

**2. Setting up Database connection using Mongoose**

```js
const Mongoose = require('mongoose');
const config = require('./config');

Mongoose.connect(config.db);

const db = Mongoose.connection;
db.on('error', console.error.bind(console, 'connection error'));
db.once('open', function callback() {
    console.log("Connection with database succeeded.");
});

exports.db = db;
```

**3. Setting up JWT**

```js
npm install --save jsonwebtoken
```

_**Register new user**_: We will check that enetered email id is unique or not. If email unique then we will save entry and send verification link using nodemailer.

```js
exports.create = function (req, res){
  req.body.password = Common.encrypt(req.body.password);
  User.saveUser(req.body, function(err, user) {
      if (!err) {
          var tokenData = {
              username: user.username,
              id: user._id
          }
          Common.sentMailVerificationLink(user,Jwt.sign(tokenData, privateKey));
          return res.send(Boom.forbidden("Please confirm your email id by clicking on link in email"));
      } else {
          if (11000 === err.code || 11001 === err.code) {
              return res.send(Boom.forbidden("please provide another user email"));
          } else{
            return res.send(Boom.forbidden(err)); // HTTP 403
          }
      }
  })
}
```

saveUser function is defined in model as a [statics function](<>) and We used ‘aes-256-ctr’ algorithm to encrypt and decrypt password.

```js
var  algorithm = 'aes-256-ctr';
var privateKey = '37LvDSm4XvjYOh9Y';

function decrypt(password) {
    var decipher = crypto.createDecipher(algorithm, privateKey);
    var dec = decipher.update(password, 'hex', 'utf8');
    dec += decipher.final('utf8');
    return dec;
}

// method to encrypt data(password)
function encrypt(password) {
    var cipher = crypto.createCipher(algorithm, privateKey);
    var crypted = cipher.update(password, 'utf8', 'hex');
    crypted += cipher.final('hex');
    return crypted;
}
```

_**Verify New User**_: When new user register, that user need to verify link, otherwise user will not able to login.\
_**Login User**_:

```js
exports.login = function (req, res){
  User.findUser(req.body.username, function(err, user) {
        if (!err) {
            console.log(user);
            if (user === null){
              return res.send(Boom.forbidden("invalid username or password"));
            }
            if (req.body.password === Common.decrypt(user.password)) {
                if(!user.isVerified){
                  return res.send(Boom.forbidden("Your email address is not verified. please verify your email address to proceed"));
                }
                else{ 
                  var tokenData = {
                      username: user.username,
                      scope: [user.scope],
                      id: user._id
                  };
                  var result = {
                      username: user.username,
                      scope: user.scope,
                      token: Jwt.sign(tokenData, privateKey)
                  };

                  return res.json(result);
                }
            } else{
              return res.send(Boom.forbidden("invalid username or password"));
            }
        } else {
            if (11000 === err.code || 11001 === err.code) {
                return res.send(Boom.forbidden("please provide another user email"));
            } else {
                    console.error(err);
                    return res.send(Boom.badImplementation(err));
            } 
        }
    })
}
```

Initially, we find user in our database, once user found in database, we decrypted entered password and matched with found user’s password and after successful match, we generate one token.\
_**Forgot Password**_: In this case, we are using one package randomstring. install _**randomstring**_ package:

```js
npm install -- save randomstring
```

```js
const randomstring = require("randomstring")
exports.forgotPassword = function (req, res){
  var random = Common.encrypt(randomstring.generate({length: 5, charset: 'alphabetic'}));
    User.findUserUpdate({username: req.body.username}, {password: random}, function(err, user) {
        if (!err) {
            if (user === null){
                return res.send(Boom.forbidden("invalid username"));
            }
            else{
                Common.sentMailForgotPassword(user);
                return res.send("password is send to registered email id");
            }
        } else {       
            return res.send(Boom.badImplementation(err));
         }
    })
}
```

Here, we are generating random password and updating that password, so that user can login with the random generated password.

Feel free to clone the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to repository for complete working code.
[source code on github](https://github.com/pandeysoni/User-Authentication-using-JWT-JSON-Web-Token-in-Node.js-Express) 
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!\
Happy Coding!!
