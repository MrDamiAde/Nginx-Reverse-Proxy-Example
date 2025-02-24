# NGINX-docker-official-image
Using Nginx to set up a web server and reverse proxy

## 1. Pulling Image

I started by running the below Docker command to pull the official Nginx image from DockerHub. This specific code runs a new container in interactive and discharged mode which allows me to make changes and run the container in the background. I also mapped port 8080 on my local machine to port 80 inside the container.

`docker run -it --rm -d -p 8080:80 --name web nginx`


![Screenshot 2025-02-24 100131](https://github.com/user-attachments/assets/6eaab62f-e660-4045-82ac-717f98b15e68)




I can now access the web server via http://localhost:8080  





![Screenshot 2025-02-24 101856](https://github.com/user-attachments/assets/45ea988b-8f5c-4ee9-b564-fda12ff45daa)



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


## 3. Building custom Nginx Image

A benefit of containerisation is the ability to move the image around. 
