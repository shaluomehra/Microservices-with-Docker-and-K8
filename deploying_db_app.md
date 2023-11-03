# Launching App & DB using K8

### Step 1
1. Create another `Dockerfile` 
2. Inside the Docker file enter:
```yaml
# Use an official MongoDB image as a base
FROM mongo:4.4

# Copy a custom mongod.conf file that allows all connections
COPY mongod.conf /etc/mongod.conf

# Expose the default MongoDB port
EXPOSE 27017

# Specify the command to run MongoDB with the custom config file
CMD ["mongod", "--config", "/etc/mongod.conf"]
```

### Step 2: Build mongo-db Image

`docker build -t mongo-db .`

Check if image has been created using `docker images`

### Step 3: Run Image

`docker run -d -p 27017:27017 shaluo-mongo`

### Step 4: Create Tag 

`docker tag mongo-db shaluomehra/shaluo-mongo`

### Step 5: Push To DockerHub

`docker push shaluomehra/shaluo-mongo`

### Step 6: Create Sparta Node Deployment File
1. Create yaml file called 'sparta-deploy.yml'
2. Copy and paste: 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
spec:
  replicas: 2  # Adjust the number of replicas based on your computer's capacity
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
        - name: node-app
          image: shaluomehra/sparta-app  # Replace with your Node.js app image
          ports:
            - containerPort: 3000  # Adjust if your app listens on a different port
          env: 
            - name: DB_HOST
              value: mongodb://10.97.103.51:27017/posts
          lifecycle:
            postStart:
              exec:
                command: ["/bin/sh","-c", node seeds/seed.js]
```

### Step 7: Create Node services File
1. Create yaml file called 'sparta-load.yml'
2. Copy and paste: 
```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-app-service-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: node-app
  ports:
    - port: 3000
      targetPort: 3000
```

### Step 8: Create file for Mongo Deployment 
1. Create yaml file called 'mongo-deploy.yml'
2. Copy and paste:
```yaml
# Use spaces not a tab
 
apiVersion: apps/v1 # which API to use for deployment
kind: Deployment # pod - service what kind of service/object
 
metadata:
  name: mongo-deployment # naming the deployment
spec:
  selector:
    matchLabels:
      app: mongo # look for this label to match with k8 service
    # lets create a replica set of this with instances/pods
  replicas: 3 #3 pods
    # template to use its label for k8 service to launch in the browser
  template:
    metadata:
      labels:
        app: mongo # This label connects to the service or any other K8 components
  # Lets define the container spec
    spec:
      containers:
      - name: mongo
        image: shaluomehra/shaluo-mongo:latest # use th image that you built in your dockerhub
        ports:
        - containerPort: 27017
```

### Step 9: Create file for mongo services
1. Create yaml file called 'mongo-service.yml'
2. Copy and paste:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
  type: ClusterIP
```
### Run Commands

`kubectl create -f sparta_app_deploy.yml`

`kubectl create -f sparta_node.yml`

`kubectl create -f mongo_services.yml `

`kubectl create -f mongo_deploy.yml `

### To UPDATE instead of Create
If the file has already been created but changed, run:

`kubectl apply -f sparta_app_deploy.yml`

`kubectl apply -f sparta_node.yml`

`kubectl apply -f mongo_services.yml `

`kubectl apply -f mongo_deploy.yml `

### Check its working 

1. Open Web Browser
2. Type `localhost` - expect 'Welcome to NginX'
3. Type `localhost:3000` - expect welcome to Sparta App
4. Type `localhost:3000/posts` - expect Recent Posts page in Sparta app