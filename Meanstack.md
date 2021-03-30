# ## A simple MEAN Stack Project on Ubuntu

MEAN stands for MongoDB, Express.js, AngularJS, and Node.js. MEAN is an end-to-end JavaScript stack largely used for cloud-ready applications.
MEAN is an open source web stack that is mainly used to create cloud-hosted applications. MEAN stack applications are flexible, scalable, and extensible, making them the perfect candidate for cloud hosting. The stack includes its own web server so it can be deployed easily. cloud.

In this tutorial we're going to implement a simple Book Register web form using MEAN stack.

## Step 1: Install NodeJs


Update ubuntu

`sudo apt update`

Upgrade ubuntu

`sudo apt upgrade`

Install NodeJS

`sudo apt install -y nodejs npm express mongoose`

The "-y" flag is a "yes" to answer all default prompts during the installation of these dependencies.

Let’s take a quick look at some of the installed packages:

- express: Express is a fast and lightweight web framework for Node.js. Express is an essential part of the MERN stack.
- npm: a package manager for the NodeJS
- mongoose: A Node.js framework which lets us access MongoDB in an object-oriented way.

Once the installation is done, you can check the Node version with 

`node --version`

```
Output:

v10.19.0
```

`npm --version`

`express --version`

## Step 2: Install MongoDB
MongoDB is a non-relational database that stores data in flexible, JSON-like documents. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

`$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

```
Output:

Executing: /tmp/apt-key-gpghome.UzNe71Z8o2/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

gpg: key BC711F9BA15703C6: public key "MongoDB 3.4 Release Signing Key <packaging@mongodb.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

```

`molokofi@molokofi:~$ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

```
Output:

deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse

```

Install MongoDb and start it immediately after installation

`sudo apt install -y mongodb`

`sudo service mongodb start`

You can verify that the service is up and running by running:

`$ sudo systemctl status mongodb`

```
Output:

● mongodb.service - An object/document-oriented database
     Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset:>
     Active: active (running) since Mon 2021-03-29 19:06:21 WAT; 5min ago
       Docs: man:mongod(1)
   Main PID: 13723 (mongod)
      Tasks: 23 (limit: 3355)
     Memory: 42.8M
     CGroup: /system.slice/mongodb.service
             └─13723 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /et>


```

Next, we install ‘body-parser'package

We need ‘body-parser’ package to help us process JSON files passed in requests to the server.

`sudo npm install body-parser`

> It's important to not that 'body-parser' has been depreacted in Node version > 4.0 (This explains the warnings you get when you run 'node server.js'). To be replaced by a more functional express.json which help parse our json files and even more.
> We won't delve into the implementation as this is just a simple tutorial

Next, create a folder named ‘Books’

`mkdir Books && cd Books`

In the Books directory,initialize node and the package manager with:

`npm init -y`

You should end up with a package.json file that looks like this:

```

{
  "name": "books",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}

```

Add a file to it named server.js

vi server.js

Copy and paste the web server code below into the server.js file.

```
const express = require('express');
const app = express();

app.use(express.static(__dirname + '/public'));
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

app.set('port', 3300);
app.listen(app.get('port'), () => {
    console.log(`Server is poppin on: http://localhost:` + app.get('port'));
});

```

## Step 3: Set up routes in the DB Server

In ‘Books’ folder, create a folder named apps

`mkdir apps && cd apps`

Create a file named *routes.js*

`vi routes.js`

Copy and paste the code below into routes.js

```
var Book = require("./models/book");
module.exports = function (app) {
  app.get("/book", function (req, res) {
    Book.find({}, function (err, result) {
      if (err) throw err;
      res.json(result);
    });
  });
  app.post("/book", function (req, res) {
    var book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages,
    });
    book.save(function (err, result) {
      if (err) throw err;
      res.json({
        message: "Successfully added book",
        book: result,
      });
    });
  });
  app.delete("/book/:isbn", function (req, res) {
    Book.findOneAndRemove(req.query, function (err, result) {
      if (err) throw err;
      res.json({
        message: "Successfully deleted the book",
        book: result,
      });
    });
  });
  var path = require("path");
  app.get("*", function (req, res) {
    res.sendfile(path.join(__dirname + "/public", "index.html"));
  });
};

```

Also in the ‘apps’ folder, create a folder named models

`mkdir models && cd models`

Then create a file named book.js

`vi book.js`

Copy and paste the code below into ‘book.js’

```
var mongoose = require('mongoose');
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
module.exports = mongoose.model('Book', bookSchema);

```

## Step 4 - Access the routes with AngularJS

Change the directory back to ‘Books’

`cd ../..`

Create a folder named *public*

`mkdir public && cd public`

Add a file named script.js

`vi script.js`

Copy and paste the Code below (controller configuration defined) into the script.js file.

```
var app = angular.module("myApp", []);
app.controller("myCtrl", function ($scope, $http) {
  $http({
    method: "GET",
    url: "/book",
  }).then(
    function successCallback(response) {
      $scope.books = response.data;
    },
    function errorCallback(response) {
      console.log("Error: " + response);
    }
  );
  $scope.del_book = function (book) {
    $http({
      method: "DELETE",
      url: "/book/:isbn",
      params: { isbn: book.isbn },
    }).then(
      function successCallback(response) {
        console.log(response);
      },
      function errorCallback(response) {
        console.log("Error: " + response);
      }
    );
  };
  $scope.add_book = function () {
    var body =
      '{ "name": "' +
      $scope.Name +
      '", "isbn": "' +
      $scope.Isbn +
      '", "author": "' +
      $scope.Author +
      '", "pages": "' +
      $scope.Pages +
      '" }';
    $http({
      method: "POST",
      url: "/book",
      data: body,
    }).then(
      function successCallback(response) {
        console.log(response);
      },
      function errorCallback(response) {
        console.log("Error: " + response);
      }
    );
  };
});

```

Also in the ‘public’ folder, create a file named index.html

`vi index.html`

Paste in the following 

```
<!doctype html>
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

</html>
```

Change the directory back up to ‘Books’ and start the server by running this command:

`cd .. && node server.js`

The server should be up and running now on port 3300. 

curl -s <http://localhost:3300>

This should render an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

For this - you need to open port 3300 in your machine (I'm using a virtulabox).

`sudo ufw allow 3300`

You can check that this firewall rule is allowed by running:

`sudo ufw status`

```
Output:

Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
3300                       ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
3300 (v6)                  ALLOW       Anywhere (v6)    
```

This is how your Web Book Register Application will look like in browser:

![](https://github.com/Arafly/MEAN_Stack/blob/master/assets/finalimage.PNG)

>Remember to refresh on each addition or deletion of entries to see the changes

If you observe, you'd see Mongoose is working in the terminal, reflecting, updating, fetching each actions you take on the browser, concerning deletion and addition.

![](https://github.com/Arafly/MEAN_Stack/blob/master/assets/mongoose.PNG)

Congratulations! You've just set up your fully functional MEAN Stack on Ubuntu.