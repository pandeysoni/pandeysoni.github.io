---
layout: post
title: CRUD Operation (Node.js + Express + MongoDB) with UI integration (Angular.js )
description: CRUD Operation (Node.js + Express + MongoDB) with UI integration (Angular.js )
keywords: node.js, express.js, mongodb, angular
categories: [crud, node, express, mongodb, angular]
---

In previous post [CRUD Operations in Node.js Application (Expresss Framework) using mongodb](http://thepandeysoni.org/2016/05/04/CRUD-operations-in-node.js-application-(expresss=framework)-using-mongodb/). In this article, we will develop FrontEnd using Angular.js. Before that we will reorganize our structure.

```js
+ client
 + assets
 + modules (there can be multiple modules)
   + app
     + config
     + app.client.routes.js
     + controllers
       + app.client.controller.js
     + views
       + app.client.view.js (can be add multiple views)
     + app.client.module.js
 + application.js
 + config.js
 + index.html
+ server
 + company(there can be multilple other modules like company)
    + company.server.controller.js
    + company.server.model.js
    + company.server.route.js
 + config
    + config.js
    + db.js
 + routes.js
+ app.js
+ package.json
+ bower.json
+ README.md
```

There will be some changes in server code like we need to load frontend directory in server side etc. Have a look at [repository](https://github.com/pandeysoni/nodeAngularApp) for new changes in server code. Before going to start development in Angular.js, first have a look to [ui-router](https://github.com/angular-ui/ui-router/wiki). Let's start FrontEnd Side code, First we need to add bower.json file in root folder with defined specific dependencies which will require in our code. For installation of packages, we have two options:

1. Need to add one line in scripts (package.json file) and only run npm install.

```js
"scripts": {
   "postinstall": "bower install"
 },
```
2. You can directly run bower install but in this case you will run npm install command as well.
In client folder, create on **application.js** file and add below code in that file. This file have configuration of application.

```js
'use strict';

//Start by defining the main module and adding the module dependencies
angular.module(ApplicationConfiguration.applicationModuleName, ApplicationConfiguration.applicationModuleVendorDependencies);

// Setting HTML5 Location Mode
angular.module(ApplicationConfiguration.applicationModuleName).config(['$locationProvider',
	function($locationProvider) {
		$locationProvider.html5Mode({
		  enabled: true,
		  requireBase: true
		});
		// $locationProvider.hashPrefix('!');
	}
]);

//Then define the init function for starting up the application
angular.element(document).ready(function() {
	//Fixing facebook bug with redirect
	// if (window.location.hash === '#_=_') window.location.hash = '#!/';

	//Then init the app
	angular.bootstrap(document, [ApplicationConfiguration.applicationModuleName]);
});
```
Now create **config.js** file in client folder, this file add module dependency and register new modules.

```js
'use strict';

// Init the application configuration module for AngularJS application
var ApplicationConfiguration = (function() {
	// Init module configuration options
	var applicationModuleName = 'nodeapp';
	var applicationModuleVendorDependencies = ['ngResource', 'ui.router',  'angular-growl'];

	// Add a new vertical module
	var registerModule = function(moduleName, dependencies) {
		// Create angular module
		angular.module(moduleName, dependencies || []);

		// Add the module to the AngularJS configuration file
		angular.module(applicationModuleName).requires.push(moduleName);
	};
	
	return {
		applicationModuleName: applicationModuleName,
		applicationModuleVendorDependencies: applicationModuleVendorDependencies,
		registerModule: registerModule
	};
})();
```
In applicationModuleVendorDependencies, you can add more modules, according to your need. Now create **index.html** file, and add all bower_components, modules and all stylesheets and javascripts file here.

```html
<!-- index.html -->
<!doctype html>

<!-- ASSIGN OUR ANGULAR MODULE -->
<html >
<head>
    <!-- META -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1"><!-- Optimize mobile viewport -->
    <base href="/">
    <title>NodeApp</title>
    <!-- SCROLLS -->
     <style>
        .dialog-open {
          display: block;
        }
        .dialog-close {
          display: none;
        }
      </style>
        <link rel="stylesheet" href="bower_components/angular-growl/build/angular-growl.min.css">
    <!-- SPELLS -->
</head>
<!-- SET THE CONTROLLER AND GET ALL TODOS -->
<body>
   <div data-ui-view >
       
   </div>
    <div data-growl=""></div>
        <!-- HEADER AND TODO COUNT -->
      

        <!-- TODO LIST -->
      

        <!-- FORM TO CREATE TODOS -->
    <script src="bower_components/angular/angular.js"></script>
    <script src="bower_components/angular-resource/angular-resource.js"></script>
    <script src="bower_components/angular-ui-router/release/angular-ui-router.js"></script> 
    <script src="bower_components/underscore/underscore.js"></script>
    <script src="config.js"></script>
    <script src="application.js"></script>  
    <script src="bower_components/angular-growl/build/angular-growl.js"></script>

    <script src="modules/app/app.client.module.js"></script>
    <script src="modules/app/config/app.client.routes.js"></script>
    <script src="modules/app/controllers/app.client.controller.js"></script>
    <script src="modules/app/services/app.client.services.js"></script>

</body>
</html>
```
Create app folder in client/modules location. Create app.client.module.js file, which just register module.

```js
ApplicationConfiguration.registerModule('app');
```
Create ui-routes in **app.client.routes.js** file in client/modules/app/config folder.

```js
angular.module('app').config(['$stateProvider', '$urlRouterProvider', 
   function($stateProvider, $urlRouterProvider) {
    // Home state routing
     $urlRouterProvider.otherwise('/');
     $stateProvider
          .state('app', {
               url: '/',
               templateUrl: 'modules/app/views/app.client.view.html'
           });
 }
]);
```
In this route, we will add more routes.
Create controller in **app.client.controller.js** file in client/modules/app /controllers folder. Here, we will add functionality.

```js
angular.module('app').controller('AppController', ['$scope',
   function($scope) {
     $scope.function = function(){
     }
   }
]);
```
Create view in **app.client.view.html** file in client/modules/app/views folder. Here, we will add UI-view. You can create multiple modules as per your requirements, but remember everytime register module, controller name, service name should be unique.
Feel free to download the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to repository for complete working code.
[source code on github](https://github.com/pandeysoni/nodeAngularApp)  
We will come with more about MongoDB and Node.js in future posts.
You can reach out to me for any doubt and suggestions. Please share this with others as well.

Thanks!  
Happy Coding!!