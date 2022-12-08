# project-3
## SIMPLE-TO-DO-APPLICATION-ON-MERN-WEB-STACk

MERN Web stack consists of the following components:
* MongoDB: A document-based, No-SQL database used to store application data in a form of documents.
* ExpressJS: A server-side Web Application framework for Node.js.
* ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
* Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.


## STEP 1 – BACKEND CONFIGURATION
Update ubuntu
```
sudo apt update
```
Upgrade ubuntu
```
sudo apt upgrade
```
Let’s get the location of Node.js software from Ubuntu repositories.
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
Install Node.js with the command below
```
sudo apt-get install -y nodejs
```
![Screenshot (66)](https://user-images.githubusercontent.com/111396874/206141988-2bdcf863-4e5a-4684-98f7-b410d2baa82d.png)
Application Code Setup
Create a new directory for your To-Do project:
```
mkdir Todo
```
Now change your current directory to the newly created one:
```
cd Todo
```
Next, you will use the command npm init to initialise your project, so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.
```
npm init
```
![Screenshot (67)](https://user-images.githubusercontent.com/111396874/206142832-eda68b58-84b6-4b24-8fd8-15a498292f5c.png)

### Installing Expressjs
To use express, install it using npm:
```
npm install express
```
Now create a file index.js with the command below
```
touch index.js
```
Run ls to confirm that your index.js file is successfully created
Install the dotenv module
```
npm install dotenv
```
Open the index.js file with the command below
```
vim index.js
```
Type the code below into it and save. Do not get overwhelmed by the code you see. For now, simply paste the code into the file.
```
const express = require('express');
require('dotenv').config();
 
const app = express();
 
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
});
```
Notice that we have specified to use port 5000 in the code. This will be required later when we go on the browser.
Use: w to save in vim and use: qa to exit vim
Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:
```
node index.js
```
If everything goes well, you should see Server running on port 5000 in your terminal.
![Screenshot (69)](https://user-images.githubusercontent.com/111396874/206143937-682ae63b-48fb-4dfd-9b89-fc086471aa7f.png)
Now we need to open this port in EC2 Security Groups. Refer to Project 1 Step 1 – Installing the Nginx Web Server. There we created an inbound rule to open TCP port 80, you need to do the same for port 5000, like this:
![Screenshot (71)](https://user-images.githubusercontent.com/111396874/206144372-a5041fa9-d15b-4f5e-a427-15e8f252e94b.png)
Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000:
```
http://<PublicIP-or-PublicDNS>:5000
```
It should show this:
![Screenshot (71)](https://user-images.githubusercontent.com/111396874/206144760-a0e34e0a-0de9-4960-8e8f-80955f8b437c.png)

#### Routes
There are three actions that our To-Do application needs to be able to do:
* Create a new task
* Display list of all tasks
* Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.
For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes
```
mkdir routes
```
``
Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2
``
Change directory to routes folder.
```
cd routes
```
Now, create a file  with the command below
```
touch api.js
```
Openand create the file **api.js** with the command below
```
vim api.js
```
Copy below code in the file. (Do not be overwhelmed with the code)
```
const express = require ('express');
const router = express.Router();
 
router.get('/todos', (req, res, next) => {
 
});
 
router.post('/todos', (req, res, next) => {
 
});
 
router.delete('/todos/:id', (req, res, next) => {
 
})
 
module.exports = router;
```
Moving forward let create Models directory.

#### MODELS
We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document.
To create a Schema and a model, install **mongoose** which is a Node.js package that makes working with mongodb easier.
Change directory back Todo folder with ``cd ..`` and install Mongoose
```
npm install mongoose
```
now run this command:
```
mkdir models && cd models && touch todo.js
```
Open the file created with ``vim todo.js`` then paste the code below in the file:
```
const mongoose = require('mongoose');
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
 
module.exports = Todo;
```
Now we need to update our routes from the file **api.js** in ‘routes’ directory to make use of the new model.
In Routes directory, open api.js with ``vim api.js``, delete the code inside with ``:%d`` command and paste there code below into it then save and exit
```
const express = require ('express');
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
 
module.exports = router;
```
The next piece of our application will be the MongoDB Database

#### MongoDB Database
We need a database where we will store our data. For this, we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS)

So I already set up my MongoDB by setting up my cluster:
![Screenshot (74)](https://user-images.githubusercontent.com/111396874/206330774-fac837a2-fd51-4e3e-84d6-5e5ab4309fee.png)

Then I allowed access to my MongoDB datbase from anywhere(not usually safe):
![Screenshot (72)](https://user-images.githubusercontent.com/111396874/206330984-7c042789-cc54-4a0b-9940-d6692c94f90c.png)

In the **index.js** file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.
Create a file in your Todo directory and name it **.env**.
```
vi .env
```
Add the connection string to access the database in it, just as below:
```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>retryWrites=true&w=majority'
```
Ensure to update <username>, <password>, <network-address> and <database> according to your setup

Now we need to update the _index.js_ to reflect the use of .env so that Node.js can connect to the database.
Simply delete existing content in the file, and update it with the entire code below.
To do that using ``vim``, follow below steps
Open the file with ``vim index.js``
* Press esc
* Type :
* Type %d
* Hit ‘Enter’
The entire content will be deleted, then,
Press i to enter the insert mode in vim
Now, paste the entire code below in the file.
```
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
```
Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the **index.js** application file.

 Start your server using the command:
```
node index.js
```
 You shall see a message ‘Database connected successfully’, if so – we have our backend configured. Now we are going to test it.
 ![Screenshot (75)](https://user-images.githubusercontent.com/111396874/206548537-878edac5-75b7-4517-b423-02eb999172d7.png)


