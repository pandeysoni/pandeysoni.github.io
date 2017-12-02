---
layout: post
title: CRUD Operations in Node.js Application (Express Framework) using mongodb
description: CRUD Operations in Node.js Application (Express Framework) using mongodb
keywords: node.js, express.js, mongodb
categories: [crud, mongodb, node.js, express]
---
I assume, that you have [node.js](http://nodejs.org) already installed. We will create a basic structure using the express generator. First, you need to install express and express-generator.

```js
npm install -g express
npm install -g express-generator
```

Great. Now we've got Express & the generator installed as a global command on our system. Use below command to create express basic structure:

```js
express nodeapp
```

nodeapp is the name of your application whatever you want. Next command to run on your terminal:

```js
cd nodeapp
npm install
```

Now check that, do you have MongoDB setup? If you haven't, go to [MongoDB Link](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/) and follow the steps and ready to start our project and one another solution is to avoid MongoDB installation steps, you can put your database on [mongolab](http://mongolab.com) then you don't need MongoDB locally on your system. Great, now we are going to place our database connection string using mongoose in one another file db.js in the config folder of root and put this piece of code in that file:

```js
var Mongoose = require('mongoose');
Mongoose.connect('mongodb://localhost/nodeappdb'); 
var db = Mongoose.connection;
db.on('error', console.error.bind(console, 'connection error'));
db.once('open', function callback() {
  console.log("Connection with database succeeded."");
});
exports.db = db;
```

Next, open up app.js and let's add our db.js file to the variables. by adding the line var db = require('./config/db');
We need to add a few more packages to our package.json file as a dependency.

```js
npm install mongoose — save
```
Awesome. Now to test it's all working in the terminal type npm start and you shouldn't see any errors and if you navigate to http://127.0.0.0:3000 you will see following screen

** _Design Schema_ **: Create one model folder. Create a new file within that folder for whatever your data is going to be. In this case, I'm going to call it company.js and add below code to that file.

```js

'use strict';

var mongoose = require('mongoose'),
    Schema = mongoose.Schema;

/**
 * @module company
 * @description contain the details of company information, conditions and actions.
 */

var CompanySchema = new Schema({
  name: { type: String },
  numberOfEmployees: { type: Number }
});


CompanySchema.statics = {

     /**
      findOnecompany. return the one company object.
      @param id: get id to find one company by id.
      @param callback: callback of this form.
    */
    get: function(query, callback) {
        this.findOne(query, callback);
    },
    /**
      findcompany. return the company objects.
      @param callback: callback of this form.
    */
    getAll: function(query, callback) {
        this.find(query, callback);
    },
    
    /**
      updatecompany. return the create company object result.
      @param updateData: updateData is use to update company w.r.t id.
      @param callback: callback of this form.
    */
    updateById: function(id, updateData, callback) {
        this.update(id, {$set: updateData}, callback);
    },
    remove: function(removeData, callback) {
         this.remove(removeData, callback);
    },
    create: function(data, callback) {
        var company = new this(data);
        company.save(callback);
    }
}

var company = mongoose.model('company', CompanySchema);

/** export schema */
module.exports = {
    Company: company
};
```

We are using express 4.x, so we will change routing structure at root level

```js
var router = express.Router();
app.use(router);
```

and change this piece of code

```js
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));
```

use router in place of app, now your code will look like this:

```js
router.use(logger('dev'));
router.use(bodyParser.json());
router.use(bodyParser.urlencoded({ extended: false }));
router.use(cookieParser());
router.use(express.static(path.join(__dirname, 'public')));
```

and append one line code in this:

```js
require('./routes/company')(router);
```
In route folder, create one company.js file. Add this code in that file:
```js
var Company= require('./controller/company');
// API Server Endpoints
module.exports = function(router){
 router.post('/company', Company.create),
 router.get('/company/:id', Company.get),
 router.put('/company/:id', Company.update),
 router.delete('/company/:id', Company.delete)
}
```
Now, as you can see, we need to create controller folder and there, we will create one company.js file.

```js
'use strict';

var Company = require('../model/company').Company;

/** create function to create Company. */
exports.create = function (req, res) {
    Company.create(req.body, function(err, result) {
        if (!err) {
            return res.json(result);
        } else {
            return res.send(err); // 500 error
        }
    });
};

/** getCompany function to get Company by id. */
exports.get = function (req, res) {
    Company.get({_id: req.params.id}, function(err, result) {
        if (!err) {
            return res.json(result);
        } else {
            return res.send(err); // 500 error
        }
    });
};

/** updateCompany function to get Company by id. */
exports.update = function (req, res) {
    Company.updateById(req.params.id, req.body, function(err, result) {
        if (!err) {
            return res.json(result);
        } else {
            return res.send(err); // 500 error
        }
    });
}

/** removeCompany function to get Company by id. */
exports.delete = function (req, res) {
    Company.removeById({_id: req.params.id}, function(err, result) {
        if (!err) {
            return res.json(result);
        } else {
            console.log(err);
            return res.send(err); // 500 error
        }
    });
}
```
Initially, its structure looks tough, but when you start to do code, then you will realize, yeah, it sounds good to do code in a standardized way. I haven't explained the code because when you will just follow the steps and finish this then you will try to understand the code and it's easy.
Feel free to download the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to the repository for complete working code.

[source code on github](https://github.com/pandeysoni/nodeapp)  

We will discuss more MongoDB and Node.js in future posts.
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!   
Happy Coding!!
