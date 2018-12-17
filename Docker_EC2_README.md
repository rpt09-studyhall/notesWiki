# Docker / EC2 Notes

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

### Setup your SSH key (.pem)

Now we have to place our public key in our ssh folder. (make sure you have a `~/.ssh` folder, create if doesn't exist.). On your dashboard find the Public DNS Name, should look maybe like `c2-54-172-80-40.compute-1.amazonaws.com` Your user will automatically be created as `ec2-user`

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
# cool you're in. this is how we can communicate with our instance, for now exit to return
[ec2-user@ip-172-31-51-246 ~]$> exit
# wahlah!
$> 


```

## Install docker  in instance

TODO

## Create local Dockerfile and sample app

TODO

## Copying files to your EC2 Instance

So to copy stuff from your local machine to the EC2 Instance, we can use `scp`. If `myApp/` is your rootlevel application with the Dockerfile and other goodies, cd into it and do the following

``` sh
# go into app
$> cd ./myApp/
# copy it over (make sure your path exists on the server)
$> scp -i "~/.ssh/mine.pem" -r .  ec2-user@ec2-54-172-80-40.compute-1.amazonaws.com:/home/ec2-user/app
```

