MongoDB + Mongoose Notes 
=========================

Ok so the first thing is to make sure you have mongo installed and the server is running. Then we'll go into
detail on mongose use

Table of Contents
----
- MongoDB
  - [Install](#instal-mongo-if-not-already-installed)
  - [Check if Running](#check-if-mongo-is-running)
  - [Mongo REPL (cli)](#open-mongo-repl)
- Mongoose
  - [Set up connection](#set-uo-connection)
  - [Schema](#schema)
  - [Typical CRUD Operations](#typical-crud-operations)


Mongo cli setup
----------------
First things first lets get mongo setup for cli use.

## install mongo if not already installed

If not already installed do so by running the following. Make sure you have `homebbrew` installed.

``` sh
# update brew
$> brew update
# install mongodb
$> brew install mongodb
```

## check if mongo is running

Now check if your mongodb service is running...

``` sh
# check running services
$> brew services list
# you should see something like that
#Name    Status  User        Plist
# mongodb started YOU  /Users/YOU/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
```
## open mongo repl

To open mongo repl 

``` sh
# type mongo 
$> mongo
# get commands
$> help
# show dbs
$> show dbs
# set a db
$> use myDB
# show collections in myDB
$> show collections
# ex. 
#   users
#   repos
# get all users
$> db.users.find()
# get user where name is github_handle = 'octocat'
$> db.users.find({github_handle: 'octocat'})
```

Mongoose
---------

First thing is we can make a db file for our `ORM`. lets make a `db/db.js` file.
Now we'll just need to install a few things `mongoose` and `bluebird`.

``` sh
# install the thing
$> npm install --save bluebird mongoose
```

## Set up connection

First thing we're gonna do is setup main setup and connect

``` js
// define mongoose
const mongoose = require('mongoose');
// define bluebird
const bluebird = require('bluebird');
// set bluebird as promise handler (mongoose promise built-ins are deprecated)
mongoose.Promise = bluebird;

// whats the mongo uri, process.env.MONGODB_URI for heroku or deployment
// the other is an aribitary db name localhost/myDB
const mongoURI= process.env.MONGODB_URI || 'mongodb://localhost/myDB';
// finally we will connect.. willConnect returns a promise btw..
const willConnect = mongoose.connect(mongoURI, {useMongoClient: true});
...
```

## Schema

Now we're going to setup a schema

``` js
...
// define user schema
const userSchema = mongoose.Schema({
    name: { type: String, unique: true  },
    rank: Number,
    created_at: Date,
}
// create it
const User = mongoose.model('User', userSchema);
...
```
## typical CRUD operations

Below could be some typical crud operations if dealing with promises. 
`find`,   `findOneAndUpdate` (creation + updating), and `deleteOne`.


``` js
...
// now we can do stuff with it like export db to use as db.getAll()
module.exports = {
  getAll: () => {
    // first arg is filter, second is what to include...exec() returns promise
    return User.find({}, {github_handle: 1, 'updated_at': 1}).exec();
  },
  delete: (username) => {
    // similar to getAll
    User.deleteOne({github_handle: username}).exec();
  }
  updateOrCreate: (username) => {
    // we can create or update with same call
    return new Promise((resolve, reject) => {
      User.findOneAndUpdate({github_handle: username}, //condition
       data, //lets just create the new model
        {upsert: true, new: true, runValidators: true},
        function (err, doc) { // callback
          if (err) {
            reject('Error when trying to update user: ' + err);
          } else {
            resolve(doc);
          }
        }
      ));
    })
  }
}
