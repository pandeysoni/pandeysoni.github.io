---
title: User authentication using passport.js in Node.js
date: "2016-06-26T22:40:32.169Z"
template: "post"
draft: false
slug: "/2016/06/26/user-authentication-using-passport.js-in-node.js/"
category: "Javascript"
tags:
  - "Passport.js"
  - "Node.js"
  - "Express"
description: "User authentication using passport.js in Node.js."
---

Here we will learn how to implement user authentication using passport-local strategy. In last post, we learned [User Authentication using JWT](http://thepandeysoni.org/2016/05/22/user-authentication-using-JWT-(JSON-Web-Token)-in-Node.js-(express=framework)/). There, we used JSON Web Token. These both are different way and robust way.
What we are going to use here:

* Node.js
* Express Framework
* Mongodb
* JWT

I assume, you all are aware with Node.js, Express Framework and Mongodb. If not, please have a look on my previous posts:

1. [Node.js Single File Application using Express Framework.](http://thepandeysoni.org/2016/02/05/node.js-single-file-application-using-express-framework/)
1. [CRUD Operations in Node.js Application (Expresss Framework) using mongodb.](http://thepandeysoni.org/2016/03/05/CRUD-operations-in-node.js-application-(expresss=framework)-using-mongodb/)

These articles will atleast give you an overview about it.

What is Passport.js?
Passport is authentication middleware for Node.js. Extremely flexible and modular, Passport can be unobtrusively dropped in to any Express-based web application. A comprehensive set of strategies support authentication using a [username and password](http://www.passportjs.org/docs/username-password/), [Facebook](http://www.passportjs.org/docs/facebook/), [Twitter](http://www.passportjs.org/docs/twitter/), and [more](http://www.passportjs.org/docs/providers). 
Copyright © [Passport.js](http://www.passportjs.org).
Here, we will create application which will do following things only:

1. Register new user. 
2. One email address (username) can be registered only once.
3. Login user (If they are matching, passport.js sends a Set-Cookie header that will be used to authenticate other pages).
4. Logout user (Expire Session Cookies).

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
You can also have a look to complete [MEAN Structure](http://thepandeysoni.org/2016/04/05/CRUD-Operation-with-UI-integration-(Angular.js)/).

**1. Setting up Server using Express Framework.**

```js

/**
 * Module dependencies
 */
const express = require('express')
const passport = require('passport')
const db = require('./server/config/db')
const bodyParser = require('body-parser')
const expressSession = require('express-session')
const cookieParser = require('cookie-parser')
const app = express()
const port = process.env.PORT || 3000

//use cookie parser to store data
app.use(cookieParser());
app.use(expressSession({secret:'somesecrettokenhere'}));
//load client folder
//bodyparser to use for request and respnse and set limit in request body data
app.use(bodyParser.urlencoded({ limit: '52428800', extended: true }));
app.use(bodyParser.json({limit: '52428800'}));

// Bootstrap passport config
require('./server/config/passport')(passport);

app.use(passport.initialize());
app.use(passport.session());

// Bootstrap routes
require('./server/config/routes')(app, passport);

app.listen(port);
console.log('Express app started on port ' + port);
```
First of all, we required all the module dependencies. After that we used middleware instances body-parser, cookie-parser and express-session. We used Mongodb to store database. You can see we loaded db file, so our connection will established, when we will start our server.

```js
const db = require('./server/config/db')
```
**_2. Setting up Database connection using Mongoose._**
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
**_3. Setting up Passport_**

```js
/*!
 * Module dependencies.
 */

const User = require('../user/user.server.model').User;
const local = require('./passport/local');

/**
 * Expose
 */

module.exports = function (passport) {
  // serialize sessions
  passport.serializeUser(function(user, done) {
    done(null, user.id)
  })

  passport.deserializeUser(function(id, done) {
    User.findOne({ _id: id }, function (err, user) {
      done(err, user)
    })
  })

  // use these strategies
  passport.use(local);
};
```

Here we used local strategy, you can add other facebook, google etc. 
**_What is passport serializer and deserializer?_**
**_Serialize function_** determine what data from the user object should be stored in the session. The result of the serializeUser method is attached to the session as **_req.session.passport.user_** = {} here for instance.  
In **_deserialize function_** you provide in first argument of deserialize function that same key of user object that was given to done function in serialize call. So your whole object is retrieved with help of that key. That key here is id(key can be any key of the user object ie name,email etc). In deserialize function that key is matched with in database or any data resource. The fetched object is attached to request object as req.user. We used **_passport-local_** for local authentication using email(username) and password.

```js
/**
 * Module dependencies.
 */
const LocalStrategy = require('passport-local').Strategy;
const User = require('../../user/user.server.model').User;

/**
 * Expose
 */

module.exports = new LocalStrategy({
    usernameField: 'email',
    passwordField: 'password'
  },
  function(email, password, done) {
    var options = {
      criteria: { email: email },
      select: 'name email hashed_password salt'
    };
    User.load(options, function (err, user) {
      if (err) return done(err)
      if (!user) {
        return done(null, false, { message: 'Unknown user' });
      }
      if (!user.authenticate(password)) {
        return done(null, false, { message: 'Invalid password' });
      }
      return done(null, user);
    });
  }
);
```
Once the load returns with our user object the only thing left is to compare the Unknown User and password to see if there is a match.
If it is a match, we let the user in (by returning the user to passport — return done(null, user)), if not we return an unauthorized error (by returning nothing to passport — return done(null, false, {message: ''})).
How route endpoint to use passport authentication.

```js
app.post('/login', passport.authenticate('local', {}), User.login);
```
Feel free to download the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to repository for complete working code.
[source code on github](https://github.com/pandeysoni/passport-authentication-in-node.js.git) 
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!  
Happy Coding!!
