# Database_ASGMT
For this demonstration, a MongoDB Database will be configured on a local computer. A database is logical storage container that holds a lot of data such as document files. The benefit of using a database is it stores a lot of data in an orgainzed manner where the files that are stored on the database can be easily accessible. <br>
<br>
MongoDB is one of the most commonly used document database softwares. Some databases are configured using the SQL programming language but MongoDB is configured using NoSQL. MongoDB is good for querying and indexing documents. The technology that will be used to make a local MongoDB database is Kubernetes. To have Kubernetes on a computer, Docker must also be downloaded.  

# Task 1
This task a cluster will be created with a specified load balancer. This cluster will hold the MongoDB database and after the cluster's port has been configured in a web browser, the database can be accessed. <br>
<br>
Kubernetes reads yaml files. Within the yaml files, a deployment and service can be specified. When Kubernetes reads any yaml file and makes objects from the given yaml files, Kubernetes will always make: <br>
* Cluster: Is a grouping of many **nodes** <br>
* Node: Holds **pods** <br>
* Pod: Hosts an application <br>
<br>

## Task 1 Procedure
1. First a cluster of nodes will be initated by using the k3d command to make a cluster of nodes that is configured to the 8080 port:
```
k3d cluster create mongo -p "8080:8081@loadbalancer"
``` 
<html>
     <h1>
        <img style="float: center;" src=/pictures/command.png width="1000" />
     </h1>
</html> 

2. A Mongo deployment yaml file will be created with the following content in the yaml file:
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
To use the terminal to make this yaml file, type:
```
nano filename.yaml
```
* filename can be the name of the yaml file that it is given
* The following code shown above is in a yaml file
<html>
     <h1>
        <img style="float: center;" src=/pictures/1.png width="1000" />
     </h1>
</html> 


# Task 2
A database is good to hold data and its always best to make any configured database secured and well protected from any hackers or secuirty threats. During this task, another yaml file will be created to hold our username and password to access the database. If there is no yaml file that holds the set username and password, then the database cannot be accessed. Though this database is configured locally and it seems configuring a yaml file to hold the password and username is not neccessary, this precaution should be considered when creating a database on the web or cloud.  
## Task 2 Procedure
1. With the "echo -n .... | base 64" where '....' can be any words, this command encrypts any given words to a phrase of letters and numbers that is used to access the database. <br>
<br>
Shown below, the words "mongo-root-username" and "mongo-root-password" are being encrypted:
```
echo -n mongo-root-username | base 64
```

```
echo -n mongo-root-password | base 64
```
<html>
     <h1>
        <img style="float: center;" src=/pictures/echo.png width="1000" />
     </h1>
</html> 


2. Now the secret.yaml file that holds the encrypted username and password can now be created:
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
To use the terminal to make this yaml file, type:
```
nano filename.yaml
```
* filename can be the name of the yaml file that it is given
* The following code shown above is in a yaml file


# Task 3
The previous tasks, the yaml files were created to deploy the mongo database along with its username and password being encrypted to increase security. To deploy these yaml files, the kubectl commands are needed in order to store these yaml files on pods and for it communciate with the cluster that was originally created. 
## Task 3 Procedure
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

## Task 4 Procedure
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

## Task 5 Procedure
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
        <img style="float: center;" src=/pictures/create_collection.png width="1000" />
     </h1>
</html> 

4. In a collection, a new document can be added. Click the green button "New Document" to add a document. In this tutorial, the following document recipe was added from https://github.com/kura-labs-org/Database_ASGMT/blob/main/K8s%20and%20MongoDB%20assignment.pdf

<html>
     <h1>
        <img style="float: center;" src=/pictures/add_document.png width="1000" />
     </h1>
</html> 
