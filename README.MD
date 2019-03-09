notes
Learn how to get started with a simple container using Docker
There are a few ways to get started with Docker, but the most common way is to use Docker Desktop for Windows or Mac
Once you have Docker Desktop installed, lets make sure you can get to it from the command line

$ docker version


Let’s take a look at the images and containers

$ docker image ls
$ docker container ls -all


Now let’s run a simple container (read the output)

$ docker run hello-world


Now, let’s take a look at the images and containers

$ docker image ls
$ docker container ls -all


In this rest of this blog post I’ll focus on using Docker to deploy Redis as a container and connect to it from code running in another container. 
Download the Latest Redis Docker Image
Assuming that you have Docker installed and running on your laptop or server, you can obtain the Redis image from the Docker Hub container registry. [note: to start Docker, use Launchpad]. There are numerous images available, each representing different versions of Redis or the underlying operating system that it is installed on.
Learn how to get started with Redis using Docker so it can be deployed and interacted with as a container.
Redis is one of the most popular NoSQL databases and is growing in popularity eacy year. It is used with many languages, such as Python, NodeJS, Java, C#, Golang and many, many others. So what if you wanted to deploy Redis 
There are plenty of deployment options when it comes to using Redis. For example, you might just use Redis Cloud which is a multi-tenant Redis-as-a-Service offering by Redis Labs. It runs on AWS, Azure and Google Cloud, and there is a free 30 MB tier, which is enough storage for most demos. After signing up for Redis Cloud, you just point your client library at the IP address and port provided by Redis Cloud and your code should connect easily.  However also install Redis on-premises or on your laptop using multiple options.
In this blog post I’ll focus on using Docker to deploy Redis as a container and connect to it from code running in another container. 
Download the Latest Redis Docker Image
Assuming that you have Docker installed and running on your laptop or server, you can obtain the Redis image from the Docker Hub container registry. [note: to start Docker, use Launchpad]. There are numerous images available, each representing different versions of Redis or the underlying operating system that it is installed on.
The simplest solution to download our image is to execute the following:

$ docker pull redis

The above command will download the Redis image with the default tag ‘latest’. It is the equivalent of ‘docker pull redis:latest’. The latest version is great if you want to be on the bleeding edge, but when you are working with production deployments you should take greater control of the Redis version you use. For example, you might want to specify a specific Redis version like this:

$ docker pull redis:5.0.3

At the time of this post, Redis 5.0.3 is the latest version and it is what the latest tag represents. If you don’t want the latest version, then you can specify redis:5.0.0 or another version.
It may take a moment, but when it is finished, you will have a Redis docker image that is ready for deployment.
Deploying an Instance of Redis as a Container
Once you verify the correct image is available, you can deploy it. The simplest way to deploy it is outlined in the Docker Hub documentation, we can execute the following command:

$ docker run --name redis1 redis:5.0.3

This command works, if there isn’t already another container named redis1. But there are potentially a few problems. You are running a container and naming it redis1. You aren’t running it in the background though. If you want to connect to it, we’d have to do it from another container instance. In other words, we wouldn’t be able to connect to it from our host computer or server.
Instead, hit CTRL-C to exit. Then do the following:

$ docker run -d -p 6379:6379 --name redis1 redis:5.0.3


The docker run command creates a writeable container layer over the specified image, and then starts it using the specified command. 
-d runs the container in detached mode, and prints the container ID

To see if docker is installed: $ docker version 
To see if docker is running: $ docker image ls
To list all images: $ docker image ls // previously docker images
To remove an image: $ docker image rmi my_image
To list all containers: $ docker container ls // only running contaners
To list all containers: $ docker container ls -all // all containers
To stop a container: $ docker stop my_container
To start a container: $ docker start my_container
To remove a container: $ docker container rm my_container
To list all services: $ docker service ls -all
To remove a service: $ docker service rm my_service
Warning: This will destroy all your images and containers. It will not be possible to restore them!
# Delete all containers: $ docker rm $(docker ps -a -q)
# Delete all images: $ docker rmi $(docker images -q)

The above command runs the container in detached mode (in the background). It also maps the container ports with host ports so that you can access Redis from a host level application if you wanted to. Port 6379 is the default port for Redis. You don’t need to map the ports in order to use Redis. The port mapping is only necessary if you wish to use it from your host (Mac, Windows, or Linux) computer. If you plan to deploy all your applications as microservices with Docker, then you’d be fine as long as your containers can communicate with each other.

Interacting with the Redis Docker Container with Basic Shell Operations
At this point you should have a Redis instance running. Rather than creating an application to connect to it, we’re going to interact with it through the Redis command line shell, or command line interface (CLI)
At this point, your container is running in detached mode. This means that you need to connect to it using the interactive terminal:

$ docker exec -it redis1 bash

The above command will connect to our deployment named redis1 using the interactive terminal and start the bash shell. More details on connecting to detached Docker containers can be found in a tutorial titled, Connecting to a Detached Docker Container for Terminal Interaction.
You are now using your Terminal as if you were inside your container. This is where we can start using Redis.
To launch the Redis shell client, execute the following:

$ redis-cli


When inside the Redis CLI, you can access all functionality that is outlined in the Redis documentation. For example, you can see information about the server by typing the INFO command:

INFO

To create a new key, you can use the SET command:

set username:1 myuser1
set username:2 myuser2

To retrieve that new key, you can use the GET command

get username:1	
“myuser1”
get username:2
“myuser2”
get username:3
(nil)


In addition to strings, Redis can store data in 9 other data structures, such as hashes

hmset username:7 fname Dave lname Nielsen hit 1
hmget username:7 fname lname hit
hincrby username:7 hit 2
hget username:7 hit
“3”


There is a lot that you can accomplish with the CLI, but you get the general idea. What we did prove is that we were able to interact with the container instance.

$ redis-cli


Run
-	https://medium.com/the-code-review/top-10-docker-run-command-options-you-cant-live-without-a-reference-d256834e86c1
Build from Dockerfile

Build from Custom Dockerfile
$ cp /usr/local/etc/redis.conf redis.conf
View redis.conf
Conclusion
You just saw how to download and deploy a Docker image or Redis as a container, then interact with it using the Redis CLI. Docker isn’t the only way to deploy Redis and the CLI isn’t the only way to interact with it. These are just two of many examples on what you can accomplish.

