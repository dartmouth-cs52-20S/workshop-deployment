# CS52 Workshop: Deployment with Docker and VMs

## Why should you care?

While most of CS52 is about learning how to make super cool things for the web, what good are your brilliant creations if no one can see them? Container technologies (the most popular being a service called Docker) and virtual machines are two of the most popular methods to get your work out there. They can also be really helpful for testing and development before you deploy. In short, you *need* to be familiar with deployment options if you want to call yourself a true full-stack developer.

## Docker Terminology

Here's a little bit of terminology to help you get used to Docker. Feel free to refer back to this section as you need.
#### Image
Images form the basis of containers. Images are built from Dockerfiles, which are a set of instructions to build a complete and executable version of an application. Think of a Docker image as the blueprint for the container. The image contains your source code + all the dependencies you need to run the container.

The following command demonstrates all the docker images you have locally stored on your system.
``` 
$ docker images
```
![](https://i.imgur.com/prgEvb7.jpg)


TAG refers to a particular snapshot of the image; you can thing of an Image as a Git repository. Images can be committed with changes and have multiple versions. The TAG for a Docker image is similar to a commit for a Git repository.

Base images: Images with no parent image (usually images with an OS like ubuntu, busybox, debian)
Child images: Images that build on base images and add additional functionality

Official image: Images officially maintained and supported by Docker (one word long, typically)
User images: Images created by users. Typically build on base images

####  Container
Containers are created based on the  Docker image files and run the actual application. We create a container using 
```docker run [image]```

#### Docker Hub
https://hub.docker.com/
A remote registry of all available Docker images (somewhat similar to GitHub). 

#### Dockerfile
Simple text file that contains a list of commands the the Docker client calls while creating an image. Commands are almost identical to equivalent linux commands. 

## Make a Docker Account
Sign up for a Docker Account [here](https://hub.docker.com/signup). You will need this account to use Docker Playground.

## Sign-In to Docker Playground
We will use Docker Playground to simulate Docker in a web-browser rather than downloading Docker locally. Sign-in [here](https://labs.play-with-docker.com)!

**Note**: You only are allowed four (4) hours at a time on docker playground before all your work is deleted. This should be more than enough time to complete the tutorial, but make sure you don't stop part of the way through intending to come back and finish later. Unfortunately this is the price we pay for the playground being offered for free, but it is hard to complain.

## Running Docker
Once you have logged into Docker Playground, click "+Add New Instance" to create a Docker enviroment, which we will be using for the rest of the tutorial! To test Docker, run the following: ```docker run hello-world```

A few lines into the output you should see the following: ```Hello from Docker!
This message shows that your installation appears to be working correctly.```

Next, we will use the [Busybox](https://en.wikipedia.org/wiki/BusyBox) container.

1. Pull the container by running the following: ```docker pull busybox```
Docker's```pull``` command fetches the Busybox image from the Docker Resgistry (similar to a git repository), and saves it to our system. Run ```docker images``` to see which images are available on the local system. You should see busybox and hello-world!

2. Now we will run our first container! Execute ```docker run busybox```.

Hmm, nothing happened, right? Is something wrong? No! When ```run``` is called Docker finds the image, in this case busybox, loads the container, and runs the command in the container. In this case, we did not specify a command, so Docker simply found the image, and loaded the container. Now, run ```docker run busybox echo "hello from busybox"```. Here we specify a command to be run inside the container. Particularly, the ```echo``` command is run inside the container, and, thus, we see the output: "hello from busybox." Once, the command is executed, the Docker exits the container! Notice how quickly this entire process was accomplished!

3. Let's see what containers are currently running.
Run ```docker ps```. This command shows all of the containers that are currently running and their "process statuses" (hence ps). Nothing is currently running, so there is only a blank line. Not that interesting, huh? Now try ```docker ps -a```. This option shows all of the containers that have run. Note that the "Status" indicates that the containers have recently exited. Much more interesting!

4. Can you run multiple commands in a container?
Yes! It would be not be very dynamic if a container could only run one command. So let's try running multiple commands in a container!
Run```docker run -it busybox sh```. The ```-it``` flag** attaches an interactive tty in the container, which allows you to run other linux commands. Take some time to run your ~favorite commands~. If you're feeling uninspired try these out:
```
echo 'Tim is the GOAT'
rm -rf bin
```
Note: ```rm -rf bin``` will prevent any other commands like ```ls``` from working. Once everything stops working, you can exit the container by executing ```exit``` and hitting enter. Now, if you start up another container with ```docker run -it busybox sh```, everything will work again because Docker creates new container everytime!

** Note if you are using Windows, and are using a shell like Git Bash, you will need to prefix the above command with ```winpty```. The full command will look like the following:
```winpty docker run -it busybox sh```  

5. Deleting containers.
Do we really want all these used containers? Time to ~Tidy Up~! First, check which containers you have run, like we did above. If you need help try ```docker ps -a``` to list containers and info or ```docker ps -aq``` for only the container IDs. Grab the ID of any container that you would like to remove and paste them along with the command: ```docker rm``` (i.e., ```docker rm [Insert ID!] [Insert ID!]```). Alternatively, you can use ```docker rm $(docker ps -aq)``` to remove all the containers at once.

## Using Docker to Run Cool and Fun Web Applications

Let's pull an premade image from the Docker Registry that contains a simple bulletin board application (https://docs.docker.com/get-started/part2/). The application is written in Node.js and requires Bootstrap and ejs. Normally we would have to add bootstrap and ejs through yarn. But these dependencies are already included in the image that you will pull. 
Run ```docker run mshakeel1/node-bulletin-board-ex``` (note that this implicitly calls `docker pull mshakeel1/node-bulletin-board-ex` to download the image from the Docker Registry). You should see the following:
```
> vue-event-bulletin@1.0.0 start /usr/src/app
> node server.js

Magic happens on port 8080...
```
The server is running but how do you access the website? What port should we use to see the website? Despite the "magic happening," the client is actually not exposing any ports so we need to re-run the docker run a command to publish ports. We will also detach our container from terminal, so we can close the terminal and keep our container running. Try ```docker run -p 8800:8080 -d --name bulletin mshakeel1/node-bulletin-board-ex```. The ```-d``` option detaches the container (ensures that the container runs in the background and does not take up space in our terminal). The --name option gives the container a name to make it easy to refer to it later. The -p option (short for --publish) asks Docker to forward traffic incoming on the host's port 8800, to the container's port 8080. Containers have their own ports, so if you want to reach one from the host computer, you have to forward traffic to it with the -p option. We can see the port that the website is hosted at by running ```docker port bulletin```. In this case, our container's name is "bulletin" as we specified in the command, but for a general container you would use ```docker port YOUR_CONTAINER_NAME```.

Since we are using Docker Playground, we can navigate to the port by clicking on the hyperLinks associated with the Port at the top of the Plaground. A hyperlink should appear for the "8800" port. If it doesn't, click on "Open Port" at the top of the screen and type: 8800. Click "ok". You should be led to the bulletin board application that looks like this: 
![](https://i.imgur.com/LrJ2fXE.jpg)

Voila! You just ran a web application that depends on Node.js, Bootstrap, ejs, and other dependencies, without having to download them yourself! p


Once you have viewed the application, you can stop the container by running ```docker stop bulletin```.


## Docker Commands Repository
```
$ docker pull [image-name]
// Fetch an image from Docker Registry (official Docker Hub).
// Short for docker pull docker.io/library/image-name

$ docker run [image-name] 
// Run a container based on a Docker image. 
--rm flag removes container once it's exited from. 
-p specifies custom port to which client will forward connections to container. 
-d Detaches terminal.

$ docker ps -a 
// Shows all the containers currently running. Displayed the container ids as well.
-a flag shows history of containers run in the past .

$ docker rm [container-id]
// Containers take up disk space. Can remove containers built in the past with this command.

$ docker rm $(docker ps -a -q -f status=exited) 
// Deletes all containers with status 'exited'.

$ docker images 
// View all images available on local system.

$ docer rmi [image-name] 
// Remove Docker image from local system.

$ docker run -it busybox sh 
// Run multiple commands inside container.
-it flag attaches us to interactive tty.

$ docker stop [container-name] 
// Stop a detached container.
```

## Part 2: creating our own docker images
Let's stay on the Docker playground and do a toy example to get some experience making docker images. Image the following scenario: you're working on two projects, each with their own set of dependencies. Chances are, these projects might even require different versions of some of the same packages.

To simulate our two different projects, let's quickly write two python scripts: `script_python2`, which uses Python 2 syntax, and `script_python3`, which uses Python 3 syntax. First, make the two files with `touch [FILENAME]`. To to edit the files, you can use `vim` or the built-in editor at the top, which you can enter with the "EDITOR" button next to the orange DELETE.

Your `script_python2` might look something like:
```
print(5/2)
print "script_python2 ran successfully"
```
Similarly, here's an example for `script_python3`:
```
print(5/2)
print("script_python3 ran successfully")
```

Obviously, this is a simple example. But imagine that `script_python2` and `script_python3` are actually large projects, each with their own set of dependencies. Keeping the dependencies straight while working on these could easily become a pain.

This is a prime use case for Docker. In order to cleanly separate these two sets of dependencies, we'll make two different docker images.

To create a docker image, we'll need a `Dockerfile`. Think of it as a set of instructions that specifies how to build our image.

Go ahead and make a file named `Dockerfile` with no extension and with the following contents:

```
FROM python:2
 
# copy the scripts we wrote to the container
COPY . .
```

Now, we'll run the following command to create the imag (don't forget the period!).
```
$ docker build -t yourdockerusername/environment_python2 .
```
*Extra Credit* (unfortunately we can't acutally give EC, but just to make sure you're paying attention): Notice docker notes that it is sending the "build context" to the Docker daemon. What does the Docker daemon do and what is its analog in a virtual machine?

Now, let's do the same thing to make a Python 3 environment. So change the first line of your `Dockerfile` to read
```
FROM python:3
```
The second line should be the same as the as for Python 2.


Now build the new image:
```
docker build -t yourdockerusername/environment_python3 .
```
Now, we can run our python scripts using these images as our containers. For example:
```
docker run yourdockerusername/environment_python2 python script_python2
```
In the above line, `docker run` created a container from the `yourdockerusername/environment_python2` image and `python script_python2` ran your script in that container. Your output should look something like this:
```
2
script_python2 ran successfully
```
Similarly, if you run the Python 3 script in your Python 3 environment,
```
docker run yourdockerusername/environment_python3 python script_python3
```
the script should successfully execute with Python 3 syntax:
```
2.5
script_python3 ran successfully
```
What happens if you try to run `script_python2` in the `environment_python3` container? Does that make sense?

There you have it! If you made it this far, you've successfully managed projects with separate dependencies using docker containers.

## Summary
* [ ] Learned some key Docker terminology
* [ ] Pulled an image from Docker hub and ran it on your local machine
* [ ] Created your very own Docker image (in fact two of them)
* [ ] Had the time of your life

## Questions
1. What is the difference between a virtual machine and a container?
2. Give an example of a situation (either from your experience or a hypothetical one) in which it would be helpful to use docker to manage an environment.

## Citations
This tutorial was adopted from https://docker-curriculum.com and https://docs.docker.com/get-started/.

## Resources
Want to learn more about Vagrant? Check out https://www.vagrantup.com/intro/index.html or https://opensource.com/resources/vagrant
   Docker more your speed? See the official introduction https://docker-curriculum.com/ or this well done introductory video https://www.youtube.com/watch?v=pGYAg7TMmp0
