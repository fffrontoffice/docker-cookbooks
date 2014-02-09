# Createing a dashing container


Virtualbox will be used to run an Ubuntu linux virtual machine. Vagrant will be used to start, stop and initially build the virtual machine with docker installed.

The native docker client for Mac OS X will be used to do all docker related activities.

See my other recipe -> [Setting up a Docker dev environment on Mac OS X](https://github.com/johnzan/docker-cookbooks/blob/master/MacOSX_development_environment/README.md) for everything you need to get up and running in Mac OS X.

 
#### Step 0 - Prepare the dev environment

Create a directory for your dashing development project. I use `~/docker_dev` as the root for all my docker related stuff.

    [~]$ mkdir ~/docker_dev/dashing
    
        
#### Step 1 - Create a baseline image
I used a trusted ruby docker image as the baseline for this image.

Dashing requires a javascript library to be installed, but does not include one by default.


I choose to use Node.js in this example.

In order to prepare for a Node.js install some prereqs are needed.

These are:

    apt-get install g++ curl libssl-dev apache2-utils -y
    apt-get install git-core -y

Next we install Node itself with the following commands:

    git clone git://github.com/ry/node.git
    cd node
    ./configure
    make
    sudo make install

Then Dashing is installed with the following command:

    gem install dashing

At this point we should have everything needed to create the default demo dashing appliction.

The commands:

    dashing new mydashboard
    cd mydashboard
    bundle
	dashing start
	
will create a dashing application in the directory mydashboard. Change directory into `mydashboard` and run the `bundle` command. Then finally, `dashing start` to start up your new dashing application on port 3030.

The Dockerfile below has everything needed.

Copy the Dockerfile to your computer and cd into the directory where it is saved.

To build the dashboard container

    [dashing2]$ docker build -t yourname/dashing .

To launch the continer interactively

    [dashing2]$ docker run -i -t yourname/dashing

To launch and run the container detached 

    [dashing2]$ docker run -d yourname/dashing


#### Dockerfile to install dashing

    # Dockerfile for dashing 
    # http://shopify.github.io/dashing/#overview

    FROM ahromis/ruby

    MAINTAINER John Zanchetta <johnzan@gmail.com>

    RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
    RUN apt-get update
    RUN apt-get upgrade -y

    # Prep for node install
    Run apt-get install g++ curl libssl-dev apache2-utils -y
    RUN apt-get install git-core -y

    # Install Node
    RUN git clone git://github.com/ry/node.git
    RUN cd node;./configure;make;sudo make install

    # Install Dashing
    RUN gem install dashing
    
    # Create a default dashboard
    RUN dashing new mydashboard
    RUN cd mydashboard;bundle

	# Launch the dashboard
    CMD cd /mydashboard;dashing start
 
