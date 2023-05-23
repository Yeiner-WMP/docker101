# Table of Contents

## Docker Overview
 [Understanding Docker](#UnderstandingDocker)<br>
[Technical requirements](#TechnicalRequirements)<br>

## Building Container Images
[Dockerfiles](#Dockerfiles)<br>
[Dockerfiles Instructions](#DockerfilesInstructions)<br>
[Building Docker images](#BuildingDockerImages)<br>
[Summary](#BuildingContainersSummary)<br>

## Managing Containers
[Understanding Docker container commands](#UnderstandingDockerContainerCommands)<br>
[Docker networking and volumes](#DockerNetworkingVolumes)<br>
[Summary](#ManagingContainersSummary)<br>

## Time to Practice
[Docker Customise Website Example](#DockerCustomiseWebsiteExample)<br>
[Docker Customise Website Example Dockerfile](#DockerCustomiseWebsiteExampleDockerfile)


<div style="page-break-after: always"></div>

# Docker Overview 

## Understanding Docker <a name="UnderstandingDocker"></a>

Docker is an open platform for developing, shipping, and running applications. It enables you to separate your applications from your infrastructure, this speeds up the delivery process for your software.

Docker provides the ability to package and run an application in an isolated environment (a container).

Using Docker ( for Mac, Linux, or Windows ), a developer can quickly wrap their code in a container as a Dockerfile, by Building Container Images and Docker Compose files.

This will help the Programmers/Developers continue using their chosen integrated development environment (IDE) and maintain their workflows when working with the code. Also providing the experience of how the application will work, since the app runs in the same environment.

### Dedicated hosts vs VMachines vs Containers 

Containers are an **_abstraction_** at the application layer that packages the **code** and the **dependencies** together. The actions you perform to the containers, running in these environments locally on your machine, will be the same commands or operations that you run against them when they are running in your stage, qa, or production environment.

Differences between Docker containers and virtual machine environments.

![Dedicated vs Virtual Machine](https://www.dropbox.com/s/uml1eynrh90apba/lds-dedicated-virtual.png?dl=0&raw=1)

- In the case of the dedicated machine, it has three applications, all sharing the same
orange software stack.
- For the virtual machines allows us to run three applications, running completely different software stacks.
- Finally the next image shows the same three applications running in containers using Docker. Also the image ( diagram ) shows the most significant benefit of `Docker`, and that is, there is no need for a complete operating system every time we need to bring up a new container, which cuts down on the overall size of containers.

![Dedicated vs Virtual Machine](https://www.dropbox.com/s/dm5eagl99joog06/lds-docker.png?dl=0&raw=1)

Benefits of Docker Containers

- Run containers in seconds
- Less resources -> less disk space
- Less Memory usage
- Does not need full OS
- Deployment
- Testing

<br >

## Technical requirements <a name="TechnicalRequirements"></a>

To use Docker we will need a host running one of the three following OS follow the [Docker Install Documentation](https://docs.docker.com/engine/install/) :

- Windows 10 Professional
- Linux (various Linux flavors)
- macOS HighSierra and above



<div style="page-break-after: always"></div>

# Building Container Images <a name=""></a>

## Dockerfiles <a name="Dockerfiles"></a>

Docker can create images automatically by reading instructions from a text document. A `Dockerfile` is a text document that contains all the commands a user could call on the command line to assemble an image. The following is an example of a `Dockerfile`.

```Dockerfile
# Comment area is like this one.

FROM alpine:latest
LABEL description="This Dockerfile installs NGINX."

RUN apk add --update nginx

EXPOSE 80/tcp

# Sample
# ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT [“nginx”]

# Sample
# CMD ["param1","param2"]
CMD ["-g", "daemon off;"]
```

<br>

### Best Practices

- You should try to get into the habit of using a `.dockerignore` file. It will essentially ignore the items you specified in the file during the build process.
- Only have one Dockerfile per folder to help you organize your containers.
- Minimize the number of packages you need to install per image. One of the biggest goals you want to achieve while building your images is to keep them as small and secure as possible.
- Only one application process per container.
- **Keep things simple**. Over-complicating your Dockerfile will potentially cause you issues down the line.

<br>
<div style="page-break-after: always"></div>

## Dockerfiles Instructions <a name="DockerfilesInstructions"></a>


| Instruction | Description |
|:-----: | :----- |
| FROM | Tells Docker which base you would like to use for your image. It is required to state the name of the image and the release tag we wish to use. `alpine:latest`|
| LABEL | Can be used to add extra information to the image. This information can be anything from a version number to a description. |
| RUN | Is where we interact with our image to install software and run scripts, commands, and other tasks. |
| COPY | Does what the `instruction` implies. Copies the file from the files folder to the host where the image is being built.|
| ADD | It automatically uploads, uncompresses, and adds the resulting folders and files to the path we request it to. |
| EXPOSE | The instruction lets `Docker` know that when the image is executed, the port and protocol defined will be exposed at runtime. |
| ENTRYPOINT | Allows to configure a container that will run as an executable. |
| CMD | It's used to provide default arguments for the `ENTRYPOINT` instruction, both the `CMD` and `ENTRYPOINT` instructions should be specified with the JSON array format. |
| USER | The USER instruction lets you specify the username to be used when a command is run. The USER instruction can be used on the RUN instruction, the CMD instruction, or the ENTRYPOINT instruction in the `Dockerfile`.|
| WORKDIR | Sets the working directory |
| ONBUILD | Lets you stash a set of commands to be used when the image is used in the future, as a base image for another container image. For example: ```ONBUILD RUN apk update && apk upgrade```| 

<br>
<div style="page-break-after: always"></div>

## Building Docker images <a name="BuildingDockerImages"></a>

Before building a Docker image let's define what a Docker Image and a Docker continer is.

A Docker Image is:
- A read-only template for creating an environment
- It contains a set of instructions with everything needed to run your App
- Contains the OS, Software, Application code (and all the dependencies).
- Also, a Snapshot


Now, let us build the base upon which we will start building our future images. There are different ways to accomplish this goal.

The first method we will explore is creating a `Dockerfile` like the one shown previously, and then we do execute the `docker image build` command using the `Dockerfile` to get ourselves an NGINX image.

Let's start by creating the `Dockerfile` as follows: ( _Modify it according to the image you want to create._ ):

```dockerfile
FROM alpine:latest

LABEL maintainer=”Yeiner Aguirre <yaguirre@westmonroepartners.com>”
LABEL description=”This example Dockerfile installs NGINX.”

RUN apk add --update nginx && \
    rm -rf /var/cache/apk/* && \
    mkdir -p /tmp/nginx/

COPY files/nginx.conf /etc/nginx/nginx.conf
COPY files/default.conf /etc/nginx/conf.d/default.conf 
ADD files/html.tar.gz /usr/share/nginx/

EXPOSE 80/tcp

ENTRYPOINT [“nginx”]

CMD [“-g”, “daemon off;”]
```

To make the image there are two ways we can go about building our image. The first way would be by specifying the `--file` switch when we use the docker image build command. We will also utilize the `--tag` switch to give the new image a unique name as shown in the following sample.

```bash 
$ docker image build --file <path_to_working_folder> --tag <Repo>:<Tag> .
```


The second ideal way to create the image is by calling the build command only with the tag as the the next sample.

```bash 
$ docker image build --tag local:dockerfile .
```

Remember that the biuld command has other options/flags that can be used please review the list by using the `--help` flag when calling the build command.

```bash
$ docker image build --help

Usage:  docker image build [OPTIONS] PATH | URL | -
Build an image from a Dockerfile

Options:
  -f, --file string     Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm        Always remove intermediate containers
      --label list      Set metadata for an image
      --rm              Remove intermediate containers after a successful build (default true)
  -t, --tag list        Name and optionally a tag in the 'name:tag' 
```
<br >

When you build your image, you should see something similar to the following Terminal output:

```bash
$ docker image build  --tag yaguirre:first_image  --network host .
Sending build context to Docker daemon  216.1kB
Step 1/10 : FROM alpine:latest
latest: Pulling from library/alpine
213ec9aee27d: Already exists 
Digest: sha256:bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
Status: Downloaded newer image for alpine:latest
 ---> 9c6f07244728
Step 2/10 : LABEL maintainer="Yeiner Aguirre <yaguirre@gmail.com>"
 ---> Running in 8a0d0cc96350
Removing intermediate container 8a0d0cc96350
 ---> 7901e50fcf40
...
Step 9/10 : ENTRYPOINT ["nginx"]
 ---> Running in 6b201bd35d42
Removing intermediate container 6b201bd35d42
 ---> 64d9ee6bff65
Step 10/10 : CMD ["-g", "daemon off;"]
 ---> Running in da590ea8ba34
Removing intermediate container da590ea8ba34
 ---> 60fd80a6b30c
Successfully built 60fd80a6b30c
Successfully tagged yaguirre:first_image
```


Once it is built, we are able to run the `docker image ls` command to check whether the image is available, as well as the size of the image, As it is shown in the Terminal output, my image size is 7.2 MB:

```bash
$ docker image ls
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
yaguirre     first_image   60fd80a6b30c   5 minutes ago    7.2MB
alpine       latest        9c6f07244728   3 months ago     5.54MB
```

<br>

### Creating the first Container

With the prior section completed we can now launch a container with our newly built image by running this command.

```bash
$ docker container run -d --name first_image -p 8080:80 local:dockerfile
```

This will launch a container called `first_image` to check the container status we can run the commad `docker ps` in the terminal. The result will look as follows:

```bash
$ docker ps

CONTAINER ID   IMAGE              COMMAND                  CREATED        STATUS          PORTS                  NAMES
bada070f9f53   local:dockerfile   "/docker-entrypoint.…"   16 hours ago   Up 11 seconds   0.0.0.0:8080->80/tcp   first_image
```

We can also access it using the web browser. The result will loook simething similar to the following:

![Running Container Web](https://www.dropbox.com/s/jdig92vk58g9qhq/lds-fist_image_web.png?dl=0&raw=1)

### Containers Basic Management 
To stop the container we can use the `CONTAINER ID` information from the `docker ps` command and pass it to a new command `docker stop` as follows. Once the command is executed then the container will not be accessible.

```bash
$ docker stop bada070f9f53
bada070f9f53
```

In case that you stopped the container but need it back again. The Stop command only stops the container but the container remains in our system. To enable it you need to type the `docker start ` command and include the container name or ID from the prior sample.

```bash
$ docker start first_image
first_image
```

<br>

To list containers the command `docker ps` is the one used, this command will bring only the **running** containers, if you need to have the complete list of containers running and stopped you need to run the same command but add the flag `-a`.

```bash
$ docker ps -a

CONTAINER ID   IMAGE                                                  COMMAND                  CREATED         STATUS                      PORTS                                            NAMES
bada070f9f53   local:dockerfile                                       "/docker-entrypoint.…"   18 hours ago    Exited (0) 7 seconds ago                                                     first_image
3e84e3f6ea4e   8680813ee8d3                                           "./start.sh"             5 months ago    Exited (137) 4 months ago                                                    epic_mclaren
```

To delete containers that not longer used we can use the command `docker rm <container-id or name>`.

```bash
$ docker rm first_image
first_image
```

To delete all stopped containers you need to run the following command. 

```bash
docker rm $(docker ps -aq)
```

In the case that there is a running container you need to need to run the following command. 

```bash
docker rm -f $(docker ps -aq)
```

To format the response of the `docker ps` to be more readable you need to run a command similar to the next sample:

```bash
docker ps --format="ID:      {{.ID}}\nNAME:    {{.Names}}\nIMAGE:   {{.Image}}\nPORTS:   {{.Ports}}\nCOMMAND: {{.Command}}\nCREATED: {{.CreatedAt}}\nSTATUS:  {{.Status}}\n"

ID:      8c334f5b0231
NAME:    first_image
IMAGE:   local:dockerfile
PORTS:   0.0.0.0:8080->80/tcp
COMMAND: "/docker-entrypoint.…"
CREATED: 2022-11-09 10:40:37 -0600 CST
STATUS:  Up 6 seconds
```


<br>
<div style="page-break-after: always"></div>

## Summary <a name="ManagingContainersSummary"></a>

During this section, we learned about Dockerfiles which are very easy to read and understand (from a BIG picture view) due to the way that it was defined.

Once we review a couple of Dockerfiles Instructions, we create a base image using a Dockerfile that teaches us to build our own images.

For more guidelines about [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) the Docker documentation website is a good reference.

<br>

<div style="page-break-after: always"></div>

# Managing Containers <a name=""></a>

In this section, we will revisit the commands from the prior section "Dockerfiles Instructions" and go into more detail, while we will continue to use our local Docker installation.

## Understanding Docker container commands<a name="UnderstandingDockerContainerCommands"></a>

Before use complex Docker commands, let's review some commands that we used previously.

Let's start by launching the most basic container, the `hello-world` container using the command `docker container run`. 

```bash 
$ docker container run hello-world
```

The above command command pulls an image from the Docker Hub, and, as per the Dockerfile, it prints the `Hello from Docker!` text to the Terminal, and then the process exits we should get a response that looks similar to the following:

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:faa03e786c97f07ef34423fccceeec2398ec8a5759259f94d99078f264e9d7af
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```


Additionally the command `docker container run ` can be used using flags to modify the container. 

```bash
$ docker container run -d --name nginx-test -p 8080:80 nginx
```

In the prior sample, three flags were introduced. One of them was `-d`, which is shorthand for `--detach`. In this case, it means that our container will be executed in the background.

Another flag you may have noticed from the command is the `--name` this flag modifies the container that we gave it and use the name used with the flag. This is because you cannot have two containers with the same name.

Finally, the command includes the `-p` flag this is because we cannot assign more than one process to a port on a host machine. This flag allows us to manually assign the host machine port to the one that we like and point to the correct port in the container. So if we attempted to launch our second container with the same port as the first container, we wouldn't receive an error message.


## Docker networking and volumes<a name="DockerNetworkingVolumes"></a>

In this section we are going to look at the basics of Docker networking and volumes using the default drivers.
<br>

### Docker Network

The examples that we have created so far, have been launched on a single flat shared network. This is that the containers created and launched would have been able to communicate with each other without haveing to use any of the host networking. 

To see this we are going to run a example with multiple containers. Before we launch our application, let's download the container images we will be using, and also create the network:

```bash
$ docker image pull redis:alpine

alpine: Pulling from library/redis
ca7dd9ec2225: Pull complete 
83276aa4de36: Pull complete 
731cc432e6da: Pull complete 
862de9590cc6: Pull complete 
Digest: sha256:2700d5097763
Status: Downloaded newer image for redis:alpine
docker.io/library/redis:alpine
 
$ docker image pull russmckendrick/moby-counter
Using default tag: latest
latest: Pulling from russmckendrick/moby-counter
ff3a5c916c92: Pull complete 
0384617ecf25: Pull complete 
fe7f0ead2923: Pull complete 
b1ad44846492: Pull complete 
Digest: sha256:13bc35dbeebb
Status: Downloaded newer image for russmckendrick/moby-counter:latest
docker.io/russmckendrick/moby-counter:latest
 
$ docker network create moby-counter 
8d036ff752ee
```

After running the commands above with similar results we can launch our containers.

```bash
$ docker container run -d --name redis --network moby-counter redis:alpine
```

From the previuos command we can see the use of the new flag  `--network`, this to define the network that our container was launched in. 

Now, following the prior command, let's launch the application
container for `moby-counter` by running the following command:

```bash
$ docker container run -d --name moby-counter --network moby-counter -p 8080:80 russmckendrick/moby-counter
```

If we run the `docker ps` command we will see that the 2 containers that we launched are now running

```bash
ID:      260c99dd1e3c
NAME:    moby-counter
IMAGE:   russmckendrick/moby-counter
PORTS:   0.0.0.0:8080->80/tcp, :::8080->80/tcp
COMMAND: "node index.js"
CREATED: 2022-11-15 11:39:16 -0600 CST
STATUS:  Up 12 minutes

ID:      a2196052696f
NAME:    redis
IMAGE:   redis:alpine
PORTS:   6379/tcp
COMMAND: "docker-entrypoint.s…"
CREATED: 2022-11-15 10:59:28 -0600 CST
STATUS:  Up 22 minutes
```

The next step is to open in a browser the url [http://localhost:8080/](http://localhost:8080/) you will be able to see a white with the text `Click to add Logos`

![Moby Counter Whitepage](https://www.dropbox.com/s/yj12f252ujy2p96/moby_counter_whitepage.png?dl=0&raw=1)

<br>
<div style="page-break-after: always"></div>

To test click anywhere on the page will add Docker logo. And let's do a quick explanation of what is happening. The application running in the moby-counter container is making a connection to the redis container to store the onscreen coordinates of each click.

![Moby Counter Clicks](https://www.dropbox.com/s/ed7p8tp1od0zu1i/moby_counter_clicks.png?dl=0&raw=1)

The application is looking to connect to a host called `redis` on port `6379`. Let's use the exec command to ping the redis container from the moby-counter application. 

```bash
$ docker container exec moby-counter ping -c 3 redis
PING redis (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.069 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.111 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.113 ms

--- redis ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.069/0.097/0.113 ms

$ docker container exec moby-counter cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
172.18.0.3	260c99dd1e3c

$ docker container exec moby-counter cat /etc/resolv.conf
nameserver 127.0.0.11
options edns0 trust-ad ndots:0

$ docker container exec moby-counter nslookup redis 127.0.0.11
Server:    127.0.0.11
Address 1: 127.0.0.11 localhost

Name:      redis
Address 1: 172.18.0.2 redis.moby-counter
```

We can find out more information about the configuration of the networks with the `inspect` command which will return a JSON object.

There is a full description that may use but at the moment, the idea is to understand the basics. Stating with the geneal network information that looks as follows:


```bash
$ docker network inspect moby-counter
[
    {
        "Name": "moby-counter",
        "Id": "8d036ff752eee11625b2920f5fdc59bdd99c7d119eeb7d454029edb5b0b33b6b",
        "Created": "2022-11-15T10:08:20.352416231-06:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
```


Then is the IP Address Management ( IPAM ) system. It shows the
subnet range and gateway IP address.

```bash
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
```


At the end is where we can find the IP address and MAC address of each container


```bash
        "Containers": {
            "260c99dd1e3c95630ce8ab6896bf797dd89301de195b87c77b7e8520fb9e290b": {
                "Name": "moby-counter",
                "EndpointID": "4edcac9f67ef74461825175dcb340ee8ed4280f5d041ffe0c03d60fbe888bfd3",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "a2196052696f7890e7f1e1ae738ffabe59d1dd2b8cd5615b5c10f89daefd9fae": {
                "Name": "redis",
                "EndpointID": "afd4b339be0adb0d421b5f2897b017a79800aead469efc0c36ea5ca682b700e3",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        }
    }
]

```

Before to move to the next section let's cleanup 

```bash
$ docker container stop redis
$ docker container stop moby-counter

$ docker container rm redis
$ docker container rm moby-counter

```

### Docker Volumes

By default all files created inside a container are stored on a writable container layer. This causes that the data doesn’t persist when that container no longer exists, and it can be difficult to get the data out of the container if another process needs it.

-   Allows sharing of data ( Files & Folders ) 
	- Between host and containers 
	- Between containers. 

How does it works. We have a container running on a host ( lets do a sample with nginx ). First we need to create the volume and this volume allows us to share the data between the host and the container and vice versa. 

![docker_volumes_nginx.png](https://www.dropbox.com/s/lhtqcy37uddxr93/docker_volumes_nginx.png?dl=0&raw=1)


To see this in practice let's start by creating a container for `nginx` as follows 

```bash
$ docker run --name website -p 8080:80 -d nginx:latest
```

The result is a page like the following image. 

![NGINX default](https://www.dropbox.com/s/jdig92vk58g9qhq/lds-fist_image_web.png?dl=0&raw=1)


```bash
$ docker ps -q 

ID:      3a5c04ace8df
NAME:    website
IMAGE:   nginx:latest
PORTS:   0.0.0.0:8080->80/tcp
COMMAND: "/docker-entrypoint.…"
CREATED: 2022-11-16 10:48:29 -0600 CST
STATUS:  Up 3 hours
```


The next step is using the container prior created for the `nginx`, we can now make a new file and in the new file let's add an `h1`  tag from `HTML`. Something like the code below may work, and we save the file as `index.html` inside the website folder.

```html
<h1>Hello There! This is using Docker Volumes.</h1>
```

Once the file is created we proceed start a countainer and mount the website folder to our container.  The following command uses the `-v` flag which is used to introduce the volume to the container. 

 ```bash
# Stop and Remove prior containers if any
$ docker stop website
$ docker rm website

# traverse to the website working directory 
$ cd <into the path for your website folder>

# Command to mount the volume into the container.
$ docker run --name website -v $(pwd):/usr/share/nginx/html:ro -p 8080:80 -d nginx:latest
```

The website now is update to the `index.html` with the content. And every change we made we can see it work in the browser.

![New File from Host](https://www.dropbox.com/s/cmo54tlhpx3od75/new_file_from_host.png?dl=0&raw=1)


As an sample we are going to create a file in the cointainer and see it reflected in the working directory mounted. For this we are going to use a new command `exec`. Once log in we we are going to run the following commands. 

```bash
$ docker exec -it website bash
root@6ee50644e154:/# cd /usr/share/nginx/html/

root@6ee50644e154:/usr/share/nginx/html# ls -l
total 4
-rw-rw-r-- 1 1000 1000 62 Nov 16 20:18 index.html

root@6ee50644e154:/usr/share/nginx/html# touch about.html
touch: cannot touch 'about.html': Read-only file system
```


As you could see we were not able to write the file. This is because when we mount the volume we indicate the `:ro` instruction. To fix this we just need to remove this instruction and launch it again. 

```bash
$ docker run --name website -v $(pwd):/usr/share/nginx/html -p 8080:80 -d nginx:latest
```

Now, if we run the same commands as before we got the following results.

```bash
$ docker exec -it website bash

root@e4d90dca45e8:/# cd /usr/share/nginx/html/
root@e4d90dca45e8:/usr/share/nginx/html# touch about.html
root@e4d90dca45e8:/usr/share/nginx/html# ls -l
total 4
-rw-r--r-- 1 root root  0 Nov 16 23:16 about.html
-rw-rw-r-- 1 1000 1000 62 Nov 16 20:18 index.html
```

If you open the folder you can see the new file as part of the list ( as shown in the following image)

![New File from container](https://www.dropbox.com/s/l7ylunkhfk37ycq/new_file_from_container.png?dl=0&raw=1)

### Summary

During this section, we learned about Docker Networking and Volumes at a very basic level we looked at how we can use Docker cmmand-line commans to manage containers and launch applications on their own `network`.

We also took a quick peak about volumes and how we can `mount` folders as part of our containers, also at a ver basic level.

For additional, or more deeply knowledge about his commands the [Docker documentation website is a good reference.](https://docs.docker.com/get-started/overview/). Or you can also check the command as follows:

```bash
$ docker container --help
$ docker network --help
$ docker volume --help
$ docker image --help
```

<br>
<div style="page-break-after: always"></div>

# Time to Practice <a name=""></a>

## Docker Customise Website Example<a name="DockerCustomiseWebsiteExample"></a>


Let's now do a "Real World" website. For that we are going to search for some free HTML templates on the web, Let's search for "Start Bootstrap" or "Free Bootstrap Themes". Once we find one sample that we like we can prceed to download it. 

For this sample we are going to use a bootstrap free project called grayscale that cen be download from this  [GitHub](https://github.com/startbootstrap/startbootstrap-grayscale).

Next step. Once you have the HTML code copy it to a folder ( we are naming it grayscale for this sample ) and change the directoy in the terminal to the newly created folder. And run the following command. 

```bash
$ docker run -d \
  --name grayscale \
  -v $(pwd):/usr/share/nginx/html \
  -p 8080:80 \
  nginx:latest
```

Now if you refresh or goes to the browser to the address [http://localhost:8080/](http://localhost:8080/) you will be able to see your website running. In our case it looks like the following:

![grayscale_screenshoot.png](https://www.dropbox.com/s/0vyuysjdzzwp80x/grayscale_screenshoot.png?dl=0&raw=1)


## Docker Customise Website Example ( Dockerfile )<a name="DockerCustomiseWebsiteExampleDockerfile"></a>

The next exercise is to modify the prior sample and start using a Dockerfile to build a `Custom Docker Image`that will be used to run as many containers as we want from the custom image. 

The first step is create a `Dockerfile` into the folder were we work the prior example. Once you create the file it will look something similar to the folowing image. To modify this file you can open it using your favorite IDE (_**Integrated Development Environment**_).

![Website_Dockerfile.png](https://www.dropbox.com/s/r3qydbqi79flzjw/Website_Dockerfile.png?dl=0&raw=1)

<br>

>_**Remember**_
>
>A Docker Image contains all your application needs:
>- A set of instructions with everything needed to run your App 	
>- The OS, Software, Application code (and all the dependencies).

<br>
What we want to do is to create our custom image a run a container from it. For that what we are going to do is modify the `Dockerfile` we just create in the root of the `website` folder for our application. The content of the file will be something similar to the following lines:

```Dockerfile
FROM nginx:latest
ADD . /usr/share/nginx/html
```

Now, the next step is build the image from the terminal. In the grayscale folder ( or the folder where your project is ) we are going to run the command `docker build`. This works in the exact same way as the command we review previously `docker image build`.

```bash
$ docker build --tag website:grayscale .
Sending build context to Docker daemon  1.378MB
Step 1/2 : FROM nginx:latest
 ---> 88736fe82739
Step 2/2 : ADD . /usr/share/nginx/html
 ---> 3305c6e1a214
Successfully built 3305c6e1a214
Successfully tagged website:grayscale
```

To check if the image was created, we can use the `docker image ls` command. 

```bash
$ docker image ls 
REPOSITORY   TAG         IMAGE ID       CREATED         SIZE
website      grayscale   3305c6e1a214   3 minutes ago   143MB
nginx        latest      88736fe82739   2 days ago      142MB
```

To run the container we need to execute the command ` docker run`

```bash
$ docker run --name website -p 8080:80 -d website:grayscale
147ef5bfd4c76750039cc2e03cd4ce9dd9cae8e55138600b89b471bacf8e9764

$docker ps --format=$FORMAT

ID:      147ef5bfd4c7
NAME:    website
IMAGE:   website:grayscale
PORTS:   0.0.0.0:8080->80/tcp
COMMAND: "/docker-entrypoint.…"
CREATED: 2022-11-17 17:30:19 -0600 CST
STATUS:  Up About a minute

```

Now if you refresh or goes to the browser to the address [http://localhost:8080/](http://localhost:8080/) you will be able to see your website running. The only difference is that in this case the image has all the information that is required to run our application. Instead of using a volume like in the prior sample. 

In our case it looks like the following:

![grayscale_screenshoot.png](https://www.dropbox.com/s/0vyuysjdzzwp80x/grayscale_screenshoot.png?dl=0&raw=1)
