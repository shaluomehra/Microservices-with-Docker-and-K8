![Alt text](<images/Screenshot 2023-11-02 101209.png>)

# Why use K8 (Kubernetes)?

Kubernetes (often abbreviated as K8s) is a powerful container orchestration platform

## Benefits of K8:
- **Scalability**: Easily scale applications up or down based on demand
- **High Availability**: Ensures that applications are always available and can recover from failures
- **Load Balancing**: Distributes traffic to ensure optimal resource usage and prevent overloads
- **Automated Rollouts & Rollbacks**: Safely deploy new versions and roll back if issues arise
- **Self-Healing**: Automatically replaces failed containers and reschedules containers when nodes die

## When would you not use K8?
- **Simple Applications**: Overhead might not be justified for small, simple projects
- **Limited Resources**: Requires a learning curve and can be resource-intensive for smaller teams
- **Short-Term Projects**: Might be overkill for temporary or experimental projects
- **Local Development**: For local dev environments, simpler tools might be more appropriate
- **Specific Platform Dependencies**: If you're tied to a particular platform that doesn't support Kubernetes or integrates better with other tools

---

## Using Kubernetes

### Step 1: Create Deployment YAML File

Create a YAML file for deployment

Call it `nginx-deploy.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 3  # Number of pod replicas
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: shaluomehra/tech254-nginx:v1 
          ports:
            - containerPort: 80
```


### Step 2: Create and Run Pods

1. Deploy the application using `kubectl create` command:

   
   ```yaml
   kubectl create -f nginx_deploy.yml
   ```
   

2. Verify that the pods are running:

   
   ```yaml
   kubectl get pods
   ```
  ![Alt text](<images/Screenshot 2023-11-02 112922.png>)

### Step 3: Exposing the Application

Create a service YAML file to expose your application:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      nodePort: 30080
  type: NodePort
```

Apply this configuration using `kubectl create` 
 ```yaml
kubectl create -f nginx-service.yml
```

After applying, your Nginx pods should be accessible at http://localhost:30080 (or whichever `nodePort` you've specified).

![Alt text](<images/Screenshot 2023-11-02 121703.png>)

**Note:** To delete pods

```yaml
kubectl delete pod <filename>
```






---