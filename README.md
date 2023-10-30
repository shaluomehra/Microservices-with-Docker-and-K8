## Some useful Docker Commands

To see if Docker is running and the version of Docker
```
docker
docker --version
```

We can run NGINX on our local host
```
docker run -d -p 80:80 nginx
```
Check its working type `localhost` in a web browser

This command displays a list of all currently running Docker containers on the system: `docker ps`

This command allows you to run a command inside a running container
`docker exec -it <ID of nginx Image> sh`

Using winpty ensures that the Docker command has an interactive terminal
`alias docker="winpty docker"`

```
apt update -y
apt upgrade -y
apt install sudo
apt install nano

cd /usr
cd share
cd nginx
cd html

pwd

ls

sudo nano index.html

Edit index.html file with profile
```

# Building an Image and Pushing to Docker Hub

Create a directory `docker-image-shaluo` <br>

Create an `index.html` file locally and paste the text:

```
<h1>Shaluo Mehra</h1>
<p>Shaluo test nginx.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Create a default file called `index.html`

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    root /usr/share/nginx/html;
    index index.html index.htm;

    server_name _;
    location / {
        try_files $uri $uri/ =404;
    }
}
```
Create another file in the directory and call it `Dockerfile` <br>

In your Dockerfile enter the following:
```
FROM ubuntu:18.04  
LABEL maintainer="shaluomehra@outlook.com" 
RUN  apt-get -y update && apt-get -y install nginx
COPY files/default /etc/nginx/sites-available/default
COPY files/index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
```

## Build your image

Use this command to build the image 

`docker build -t tech254-shaluo-nginx .` <br>

To check if image has been created use `docker images`

Now we need to run this image using a different port as Port 80 will already be in use:

`docker run -d -p 81:80 tech254-shaluo-nginx:v1`

Login to Docker

`docker login`

Create a tag

`docker tag tech254-shaluo-nginx:v1 shaluomehra/tech254-nginx:v1`

Push your image to DockerHub

`docker push shaluomehra/tech254-nginx:v1`











