# 🚀 Nginx with Docker: Static Web server & Reverse Proxy  

This project explores **Nginx** and **Docker** by setting up a **basic web server**, then creating a **custom Nginx image**, and finally configuring it as a **reverse proxy** for a Node.js application.  

## 📌 Features  
✅ **Basic Nginx Server** - Serve static HTML files using Nginx  
✅ **Custom Nginx Image** - Build a Docker image with a modified Nginx setup  
✅ **Reverse Proxy** - Forward requests to a Node.js backend  
✅ **Docker & Docker Compose** - Simplifies deployment  

## 🛠 Tech Stack  
- **Nginx** - Webserver & reverse proxy  
- **Node.js** - Simple backend application  
- **Docker** - Containerisation  
- **Docker Compose** - Multi-container setup  


## 1. Basic Nginx Server

1. To run a basic Nginx server, I simply ran the below Docker command to pull the official Nginx image from DockerHub. This command runs the container as a daemon (-d) and maps port 8080 on my local machine to port 80 inside the container.

`docker run -it --rm -d -p 8080:80 --name web nginx`


![Screenshot 2025-02-24 100131](https://github.com/user-attachments/assets/6eaab62f-e660-4045-82ac-717f98b15e68)




2. I could then access the nginx web server via http://localhost:8080  





![Screenshot 2025-02-24 101856](https://github.com/user-attachments/assets/45ea988b-8f5c-4ee9-b564-fda12ff45daa)



---



## 2. Adding Custom HTML file

1. To put the web server to use, I created a directory called "site-content", created an index.html file in that directory, and then added the below HTML to it:
```
@"
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
"@ | Set-Content "index.html"

```
2. I then ran the following command to create a bind mount volume. This mounts our local directory ``` C:/Users/44749/site-content ``` locally into the running container at: /usr/share/nginx/html

``` 
docker run -it --rm -d -p 8080:80 --name web -v C:/Users/44749/site-content:/usr/share/nginx/html nginx

```


![image](https://github.com/user-attachments/assets/0dc9cd32-fe39-4272-bbac-f7fb5ca7cb3b)

3. To test this out, I searched  http://localhost:8080 on Microsoft Edge and the below HTML rendered successfully:

![Screenshot 2025-02-24 115647](https://github.com/user-attachments/assets/482d03b3-f80f-411f-8fe0-b5aa961ebea5)

---

## 3. Building custom Nginx Image

1. To create a flexible custom Nginx Image, I created a Dockerfile in the same "site-content" directory and added the following command:
```
FROM nginx:latest  
COPY index.html /usr/share/nginx/html/index.html
```
The ``` FROM ``` command pulls the ``` nginx:latest ``` image from Dockerhub into my local machine to build the custom image on.
The ``` COPY ``` command copies our index.html file into the ``` /usr/share/nginx/html ```` directory which overwrites the default index.html file provided in the ``` nginx:latest ``` image.

2. I then built a webserver using ``` docker build -t webserver . ``` which tells docker to execute the commands in the Dockerfile. Below is the output of this command:

![Screenshot 2025-02-24 135346](https://github.com/user-attachments/assets/0aea7f7d-8789-4eeb-af10-6f05d6f28faf)

3. I then ran ``` docker run -it --rm -d -p 8080:80 --name webserver ``` in a container without the need for a mount for the HTML file

4. Here is confirmation that it successfully worked when i navigated to http://localhost:8080:

![Screenshot 2025-02-24 135633](https://github.com/user-attachments/assets/af31f89a-b758-4730-9b34-45c55ff15f01)

---

## 4. Setting up a reverse proxy server

This section demonstrates how to set up a reverse proxy server using Nginx and Docker Compose to route traffic to a Node.js application. 

1. I first created a nginx.conf file and added the below commands which listen on port 80 and proxies any requests to node-app:3000 (Node.js application).
```
events {}
http {
    server {
        listen 80;
        location / {
            proxy_pass http://node-app:3000;
            proxy_set_header HOST $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```
2. I then created a ``` docker-compsoe.yml ``` file with the below commands which allows me to run multiple containers automatically, instead of running them manually:

```
services:
 nginx:
  build: .
  ports:
   - "8080:80"
  depends_on:
   - node-app

 node-app:
  image: node:latest
  command: >
   node -e "require('http').createServer((req, res) => res.end( 'Node says Hello Dami')).listen (3000);"
  ports:
   - "3000:3000"
```
✅ This setup builds a custom Nginx server, runs a node.js backend on port 3000 and routes any traffic through Nginx. 

3. I then wrote the below Dockerfile to copy my nginx.conf file into the container and replace the default Nginx configuration:

```
FROM nginx:latest
COPY nginx.conf /etc/nginx/nginx.conf
```
Then I ran ``` docker-compose up --build ``` to automatically start the containers as defined in ``` doccker-compose.yml ``` , and below is the successful output:

![Screenshot 2025-02-24 185009](https://github.com/user-attachments/assets/5cb8cf0d-07bb-4e6c-ad96-0b01e2c67af6)
