# Nginx Webserver/Reverse Proxy Deployment With Docker

## 📌 Overview  
This project uses   creates an **Nginx reverse proxy** inside a **Docker container** 

## 1. Pulling Image

I started by running the below Docker command to pull the official Nginx image from DockerHub. This command runs the container as a daomon (-d) maps port 8080 on my local machine to port 80 inside the container.

`docker run -it --rm -d -p 8080:80 --name web nginx`


![Screenshot 2025-02-24 100131](https://github.com/user-attachments/assets/6eaab62f-e660-4045-82ac-717f98b15e68)




I could then access the nginx web server via http://localhost:8080  





![Screenshot 2025-02-24 101856](https://github.com/user-attachments/assets/45ea988b-8f5c-4ee9-b564-fda12ff45daa)



---



## 2. Adding Custom HTML file

To put the web server to use, I created a directory called "site-content", created an index.html file and added the below HTML to it:
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

I then ran the following command to create a bind mount volume:

``` 
docker run -it --rm -d -p 8080:80 --name web -v C:/Users/44749/site-content:/usr/share/nginx/html nginx

```


![image](https://github.com/user-attachments/assets/0dc9cd32-fe39-4272-bbac-f7fb5ca7cb3b)


---

## 3. Building custom Nginx Image

To create a flexible custom Nginx Image, I created a Dockerfile in the same "site-content" directory and added the following command:
```
FROM nginx:latest  
COPY index.html /usr/share/nginx/html/index.html
```
I then built a webserver using ``` docker build -t webserver . ``` which tells docker to execute the commands in the Dockerfile. Below is the output of this command:

![Screenshot 2025-02-24 135346](https://github.com/user-attachments/assets/0aea7f7d-8789-4eeb-af10-6f05d6f28faf)

Here is confirmation that it successfully worked:

![Screenshot 2025-02-24 135633](https://github.com/user-attachments/assets/af31f89a-b758-4730-9b34-45c55ff15f01)

---

## 4. Setting up a reverse proxy server

In this project, I set up a reverse proxy server using Nginx and Docker Compose to route traffic to a Node.js application.

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
2. I then created a docker-compsoe.yml file with the below commands

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

3. I then wrote the below Dockerfile to copy my nginx.conf file into the container and replace the default Nginx configuration:

```
FROM nginx:latest
COPY nginx.conf /etc/nginx/nginx.conf
```
Then I ran ``` docker-compose up --build ``` to  run the project, and below is the successful output:

![Screenshot 2025-02-24 185009](https://github.com/user-attachments/assets/5cb8cf0d-07bb-4e6c-ad96-0b01e2c67af6)
