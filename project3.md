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

![getting the location of Node Js software from Ubuntu repo](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/6e89ce64-2287-4f55-b528-0e13d8271e9b)

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

![writing new entry into indexjs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/48103e5a-23b1-4657-8ffc-a06e5824772e)

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

![delete vim in apijs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/284dc41a-96e0-41d8-aaee-93701e511b34)

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

   ![deleting vim indexjs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/4b5c7f07-86f9-4a28-b328-ce94567244d4)

`const express = require('express');
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

## Frontend Creation

Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app. In the same root directory as your backend code, which is the Todo directory, run

 `npx create-react-app client`

This will create a new folder in your Todo directory called client, where you will add all the react code.

#### Running a React App

Before testing the react app, there are some dependencies that need to be installed.

Install concurrently. It is used to run more than one command simultaneously from the same terminal window.

`npm install concurrently --save-dev`

Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.

`npm install nodemon --save-dev`

In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
`"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},`

#### Configure Proxy in package.json

Change directory to ‘client’

`cd client`

Open the package.json file

`vi package.json`

Add the key value pair in the package.json file "proxy": "http://localhost:5000"

The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, ensure you are inside the Todo directory, and simply do:

`npm run dev`

![npm run dev 2](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/33d2ade2-0e7a-4d06-bf40-c7fb87daf1e5)

Your app should open and start running on localhost:3000
Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule. You already know how to do it.

![our web at port 3000](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/82946c42-ee0e-4f2a-b162-0031de772f69)

#### Creating your React Components

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component. From your Todo directory run

`cd client`

move to the src directory

`cd src`

Inside your src folder create another folder called components

`mkdir components`

Move into the components directory with

`cd components`

![make directory components in todo_clients_src](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/5cb9d5b4-0115-48d2-9bc7-33ddb9225b03)

Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.

`touch Input.js ListTodo.js Todo.js`

![creating 3 files in components](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/296bac9a-0f0b-409e-8186-114cb4fed182)

Open Input.js file

`vi Input.js`

Copy and paste the following

`import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input`

To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios. Move to the src folder

`cd ..`

Move to clients folder

`cd ..`

Install Axios

`npm install axios`

Go to ‘components’ directory

`cd src/components`

After that open your ListTodo.js

`vi ListTodo.js`

in the ListTodo.js copy and paste the following code

`import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo

Then in your Todo.js file you write the following code

import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;`

We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.
Move to the src folder

`cd ..`

Make sure that you are in the src folder and run

`vi App.js`

Copy and paste the code below into it

`import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;`

![writing into Appjs](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/5d548264-3b1f-4850-8cf1-5bfff349b8fc)

After pasting, exit the editor. In the src directory open the App.css

`vi App.css`

Then paste the following code into App.css:

`.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}`

![writing into Appcss](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/abef528d-fd30-4ae5-b7f7-06c7e8b46608)


Exit
In the src directory open the index.css

`vim index.css`

Copy and paste the code below:

`body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}`

![writing into indexcss](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/6df62e04-889b-4c4c-bbc0-dfe1cc70ae3f)

Go to the Todo directory

`cd ../..`

When you are in the Todo directory run:

`npm run dev`

Assuming no errors when saving all these files, our To-Do app should be ready and fully functional with the functionality discussed earlier: creating a task, deleting a task and viewing all your tasks.

![my todo website final job](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/2af6578f-2323-42f1-81d6-71886f7306bf)

Congratulations
In this Project #3 you have created a simple To-Do and deployed it to MERN stack. You wrote a frontend application using React.js that communicates with a backend application written using Expressjs. You also created a Mongodb backend for storing tasks in a database.
