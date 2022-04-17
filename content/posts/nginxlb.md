+++ 
date = "2022-03-10"
title = "Application of Nginx Load Balancer with Docker Compose"
+++

***Hello, everyone! I want to talk about Nginx and Docker. I'll talk about creating a load balancer with Nginx on Docker. I'll write about implementation so If you have any problems with load balancer, nginx, docker etc. you can click the links under the text.***

**This file in my [Github](https://github.com/ruchany13/MyDockerExamples/tree/local/NginxLB)**

## Load Balancer

 A load balancer acts as the “traffic cop” sitting in front of your servers and routing client requests across all servers capable of fulfilling those requests in a manner that maximizes speed and capacity utilization and ensures that no one server is overworked, which could degrade performance. If a single server goes down, the load balancer redirects traffic to the remaining online servers. When a new server is added to the server group, the load balancer automatically starts to send requests to it.
 You can find a lot of detail about load balancer in this [link](https://www.nginx.com/resources/glossary/load-balancing/).

![Load Balancer](LB.png)

## Nginx
 NGINX is open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers.
 You can find a lot of detail about Nginx in this [link](https://www.nginx.com/resources/glossary/nginx/).

## Docker 
 Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.
 You can find a lot of detail about Nginx in this [link](https://docs.docker.com/get-started/overview/).
 
 # Installation
 Now, we'll install some apps for implementation. I'll write installation for MacOS and Ubuntu. I'm using MacOs.

 ## Docker

 - **Macos**: 
 You can download from the [website](https://www.docker.com/products/docker-desktop/) or with [HomeBrew](https://brew.sh/). If you don't have, the *brew installation:*

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
If you install HomeBrew we can continue:
 ```bash
 brew cask install docker
 docker --version
 ```
 That is very easy. You can check Docker Installation with second command.

 - **Ubuntu:**
```bash
sudo apt update
sudo apt upgrade

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Yes, It looks complex but, don't be afraid. Just copy and paste:) If you want to learn more details about installation click the [link](https://docs.docker.com/engine/install/ubuntu/).

## Text Editor
I'm using Visual Studio Code on MacOS but in linux server you can use vim or nano. If you have GUI, you can use Visual Studio.

### Visual Studio
- **Macos**:

```bash
brew install --cask visual-studio-code
```
- **Ubuntu with GUI:**

```bash
sudo apt update
sudo apt upgrade
sudo apt install software-properties-common 
sudo apt install apt-transport-https wget

wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"

sudo apt install code
```
Or, you can download on [Snap Store](https://snapcraft.io/store).
```bash
sudo apt update
sudo apt install snapd
sudo snap install code --classic
```
***Hint:*** Every time in Linux, before installation use update command.

 # Implementation
   Yes, we can start writing a little bit of code, and a config file. It’ll be funny:)
 
If you are ready, we can start. The load balancer separates requests from each server. We will create two servers for load balancing. Every server includes different index.html file so we can check the system. The name of servers app1 and app2. And we will create an Nginx file for load balancer configs. We will run on Docker with a docker-compose.yml file.

 File systems:
```.
--- NginxLB
  +-- docker-compose.yml
  +-- nginx
    --- Dockerfile
    --- nginx.conf
    
  +-- app1
      --- Dockerfile
      --- index.html
      
  +-- app2
      --- Dockerfile
      --- index.html
      
  

 ```
I'm doing on MacOS on terminal:

```bash
mkdir NginxLB
cd NginxLB
touch docker-compose.yml
mkdir nginx
mkdir app1
mkdir app2
```

## Create Nginx Config

We'll create a config file in the nginx directory. This config file includes how Nginx work and which ports.

```
http {
    upstream loadbalancer {
      server 172.17.0.1:7001;
      server 172.17.0.1:7002;
    }
  
    server {
      location / {
        proxy_pass http://loadbalancer;
      }
    }
  }
  
  events { }

```

We are run on the local machine, so we'll write 172.17.0.1 for the server. This IP is local IP. We'll use 7001 and 7002 ports for servers. You can change your ports or server IP. If you change the port and IP address don't forget to change in the docker-compose.yml.

We'll run a website on the main server. Main server Dockerfile:

```Dockerfile
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf

```

Now, we can write our changes in docker-compose.yml:
```yml
version: '3'

services:
  nginxserver:
    build: ./nginx
    ports:
      - "7003:80"
    depends_on:
      - app1
      - app2
```
The services include our docker containers. The first container is main server.
- **nginxserver** is the name of the main server. We run the website on this server.

- **ports** include which ports we use. Nginx exposes 80 ports for requests. We'll use port 7003 to connect 80 ports in nginx.

- **depends_on** meaning this service depends on these services for running. They don't run, this service doesn't run.

Next, we'll create our server app1 and app2.

## App1 and App2

Firstly, we'll create a Dockerfile for app1 in the app1 file. You can copy this file app2.

```Dockerfile
FROM nginx

COPY ./index.html /usr/share/nginx/html/index.html
````


This is the same with app2. But, index.html has a little bit different. 

App1 index.html:

```html
<html>
     <div style="text-align: center;"> 
         <h3>Welcome to My Website</h3>
         <p>app1 server respond </p>
     </div>
 </html>
```

App2 index.html:

```html
<html>
     <div style="text-align: center;"> 
         <h3>Welcome to My Website</h3>
         <p>app2 server respond </p>
     </div>
 </html>
```
Okay, we created an Html page. Which server responds to our request to write in Html. So we can check our systems. Now, we can add our servers in docker-compose.yml.

```yml
  app1:
    build: ./app1
    ports:
      - "7001:80"
  
  app2:
    build: ./app2
    ports:
      - "7002:80"
```
We created services for each server. We define ports in *nginx.conf* file. We use these ports.

Lastly *docker-compose.yml*:
```yml
version: '3'

services:
  nginxserver:
    build: ./nginx
    ports:
      - "7003:80"
    depends_on:
      - app1
      - app2
  app1:
    build: ./app1
    ports:
      - "7001:80"
  
  app2:
    build: ./app2
    ports:
      - "7002:80"
```


## Build and Run
Now, the last step is building compose file and run on our localhost.

You have to same directory with *docker-compose.yml*
We will run the docker-compose file. Docker build services with Dockerfiles we created.

This command creates, builds and runs services. That is very easy. Yes with one command!

```bash
docker-compose up
```
Let's check our *localhost:7003* and see which server responds. That is. Very easy and useful. 

![App1](/NginxLBApp1.png)
![App2](/NginxLBApp2.png)



Last hint: You can use with *-d* parameters for use terminal. It'll work back at the terminal.

```bash
docker-compose up -d
```

Thank you for reading:)







