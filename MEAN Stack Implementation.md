## MEAN Stack Deployment to Ubuntu in AWS

Now, we have already learned how to deploy LAMP, LEMP and MERN Web stacks – it is time to get ourself familiar with MEAN stack and deploy it to Ubuntu server.

### MEAN STACK = MongoDB + ExpressJS + AngularJS + NodeJS
MongoDB = Document Database
Express = Back-end application framework
Angular = Front-end application framework
Node.Js = Javascript runtime enviroment

In order to complete this project we will need an AWS account and a virtual server with Ubuntu Server OS.

![setting up instance for project4](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9f0d01eb-fbc9-47f7-a8e7-bd43056d4d87)

![connecting to instance on terminal](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/00a492b9-13db-485b-b1da-f7c51a6f4abc)

### Install NodeJs

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this project to set up the Express routes and AngularJS controllers.

run the following command to get ubuntu ready for use

`sudo apt update`
`sudo apt upgrade`

add certificates and dependencies using the command below

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`
`curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

![adding certificates](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d6332df6-5d7b-441a-a5fa-2d727f431614)

![add certificates 2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c8949321-4b09-4d44-8002-4492840e1870)


Install NodeJS using the command below

`sudo apt install -y nodejs`

![install node js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e2e5c672-a645-48e8-99b6-c9f7249aedc0)


### Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

Install MongoDB, Start the server and verify that the service is up and running using the following codes respectively

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

`sudo apt install -y mongodb`

`sudo service mongodb start`

`sudo systemctl status mongodb`

![install mongoDB](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3b6b0f11-5e98-4406-af91-63cb6c519e1c)

![verify mongoDB is up and start the server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5ce02db2-f2e1-4763-b49a-00af3480a9e7)


Install npm – Node package manager.

`sudo apt install -y npm`

Install body-parser package. We need ‘body-parser’ package to help us process JSON files passed in requests to the server.

`sudo npm install body-parser`

![install bodyparser](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/6e61ae32-715e-41a0-a41e-211c3138bdbd)


Create a folder named ‘Books’

`mkdir Books && cd Books`

![mkdir and cd Books](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c7bba93a-9740-4dbe-a8d4-d3d5b79082fc)


In the Books directory, Initialize npm project

`npm init`

![initialize npm project in books directory](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3712c3d4-d59b-427f-8927-5f364d06482c)


Add a file to it named server.js

`vi server.js`

Copy and paste the web server code below into the server.js file.

`var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});`

![adding file to server named server js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c27fc103-37b8-4bf1-898f-d7684a08d017)


### Install ExpressJS and set up Routes to the Server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database. We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

`sudo npm install express mongoose`

In ‘Books’ folder, create a folder named apps

`mkdir apps && cd apps`

Create a file named routes.js

`vi routes.js`

![mkdir apps cd apps vi into routes js mkdir models cd models vi into books js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9c964021-3c5e-4700-8a16-6500a0fbcff5)


Copy and paste the code below into routes.js

`const Book = require('./models/book');

module.exports = function(app){
  app.get('/book', function(req, res){
    Book.find({}).then(result => {
      res.json(result);
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while retrieving books');
    });
  });

  app.post('/book', function(req, res){
    const book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages
    });
    book.save().then(result => {
      res.json({
        message: "Successfully added book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while saving the book');
    });
  });

  app.delete("/book/:isbn", function(req, res){
    Book.findOneAndRemove(req.query).then(result => {
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while deleting the book');
    });
  });

  const path = require('path');
  app.get('*', function(req, res){
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
  });
};`

![writing using vi into routes js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/565c5a0a-9d05-4318-a745-93069662b0ca)


In the ‘apps’ folder, create a folder named models

`mkdir models && cd models`

Create a file named book.js

`vi book.js`

Copy and paste the code below into ‘book.js’

`var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);`

![writing using vi into books js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c0dae182-28c4-42b6-afd1-1bf5e7724825)


### Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to ‘Books’

`cd ../..`

Create a folder named public

`mkdir public && cd public`

Add a file named script.js

`vi script.js`

![mkdir public cd into public and vi script js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/99df2894-0386-4260-b713-7c800a19a80e)


Copy and paste the Code below (controller configuration defined) into the script.js file.

`var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});`

![writing using vi into scripts js](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/738aac86-2d6b-41eb-95d0-79b60ebff24c)


In public folder, create a file named index.html;

`vi index.html`

Copy and paste the code below into index.html file.

`<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>`

![writing using vi into index html](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a38d47a4-19e1-4304-861e-93da42c4a118)


Change the directory back up to Books

`cd ..`

Start the server by running this command:

`node server.js`

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

`curl -s http://localhost:3300`

It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet. For this – you need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3a60e279-3042-4a52-9d97-09509177149d)

Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name. Quick reminder how to get your server’s Public IP and public DNS name:

You can find it in your AWS web console in EC2 details
Run `curl -s http://169.254.169.254/latest/meta-data/public-ipv4` for Public IP address or `curl -s http://169.254.169.254/latest/meta-data/public-hostname` for Public DNS name.
This is how your Web Book Register Application will look like in browser:

![server from browser](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e39d91d1-dec2-46a5-9a46-358e6fe931e0)


Congratulations!
You have now completed all ‘PBL Progressive’ projects and are ready to move on to more complex and fun ‘PBL Professional’ projects!!!
