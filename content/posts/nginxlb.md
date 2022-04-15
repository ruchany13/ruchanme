+++ 
date = "2022-03-10"
title = "Application of Nginx Load Balancer with Docker Compose"
+++

***Hello everone! I want to talking about Nginx and Docker. I'll talking about creating load balancer with Nginx on Docker. I want to write about implementation so If you have any problem about load balancer, nginx, docker etc. you can click links under the text.***

## Load Balancer

 A load balancer acts as the “traffic cop” sitting in front of your servers and routing client requests across all servers capable of fulfilling those requests in a manner that maximizes speed and capacity utilization and ensures that no one server is overworked, which could degrade performance. If a single server goes down, the load balancer redirects traffic to the remaining online servers. When a new server is added to the server group, the load balancer automatically starts to send requests to it.
 You can find a lot of detail about load balancer in this [link](https://www.nginx.com/resources/glossary/load-balancing/).

## Nginx
 NGINX is open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers.
 You can find a lot of detail about Nginx in this [link](https://www.nginx.com/resources/glossary/nginx/).

## Docker 
 Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.
 You can find a lot of detail about Nginx in this [link](https://docs.docker.com/get-started/overview/).
 
 # Implementation
  Yes, we can start  write a little bit code, config file. It'll be funny:)
 **Firstly you need install this**
 - [Docker](https://docs.docker.com/get-docker/)
 - Text editor, Prefer [Visual Studio Code](https://code.visualstudio.com/download)
 
 If you are ready, we can start. Load balancer seperate request each server. We will create two server for load balancing. Every server include different index.html file so we can check the system. The name of servers **app1** and **app2** . And we will create nginx file for load balancer configs. We will run on Docker with **docker-compose.yml** file. 

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

We'll create config file in nginx file. This config file include how Nginx work and which ports.

```
http {
    upstream loadbalancer {
      server localhost:7001;
      server localhost:7002;
    }
  
    server {
      listen 80;
      location / {
        proxy_pass http://loadbalancer;
      }
    }
  }
  
  events { }

```

We are run on the local machine, so you we'll write localhost for server. We'll use 7001 and 7002 ports for servers. You can change your ports or server IP. If you change port and IP adress you don't forgot change in the docker-compose.yml .

We'll run website on main server. Main server Dockerfile:

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
The services are include our docker containers. First containers is main server.
- **nginxserver** is name of main server. We run website on this server.
- **ports** are include which ports we use. Nginx expose 80 port for request. We'll use 7003 for connect 80 port in nginx.
- **depends_on** meaning this service depend on these services for running. They don't run, this service doesn't run.

Next, we'll create our server app1 and app2.

## App1 and App2

Firstly, we'll create a Dockerfile for app1 in app1 file. You can copy this file app2.

```Dockerfile
FROM nginx

COPY ./index.html /usr/share/nginx/html/index.html
````


This is same with app2. But, index.html have a little bit different. 

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

Okay, we created html page. Which server is respond our request write in html. So we can check our systems. Now, we can add our servers in docker-compose.yml.

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
We created servieces for each servers. We define ports in *nginx.conf* file. We use these ports.

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
Now, last step is build compose file and run on our localhost.

You have to same directory with *docker-compose.yml*
We will run docker compose file. Docker build services with Dockerfiles we created.

This command create, build and run services. That is very easy. Yes with one command!

```bash
docker-compose up
```
Let's check our *localhost:7003* and see which server respond.








