# NGINX-docker-official-image
Using Nginx to set up a web server and reverse proxy

## 1. Pulling Image

I started by running the below Docker command to pull the official Nginx image from DockerHub. This specific code runs a new container in interactive and discharged mode which allows me to make changes and run the container in the background. I also mapped port 8080 on my local machine to port 80 inside the container.

`docker run -it --rm -d -p 8080:80 --name web nginx`


![Screenshot 2025-02-24 100131](https://github.com/user-attachments/assets/6eaab62f-e660-4045-82ac-717f98b15e68)




I can now access the web server via http://localhost:8080  





![Screenshot 2025-02-24 101856](https://github.com/user-attachments/assets/45ea988b-8f5c-4ee9-b564-fda12ff45daa)



---



## 2. Adding Custom HTML file
I created a folder called "site-content" and created a custom HTML page using the below code:
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




![image](https://github.com/user-attachments/assets/0dc9cd32-fe39-4272-bbac-f7fb5ca7cb3b)

![image](https://github.com/user-attachments/assets/de86e4a2-f472-4b84-96e1-18ae89b46126)

---

## 3. Building custom Nginx Image

I created a Dockerfile in the same "site-content" directory and added the following command:
```
FROM nginx:latest  
COPY index.html /usr/share/nginx/html/index.html
```
I then built a webserver using ``` docker build -t webserver . ``` which tells docker to execute the comands in the dockerfile. Below is the output of this command:

![Screenshot 2025-02-24 135346](https://github.com/user-attachments/assets/0aea7f7d-8789-4eeb-af10-6f05d6f28faf)

Here is confirmation that it successfully works:

![Screenshot 2025-02-24 135633](https://github.com/user-attachments/assets/af31f89a-b758-4730-9b34-45c55ff15f01)

---

## 4. Setting up a reverse proxy server

I will now set up a REST API behind a reverse proxy using nginx as the reverse proxy.

I used the below command to pull a frontend application made in React.js and a simple backend API written in Node.js

``` git clone https://github.com/pmckeetx/docker-nginx.git ```
