## Launching App Using K8

### Step 1: Create Deployment YAML File

Create a YAML file for deployment

Call it `sparta-deploy.yml`

```yaml
# The API version used to create the object
apiVersion: apps/v1

# The type of object we want to create
kind: Deployment

# Metadata about the object like its name
metadata:
  name: node-app-deployment

# Specification details of the object
spec:
  
  # Number of pod replicas
  replicas: 2  # Adjust the number of replicas based on your computer's capacity
  
  # Label selector for pods. Existing ReplicaSets whose pods are selected by this will be scaled down.
  selector:
    matchLabels:
      app: node-app

  # Template for the pod's contents
  template:
    # Metadata to label the pod
    metadata:
      labels:
        app: node-app

    # Details of the pod's contents
    spec:
      containers:
        - name: node-app  # Name of the container
        
          # Image of the container to be used
          image: shaluomehra/sparta-app  # your sparta app image

          # Ports exposed by this container
          ports:
            - containerPort: 3000  # Adjust if your app listens on a different port
```


### Step 2: Create and Run Pods

1. Deploy the application using `kubectl create` command:

   
   ```yaml
   kubectl create -f sparta-deploy.yml
   ```
   

2. Verify that the pods are running:

   
   ```yaml
   kubectl get pods
   ```

### Step 3: Exposing the Application

Create a service YAML file to expose your application:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  selector:
    app: node-app
  type: NodePort
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30100 # Between 30000 - 32767
```
### Step 3b: You can also use a Load Balancer

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

Apply this configuration using `kubectl create` 
 ```yaml
kubectl create -f sparta-service.yml
```

After applying, your Nginx pods should be accessible at http://localhost:30100 (or whichever `nodePort` you've specified).

**Note:** To delete pods

```yaml
kubectl delete pod <filename>
```









