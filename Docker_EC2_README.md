


# Docker / EC2 Notes

  - [Docker / EC2 Notes](#docker--ec2-notes)
    - [About](#about)
    - [EC2 Setup](#ec2-setup)
      - [Launch instance on AWS](#launch-instance-on-aws)
      - [Setup your SSH key (.pem)](#setup-your-ssh-key-pem)
      - [make app folder and install Docker](#make-app-folder-and-install-docker)
    - [Create local Dockerfile and sample app](#create-local-dockerfile-and-sample-app)
      - [start with a hello world app](#start-with-a-hello-world-app)
      - [index.js](#indexjs)
      - [Dockerfile](#dockerfile)
    - [Copying files to your EC2 Instance](#copying-files-to-your-ec2-instance)
      - [Option 01: SCP [Not preferred]](#option-01-scp-not-preferred)
      - [Option 02: rsync with .gitignore [Preferred]](#option-02-rsync-with-gitignore-preferred)
    - [Build docker image and run it!](#build-docker-image-and-run-it)
    - [Open up your port on ec2!](#open-up-your-port-on-ec2)
    - [Killing a running docker container](#killing-a-running-docker-container)
    - [Shelling / excuting into your docker container](#shelling--excuting-into-your-docker-container)
    - [Advanced Dockerfile (with postgresql!)](#advanced-dockerfile-with-postgresql)
      - [installing more npm stuff](#installing-more-npm-stuff)
      - [editing our Dockerfile , node index, and supplying a schema.sql file for seeding!](#editing-our-dockerfile--node-index-and-supplying-a-schemasql-file-for-seeding)
      - [Voila it works!](#voila-it-works)

## About
Notes on Docker / EC2 . 
To get up and running [this is a great tutorial from amazon](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#install_docker) which is where most of the beginning info is setup.

## EC2 Setup 

### Launch instance on AWS

EC2 Is AWS's service which basically will you get you a basic linux box you can ssh into. This can then house our docker container. Complete the following steps

  - Launch an Instance from the [EC2 Dashboard](https://console.aws.amazon.com/ec2). First one is fine
  - Review, and hit Launch. 
  - If you don't have a keypair, click create, give it a name and download the `.pem` file which will be your public key. I name it like `mine.pem`
  - great now on your dashboard you should see your instance.

Should look like this.

![launch instances](https://i.imgur.com/L24I1Rsr.png)

### Setup your SSH key (.pem)

Now we have to place our public key in our ssh folder. (make sure you have a `~/.ssh` folder, create if doesn't exist.). On your dashboard find the Public DNS Name, should look maybe like `c2-54-172-80-40.compute-1.amazonaws.com` Your user will 
matically be created as `ec2-user`

``` sh
# move it to shell folder.
$>  cd path/to/downloaded/pem
$> mv mine.pem ~/.ssh
$> chmod 400 mine.pem
# great! now you can ssh into your instance by specifing pem key
$>  ssh -i "~/.ssh/mine.pem" ec2-user@ec2-54-172-80-40.compute-1.amazonaws.com
# expected result should be something like below
#
# /////////////////////////////////////////////////
# Last login: Mon Dec 17 00:46:31 2018 from c-98-207-98-227.hsd1.ca.comcast.net

#        __|  __|_  )
#        _|  (     /   Amazon Linux 2 AMI
#       ___|\___|___|
#
# /////////////////////////////////////////////////
#
# cool you're in. this is how we can communicate with our instance, lets go to our user folder and make an app folder
[ec2-user@ip-172-31-51-246 ~]$> cd /home/ec2-user
# cool yah made it to the ec2 party
```

### make app folder and install Docker

Most of this info is taken from [amazon](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#install_docker)

``` sh
[ec2-user@ip-172-31-51-246 ~]$> mkdir app
# cool now lets install docker its pretty simple (taken from the amazon tutorial mentioned in the intro of this readme)
# update package manager existing packages (on centOs it uses yum)
[ec2-user@ip-172-31-51-246 ~]$> sudo yum update -y
# this installs docker 
[ec2-user@ip-172-31-51-246 ~]$> sudo amazon-linux-extras install docker
# start docker daemon
[ec2-user@ip-172-31-51-246 ~]$> sudo service docker start
# give ec2-user admin docker privelages (so sudo not needed)
[ec2-user@ip-172-31-51-246 ~]$> sudo usermod -a -G docker ec2-user
# logout 
# log back in to activate priveleges , should be good
[ec2-user@ip-172-31-51-246 ~]$> docker info
# and now exit to return, we good for now!
[ec2-user@ip-172-31-51-246 ~]$> exit
# wahlah!
$> 
```

## Create local Dockerfile and sample app

### start with a hello world app

For my sample app it looks basically like this.

``` sh
.
├── .git
├── .gitignore
├── Dockerfile
├── index.js
├── node_modules
├── package-lock.json
└── package.json
```

To create this do the following commands

``` sh
# init git
$> git
# init npm
$> npm init
$> npm install --save express
# touch some files and  (add node_modules/ to gitignore)
$> touch index.js && .gitignore && vi .gitignore
# great lets edit our basic app!
$> code index.js
```
### index.js
``` js

const app = require('express')();
const PORT = 80;

app.get('/', (req, res) => {
  res.status(200).send("oh hai from chris from docker from ec2!")
});

app.listen(PORT, () => {
  console.log(`listening on port ${PORT}..`);
});

```

### Dockerfile

Our Dockerfile is not doing a lot of stuff in this case. We're starting with a node image, and then installing npm dependencies and starting node.
We're then running our index.js file and exposing it on port 80! Note: Exposing means opening it up to other parts of its internal container, but not to the outside world! Another thing to note about `RUN` vs `CMD`:

  - `RUN` - Runs at `build` time. be wary of doing things like setting up users and starting services in this, because this process is ended and often are run in subcontainers. 
  - `CMD` - Runs at `runtime` so starting node, or postgres or populating db, should happen then!
``` dockerfile
FROM node:8.9.4
# copy files from current folder to container folder
COPY . .

# node start up
RUN npm install
CMD node index.js
EXPOSE 80
```

## Copying files to your EC2 Instance

### Option 01: SCP [Not preferred]

So to copy stuff from your local machine to the EC2 Instance, we can use `scp`. If `myApp/` is your root level application with the Dockerfile and other goodies, cd into it and do the following. 
``` sh
# go into app
$> cd ./myApp/
# copy it over (make sure your path exists on the server)
$> scp -i "~/.ssh/mine.pem" -r .  ec2-user@ec2-54-172-80-40.compute-1.amazonaws.com:/home/ec2-user/app
```

### Option 02: rsync with .gitignore [Preferred]

So I tried scp and then realized (hmm i want to ignore a bunch of stuff like `node_modules/`) and also wanted maybe it to only include what `.git` is tracking. I also wanted to only sync diffs, not a dumb copy, so thats where `rsync` can come in!  We can do the syncing of diffs, but also read our git logic for an added bonus!

Looks like this...

``` sh
$> rsync --include .git --exclude-from="$(git -C [MY_SOURCE_DIR] ls-files --exclude-standard -oi --directory >.git/ignores.tmp && echo .git/ignores.tmp)" -rave "ssh -i ~/.ssh/mine2.pem" [MY_SOURCE_DIR]  ec2-user@ec2-54-172-80-40.compute-1.amazonaws.com:[MY_TARGET_DIR] 
```

And in action from scratch.

``` sh
# go into app
$> cd ./myApp/
# make sure you have a git directory (skip the next three lines if you do)
$> git init
# add some stuff to gitignore
$> touch .gitignore && vi .gitignore
$> git add . 
$> git commit '[init]'
# now from now one we can run this command from (its long yeah)
# why its long is because it also reads your git ignore file (note src '.' and ssh key and target .)
$> rsync --include .git --exclude-from="$(git -C . ls-files --exclude-standard -oi --directory >.git/ignores.tmp && echo .git/ignores.tmp)" -rave "ssh -i ~/.ssh/mine2.pem" . ec2-user@ec2-54-172-80-40.compute-1.amazonaws.com:/home/ec2-user/app
```

I reccommend throwing this bit in a script.

## Build docker image and run it!

Cool lets return to our ssh'd in ec2 instance that should now have all our files on it. If you forgot how to get back in ^^ look above.

Lets discuss nomenclature. Docker `image` vs `container`.

  - `image` a docker built snapshot of your linux kernel + added stuff. this is built according to your `Dockerfile` but not quite running. Anything in the `RUN` series of commands is already in this.
  - `container` this is a running instance of your `image`. When you run `docker run ..` you are actually creating a new container instance and also running the `CMD` operation from the Dockerfile.

``` sh
# make sure you're in your folder and the docker file is there! hopefully all your ignored files are also not there!
[ec2-user@ip-172-31-51-246 ~]$> cd home/ec2-user/myApp/
# BUILD! -t stands for tag or title for your image, dont forget the . which says THIS directory
# note: --rm , says remove previous image if exists
[ec2-user@ip-172-31-51-246 ~]$> docker build --rm -t hello-world .
# SEE IMAGE! you will see your image(s) by doing this command. it will look like:
#
#
# REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
# hello-world         latest              c6b228cb1da5        20 seconds ago      791MB
# my_app2             latest              d03be9113ce8        34 hours ago        769MB
# ...
#
# this will show you currently running ones, which hello-world we haven't run yet so it shouldnt be there! looks like..
#
# CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
#
[ec2-user@ip-172-31-51-246 ~]$> docker ps
# cool beans. lets run this bad boy or girl, and we have a few flags. 
# -i displays output in this terminal
# -p 80:80 maps the container port (which is running express) to our host port (ec2 instance!) very important
[ec2-user@ip-172-31-51-246 ~]$> docker run -i -p 80:80 hello-world
# you should see our node console log
#
# listening on port 80..
#
# woop woop!

```

## Open up your port on ec2!

Ok so our instance with docker is good to go, but amazon isn't opening up any ports outside of ssh by default. So if we want browsers to ping us, we need to open up a port aka port 80. To do this part go in your ec2 console and go to `network > security group` and hit `edit` and basically open your desired port to HTTP port `80` and ip I set anywhere but maybe you can be more selective . you do you.

![port opening](https://i.imgur.com/zqEzBZEr.png)

Now if you go to your site:

![it works!](https://i.imgur.com/XPPoivs.png)

## Killing a running docker container

Unfortunately `Ctrl+c` or `exit` wont work. Ok so now we want to kill our docker image, maybe we have some changes to the `Dockerfile` or something. The best way is to open a new tab and do  `docker stop`...below are detailed instructions.

  - open a new terminal tab and ssh into ec2
  - run  `docker ps` to find the container ID
  - run `docker stop [Container ID]`
  - wait...
  - it should be killed and now you can return to your other tab :D

## Shelling / excuting into your docker container

A really handy way to build out your Dockerfile, might be to make a super simple runtime env, then shell in and test your commands. You can do so by doing `docker exec [flags] [container_id] [command]`


## Advanced Dockerfile (with postgresql!)

So now I will go over a slightly more complex `Dockerfile` which will run both `node` and `postgresql` in one image/container. First let see our files on file system. Im going to highlight the new files with a `*`.

**Note:** we have an `.env` file for storing database credentials. THis is probably in your .gitignore so if you're copying this to another host machine like on your ec2 instance, make sure you create this file on there as well if you're using a rsync + .gitignore tactic mentioned above, as this file would not be copied over in that workflow.

**Note:** you'll also see we have a `schema.sql` file which stores some seed data. this is optional obviously but if you want to start with some base data this is here.

Files for my local app (* = new added files vs. from previous exercise)
``` 
.
├── .env *
├── .git
├── .gitignore
├── Dockerfile
├── index.js
├── launch.sh
├── node_modules
├── package-lock.json
├── package.json
└── schema.sql *
```
### installing more npm stuff
So we're also going to want to install some extra dependencies in `npm package.json`. You might already have express if you're building from first tutorial
``` sh
# install pg (postgresql), express (if not already), and dotenv (for config env vars)
$> npm install --save express pg dotenv
```

### editing our Dockerfile , node index, and supplying a schema.sql file for seeding!

Lastly I'll show you the edited files which should be straight forward.

**schema.sql**

this file is just a pretty dumb sql file which would create a table called `test` and fill it with some data

``` sql
DROP TABLE IF EXISTS test;

CREATE TABLE test (
      id SERIAL,
      date bigint,
      name text, 
      age int, 
      comment text,
      PRIMARY KEY (id)
);

INSERT INTO test (name, age, comment) VALUES ('bill',25,'isnt');
INSERT INTO test (name, age, comment) VALUES ('ted',32,'this');
INSERT INTO test (name, age, comment) VALUES ('sally',45,'just');
INSERT INTO test (name, age, comment) VALUES ('suzan',18,'so');
INSERT INTO test (name, age, comment) VALUES ('betty',37,'interesting');
INSERT INTO test (name, age, comment) VALUES ('tim',52,'bruh');
```

**.env**

Note if this is in your .gitignore, then make sure you recreate this somehow (by touch, vim + writing it OR copying it over via `scp` or `rsync`) on your ec2 instance!

```
DB_HOST=localhost
DB_USER=docker
DB_PASS=docker
```
**index.js**
Ok now the guts. First is our actual web application, it literally just prints out our dn entries and some messages. 

``` js
const app = require('express')();
const { Client } = require('pg');
const dotenv = require('dotenv').config();

const client = new Client({
  database: 'test_db',
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASS,
});

let CONNECTED = false;

client.connect()
  .then(() => {
    CONNECTED = true;
  })
  .catch(e => {
    console.log('oops couldnt connect to db!: ', e)
  });

app.use((req, res, next) => {
  console.log('incoming request:', req.method, ' --> ', req.url);
  next();
});

const PORT = 80;

app.get('/', (req, res) => {
  if (CONNECTED) {
    client.query('SELECT * FROM test', []).then(({rows}) => {
      res.status(200).send(JSON.stringify({message: "oh hai from chris from docker from ec2 we also have some db entries now!", rows}));
    }).catch((e) => {
      res.status(500).send(JSON.stringify({error: "oh hai you connected to docker and ec2, but something went wrong with querying the db!" + e.toString()}));
    });
  } else {
    res.status(500).send(JSON.stringify({error: "oh hai you connected to docker and ec2, but couldn't connect to db!"}));
  }
});


app.listen(PORT, () => {
  console.log(`listening on port ${PORT}..`);
})

```
**Dockerfile**

Our dockerfile is just slightly more verbose, as you can see it basically now will add postgresql, create a role called `docker` with password `docker` and create a db called `test_db` using role `docker`. Note: You can just use the given `postgres` user but this is just to show how you can add credentials. 

Lastly, it connects using the docker user and feeds in the schema.sql file to seed the database with a table called `test`. We then start our node web application :)

``` dockerfile
FROM node:8.9.4

# copy files from current folder to images app folder
# why make a new app directory in our image?
# (optional) we make an app folder in our container so we can avoid all the root level messy mess
RUN mkdir app
COPY . ./app
WORKDIR app

# install psql
RUN apt-get update \
  && apt-get install -y postgresql postgresql-contrib \
  && apt-get install sudo
# install npm
RUN npm install
#expose pg + node
EXPOSE 80 5432
# run our shell script
CMD /etc/init.d/postgresql start \
&& sudo -u postgres psql --command "CREATE USER docker WITH SUPERUSER PASSWORD 'docker';" \
&& sudo -u postgres createdb -O docker test_db \
&& sudo PGPASSWORD='docker' -u postgres psql -d test_db -U docker -h 127.0.0.1 -p 5432 < schema.sql \
&& node index.js

```

### Voila it works!

![it works once more!](https://i.imgur.com/cFcgEKH.png)