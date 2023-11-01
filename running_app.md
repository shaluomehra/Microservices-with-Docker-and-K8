## Starting the Sparta APP using Docker

Setting up Sparta Test App through Docker container 

1. Create a Directory with the app folder and a docker file
2. Go inside the app folder, where `app.js` exists
3. Create a `Dockerfile` by doing `nano Dockerfile`
3. Inside the Docker file enter:

```
FROM node:latest
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
cmd [ "node", "app.js" ]
```

### Build The Image

`docker build -t sparta-app .`

Check if image has been created using `docker images`

### Build The Container

The image will run on port 3000

`docker run -d -p 3000:3000 sparta-app`

### Check If It Is Working

Go onto your web browser and type in `localhost:3000`

### Create Tag 

`docker tag sparta-app shaluomehra/sparta-app`

### Push To Dockerhub

`docker push -d -p shaluomehra/sparta-app`























