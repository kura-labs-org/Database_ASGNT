# Database_ASGMT
<h1 align=center>Database assignment</h1>

<h2>Welcome to the database assignment repo.</h2>  

![image](https://user-images.githubusercontent.com/60336145/140837762-5ecfc75f-336f-42ab-b80f-2c83008e831d.png)
[See in diagrams.net](https://drive.google.com/file/d/1-27RjLHwyUy2bCKvpcBuy-iurBjPzVSc/view?usp=sharing)

AIM: Deploy a MongoDB database by using kubernetes and docker on your local machine.
Tools:
 	Kubernetes: allows you to create a cluster that contains and controls docker containers
 	Docker: it will allow you create containers that are specifically configured to run our applications and only contain the necessary resources.
 	K3d: This tool allows you to run commands on kubernetes to create, and manage kubernetes resources .
	Kubectl: This tools allow us to interact with docker by creating, deleting and managing docker resources such as pods, containers, services and deployments. 

: create a cluster by using K3D
Using the command: k3d cluster create name  -p “port: (target-port)@loadbalancer”
In my case I use the ports 8081: 27017
Once the process is done you will see that your cluster is available for use.

 ![image](https://user-images.githubusercontent.com/60336145/140837298-c51abc0a-1795-4b29-8516-9bfcce01809e.png)










You can use the command: kubectl get all to verify the details and information of your cluster
 ![image](https://user-images.githubusercontent.com/60336145/140837321-69100475-2135-46ac-a48d-1497faf41d3e.png)



Step 2: create the YML files necessary for each deployment, pod, and container
YML files contain the information necessary to create our Kubernetes and Docker resources this is an example of a YML file. I gave my files descriptive names for more convenient use.

mongodb-container-create.yml
 ![image](https://user-images.githubusercontent.com/60336145/140837330-a05b2825-b73b-47b4-b003-3c98f8245a52.png)

This Is the first YML contains information that we needed to make our docker pod and container

this is the second file that contains the information needed to create a service that will allow us to forward the port that is serving our application in the container so that I can be accessible from outside of the pod. The ports are 27017 in both cases.

mongodb-lb-create.yml
 
![image](https://user-images.githubusercontent.com/60336145/140837347-1bc99401-b752-422c-bdab-4841587f2161.png)











This is the third YML file it tells the front end part of our database where our actual database is located and this file is needed for security purposes.  

config_map.yml
 ![image](https://user-images.githubusercontent.com/60336145/140837367-d4a9a422-6596-4fa1-b59f-4fbfd1e51da2.png)


the 4th YML file creates the application container that allows us to interact with the database with an image container called Mongo-Express. I used the following script in YML file.

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














mongodb-express-app.yml
 ![image](https://user-images.githubusercontent.com/60336145/140837387-8866e385-ce9c-4d00-bfdb-47642e49cd10.png)

The last part is to create a secret.yml file that will hold an encrypted version of our username and password in order to access the database. the frontend part of our database will use this secret file in order to access the database every time a change is made.












secret.yml
![image](https://user-images.githubusercontent.com/60336145/140837402-7914cba1-d462-4774-88db-350c996bfbf4.png)
 
We can make our own base 64 encoded string by using the command:
 echo -n example_username | base64

now that we have created the necessary files we can create the necessary resources by running the following command with each of our files

!!! An error that I run into was the order in which I created their resources This is why it is very important to around the files in the correct order. !!!


kubectl apply -f name_of_file.yml is the following order

1.	kubectl apply -f  mongodb-container-create.yml
2.	kubectl apply -f  mongodb-lb-create.yml
3.	kubectl apply -f  secret.yml
4.	kubectl apply -f  config.yml
5.	kubectl apply -f  mongodb-express-create.yml





You can verify that the resources have been made and are running by running the command kubectl get pod or kubectl get all


 
![image](https://user-images.githubusercontent.com/60336145/140837428-619f9f2d-31be-4d18-8902-ee46363f6fba.png)














Once all the resources are running you can visit your clusters port in your browser via the localhost and we can see that our database is ready for use.

You can create a database by adding a name in the database name text field and clicking on the create database button.

 



![image](https://user-images.githubusercontent.com/60336145/140837442-1c9d5eb4-7cdb-4750-9b3c-e863226cfe45.png)











After your database hasn't been created you can create a collection by adding a name the collection name text field and clicking on the create collection button.  you can view the collection by clicking on the view button.

 






![image](https://user-images.githubusercontent.com/60336145/140837448-f5b192a7-d001-4f5b-adc0-79cdf84c941d.png)













A new entry can be created by clicking on the new documents button and bring the desired data in each text field.

 

 ![image](https://user-images.githubusercontent.com/60336145/140837467-7a826fea-fbad-4d3f-aabf-718721141a0d.png)
![image](https://user-images.githubusercontent.com/60336145/140837483-5925b955-7fe2-46c1-a931-51a95f0bdd4a.png)


