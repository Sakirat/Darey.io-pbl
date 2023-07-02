# Simple To-Do Application on MERN Web Stack

In this project, you are tasked to implement a web solution based on MERN stack in AWS Cloud.

## MERN = MongoDB + ExpressJS + ReactJS + Node.js
MongoDB: A document-based, No-SQL database used to store application data in a form of documents
ExpressJS: A server side Web Application framework for Node.js
ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/7e315034-705f-4860-9143-b6d622efccf8)

As shown on the illustration above, a user interacts with the ReactJS UI components at the application front-end residing in the browser. This frontend is served by the application backend residing in a server, through ExpressJS running on top of NodeJS.
Any interaction that causes a data change request is sent to the NodeJS based Express server, which grabs data from the MongoDB database if required, and returns the data to the frontend of the application, which is then presented to the user.

## Backend Configuration

using an instance set up on AWS console using Ubuntu AMI, connect to the instance on Terminal and run the following codes
Update Ubuntu with code

![connecting to instance](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/38bce3b9-0561-46a2-a2a6-b555f07e0af1)


`sudo apt update`

![updating ubuntu](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/a5aca27b-529a-429c-b8b2-aca1f0d3f746)


Upgrade Ubuntu with code

`sudo apt upgrade`

![sudo apt upgrade](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/52b4ada9-32ab-4a65-829e-6ae2ef039f72)


Lets get the location of Node.js software from Ubuntu repositories.

`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

install Node.js on the server with code below

`sudo apt-get install -y nodejs`

The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts. Verify the node installation with the command below

`node -v`

Verify the npm installation with the command below

`npm -v`

![install Nodejs NPM CHECK VERSION](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/f2cd731b-5e8e-46cf-8986-48925914011a)


### Application Code Setup

Create a new directory for your To-Do Project with code below

`mkdir Todo`

Run the command below to verify that the Todo directory is created with ls command

`ls`

![create directory Todo n run ls](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/45706432-31fa-4286-9319-46994e1a085a)


In order to see some more useful information about files and directories, you can use following combination of keys ls -lih – it will show you different properties and size in human readable format. You can learn more about different useful keys for ls command with ls --help. Now change your current directory to the newly created one:

`cd Todo`

Next, you will use the command npm init to initialise your project, so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.

`npm init`

Run the command ls to confirm that you have package.json file created.

![initialise project with npm init](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/aaa4fb67-895b-4509-8f71-fd105ebc889e)


## Install ExpressJS
Remember that Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore it simplifies development, and abstracts a lot of low level details. For example, Express helps to define routes of your application based on HTTP methods and URLs. To use express, install it using npm code below

`npm install express`

Now create a file index.js with the command below

`touch index.js`

Run ls to confirm that your index.js file is successfully created
Install the dotenv module with code below

`npm install dotenv`

Open the index.js file with the command below

`vim index.js`

Type the code below into it and save

`const express = require('express');
require('dotenv').config();

const app = express();`

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});`

Notice that we have specified to use port 5000 in the code. This will be required later when we go on the browser. Use :w to save in vim and use :qa to exit vim
Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:

`node index.js`

If every thing goes well, you should see Server running on port 5000 in your terminal.
Now we need to open this port in EC2 Security Groups. 

![opening port 5000 on the concole](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/06b1b6c7-325f-4d3d-a9a3-937ac917967a)

Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000:Quick reminder how to get your server’s Public IP and public DNS name:
1) You can find it in your AWS web console in EC2 details
2) Run `curl -s http://169.254.169.254/latest/meta-data/public-ipv4` for Public IP address or `curl -s http://169.254.169.254/latest/meta-data/public-hostname` for Public DNS name.

![welcome to Express on browser](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/de4ff556-40e6-433f-99d1-e3e3bef88bcf)

### Routes
There are three actions that our To-Do application needs to be able to do:
1) Create a new task
2) Display list of all tasks
3) Delete a completed task
Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE. For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes with the command below

`mkdir routes`

Change directory to routes folder.

`cd routes`

Now, create a file api.js with the command below

`touch api.js`

Open the file with the command below

`vim api.js`

Copy below code in the file

`const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;`

![vim entry into routes file](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/690a56d0-0880-4c60-a59c-354686d5fe8a)

Moving forward let create Models directory.

### Models

Now comes the interesting part, since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.
A model is at the heart of JavaScript based applications, and it is what makes it interactive.
We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document.

In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties. To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier. Change directory back Todo folder with cd .. and install Mongoose.

`npm install mongoose`

![install mongoose](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/ca4cac93-d4e0-4b56-a5b1-bea294365f33)

Create a new folder models

`mkdir models`

Change directory into the newly created ‘models’ folder with

`cd models`

Inside the models folder, create a file and name it todo.js

`touch todo.js`

 All three commands above can be defined in one line to be executed consequently with help of && operator, like this:

 `mkdir models && cd models && touch todo.js`

 Open the file created with `vim todo.js` then paste the code below in the file

 ![using vim to wtite into todojs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/416b56ef-40bf-476b-b3e8-baec978cd58a)


`const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;`
 
Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model. In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit



`const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;`

## MongoDB Database

We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Follow the sign up process, select AWS as the cloud provider, and choose a region near you. Complete the get started checklist.

![MongoDB](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/2885ef8a-5812-4d8c-ac83-a1e95b1b9037)

Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing) and make sure you change the time of deleting the entry from 6 Hours to 1 Week

![mongoDb 6hours to 1week](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/b0fd6c30-7652-402a-9aeb-4a7ebdb616e2)

In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now. Create a file in your Todo directory and name it .env.

`touch .env`
`vi .env`

Add the connection string to access the database in it, just as below

`DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`

Ensure to update <username>, <password>, <network-address> and <database> according to your setup

![writing vim into todo to load mongo database](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/1087c058-f148-415c-b2fc-887c530a27da)

Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.
Simply delete existing content in the file, and update it with the entire code below.
To do that using vim, follow below steps
1. Open the file with vim index.js
2. Press esc
3. Type :
4. Type %d
5. Hit ‘Enter’
The entire content will be deleted, then,
6. Press i to enter the insert mode in vim
7. Now, paste the entire code below in the file.

`
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
`
![writing new entry into indexjs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/67d133f7-28a5-4a83-aa6f-c3eb7c70a5a1)

Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.
Start your server using the command

`node index.js`

You shall see a message ‘Database connected successfully’, if so – we have our backend configured. Now we are going to test it.

![start server using node command](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/77ad3fbb-4e6d-4ece-81c9-734bc871aeb2)

### Testing Backend Code without Frontend using RESTful API

So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfull API. Therefore, we will need to make use of some API development client to test our code. In this project, we will use Postman to test our API. Install Postman and also learn howto perform CRUD operations on Postman

You should test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code. Now open your Postman.
create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database. Note: make sure your set header key Content-Type as application/json

![using postman Post command](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/1409f7c1-3f9b-4b5b-bec1-d0ec0e6fc520)


Create a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).

![using postman Get command](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/591890d8-606a-416e-8f0d-bc87c576b7a4)

