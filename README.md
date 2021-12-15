# Database_ASGMT

# Task 1

# Task 1 Procedure
1. Create a load balancer:
```
k3d cluster create mongo -p "8080:8081@loadbalancer"
``` 
<html>
     <h1>
        <img style="float: center;" src=/pictures/command.png width="1000" />
     </h1>
</html> 

2. Create a Mongo deployment yaml file. Below shows the contents that was in the Mongo deployment yaml file:
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:4.4
          ports:
            - containerPort: 27017
          env:
          - name: MONGO_INITDB_ROOT_USERNAME
            valueFrom:
              secretKeyRef:
                name: mongodb-secret
                key: mongo-root-username
          - name: MONGO_INITDB_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mongodb-secret
                key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/1.png width="1000" />
     </h1>
</html> 


# Task 2

# Task 2 Procedure
1. Type:
```
echo -n mongo-root-username | base 64
```

2. Type
```
echo -n mongo-root-password | base 64
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/echo.png width="1000" />
     </h1>
</html> 


3. Create a secret.yaml file:
```
apiVersion: v1
kind: Secret
metadata:
    name: mongodb-secret
type: Opaque
data:
    mongo-root-username: bW9uZ28tcm9vdC11c2VybmFtZQ==
    mongo-root-password: bW9uZ28tcm9vdC1wYXNzd29yZA==
```



# Task 3

# Task 3 Procedure
1. Deploy the yaml files:
```
kubectl create -f secret.yaml
kubectl create -f mongodb-deployment.yaml
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/twocommands.png width="1000" />
     </h1>
</html> 

# Task 4

# Task 4 Procedure
1. Create a config_map.yaml file:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: mongo-service
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/3.png width="1000" />
     </h1>
</html> 

2. Create a mongo_express.yaml file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongoexp-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
        - name: mongo-express
          image: mongo-express
          ports:
            - containerPort: 8081
          env:
          - name: ME_CONFIG_MONGODB_ADMINUSERNAME
            valueFrom:
              secretKeyRef:
                name: mongodb-secret
                key: mongo-root-username
          - name: ME_CONFIG_MONGODB_ADMINPASSWORD
            valueFrom:
              secretKeyRef:
                name: mongodb-secret
                key: mongo-root-password
          - name: ME_CONFIG_MONGODB_SERVER 
            valueFrom:
              configMapKeyRef:
                name: mongodb-configmap
                key: database_url
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-exp-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/4.png width="1000" />
     </h1>
</html> 

3. Create the config map:
```
kubectl create -f config_map.yaml
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/config_map.png width="1000" />
     </h1>
</html> 

# Task 5 

# Task 5 Procedure
1. Enter the application by typing into a web browser:
```
localhost:8080
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/localhost8080.png width="1000" />
     </h1>
</html> 

2. A new database called 'test' will be made. This is done by pressing the blue button 'Create Database' in the top right corner of the screen:
<html>
     <h1>
        <img style="float: center;" src=/pictures/enter_database.png width="1000" />
     </h1>
</html> 

3. Click the database test. In a database, collections can be created. To create one, click the blue button "Create Collection" in the top right corner. This tutorial a new collection called "recipe" was made. 
<html>
     <h1>
        <img style="float: center;" src=/pictures/createcollection.png width="1000" />
     </h1>
</html> 

4. In a collection, a new document can be added. Click the green button "New Document" to add a document. In this tutorial, the following document recipe was added from https://github.com/kura-labs-org/Database_ASGMT/blob/main/K8s%20and%20MongoDB%20assignment.pdf

<html>
     <h1>
        <img style="float: center;" src=/pictures/add_document.png width="1000" />
     </h1>
</html> 
