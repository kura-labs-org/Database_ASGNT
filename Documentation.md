# Database Assignment

### Objectives
**We are going to deploy a secure database within a cluster that we will be able to interact with through a UI**

### Step 1: Creating the Cluster

Let's create the cluster. `k3d cluster create db-assign -p "8080:8081@loadbalancer"` Next we are going to configure a series of .yml files that are contained in this repo. Let's go over some of the important points:

1. Firstly, the database is being created on a container in the **Mongo.yml** file using the `mongo` img.

![mongo.yml](images/ss_of_mongoyml.png)

We can see that the container's exposed port is 27017. We can also see that we create two environment variables: a root username and password for the db. We reference mongodb-secret for that information. Let's set up our next file, **Secret.yml**, to contain that information.

2. **Secret.yml** will contain encrypted information on our root username and password.

![secret.yml](images/ss_of_secretyml.png)

We see both the referenced `name` and `key` from **Mongo.yml**, as well as the encrypted values for both the username and password. You will need to create your own encrypted values and input them here by using this command: `echo -n <example_username> | base 64`. Do the same for the password and paste both values in their respective places.

3. Next we have our **mongo_express_deploy.yml**. 

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