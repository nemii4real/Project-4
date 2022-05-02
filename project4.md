## Step 1: Install NodeJs


`sudo apt update`




`sudo apt upgrade`



![sudo update/upgrade](./images/sudo-update-upgrade.png)





Run this command

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`






`sudo apt install -y nodejs`




![sudo-install-y-nodes](./images/install-y-nodes.png)





`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`



`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`










![keysever updated](./images/key-server.png)








`sudo apt install -y mongodb`









To start server `sudo service mongodb start`





`sudo systemctl status mongodb` 




To enable mongodb or any file `sudo systemctl enable mongodb` replace the file name to which  you are trying to enable, such as mongodb etc.









![Mongodb](./images/monogodb-installed.png)






`sudo apt install -y npm` 




 Had  error messages running the command above, so I ran this command `npm -v` and was able to proceed.




![npm installed](./images/npm-installed.png)






`sudo npm install body-parser`








![body-parser installed](./images/body-parser.png)









`mkdir Books && cd Books`






In book directory intsall `npm init`



![npm init](./images/npm-init.png)





`vi server.js` type the command below

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




![server.js-installed](./images/server.png)


## Install Express and set up routes to the server

`sudo npm install express mongoose`






![npm-mongoose-installed](./images/ScreenShot_5_2_2022_5_13_06_AM.png)







In book directory create `mkdir apps && cd apps`






In apps dir run `vi routes.js` paste command below insde the empty file.

`var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};`





![app-dir-routes-file-created](./images/app-dir-routes-file-created.png)




In apps create dir `mkdir models && cd models`




In models dir run `vi book.js` to create book.js and paste command below




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





In book dir, create a public dir `mkdir public && cd public`




`vi script.js` run in public directory




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






![model,books,public,script-creadted](./images/mode-book-public-script.png)








Run command `vi index.html` paste command below in empty file



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



`cd ..` back to books run `node server.js` I had an error because I did not cd back to books.




![port-3300-running](./images/port-3300.png)





The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

`curl -s http://localhost:3300` run in a separate terminal





Open TCP port 3300 in your AWS Web Console for your EC2 Instance.




Copy public ip address and paste with port 3300 includeded `http://34.224.75.148:3300/`







![web book-running](./images/web-book.png)
