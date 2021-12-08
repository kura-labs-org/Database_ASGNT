# Database Assignment

### Objectives
**We are going to deploy a secure database within a cluster that we will be able to interact with through a UI**

### Step 1: Creating the Cluster

Let's create the cluster. `k3d cluster create db-assign -p "8080:8081@loadbalancer"` Next we are going to configure a series of .yml files that are contained in this repo. Let's go over some of the important points:

1. Firstly, the database is being created in the Mongo.yml file.
![mongo.yml](images/Screen Shot 2021-12-08 at 1.16.04 PM.png)

