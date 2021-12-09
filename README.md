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

<html>
     <h1>
        <img style="float: center;" src=/pictures/twocommands.png width="1000" />
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


# Task 3

# Task 3 Procedure

# Task 4

# Task 4 Procedure

# Task 5 

# Task 5 Procedure

# Task 6

# Task 6 Procedure
