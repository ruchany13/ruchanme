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

```
mkdir NginxLB
cd NginxLB
touch docker-compose.yml
mkdir nginx
mkdir app1
mkdir app2
```

## Create Nginx Config

We'll create config file. This config file include how Nginx work and which ports.  






