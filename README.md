[![Build Status](https://travis-ci.com/Talismanic/capstone_project_udacity.svg?branch=master)](https://travis-ci.com/Talismanic/capstone_project_udacity)
# capstone_project_udacity
This is the capstone project of udacity nano-degree of Cloud Engineer. In this project, we have done the following things:

##### 1. Built a microservice which exposes REST API using nodejs, AWS RDS & S3
##### 2. Dockerized the application
##### 3. Run the application in a kubernetes cluster
##### 4. Used travis-ci as the CI tool
##### 5. Used a multibranch jenkins pipeline to deploy the application in the EKS


### Dependencies:

1. Travis-CI Account
2. Build Machine (Here I used an EC2)
3. Jenkins Installed with necessary plugins in the build machine (e.g: Blueocean, Docker, AWS SDK, Github, pipelines, kubernetes cli etc)
4. Docker & Docker Compose Installation
5. AWS profile configuration with aws-cli and eksctl
6. Kubectl
7. Hadolint

### Steps to Build & Deploy 

#### Step 01: Dockerizing the Application
The main application is a node app which exposed couple of REST APIs. Source code is in the ./src folder. So to make this application containeried we first written the Dockerfile. Then we ran the below commands to build the docker image and to push it to the docker hub.
```
sudo docker-compose -f docker-compose-build.yaml build
sudo docker-compose -f docker-compose-build.yaml push
```
Now we can check the image locally by running command like
```
sudo docker images
```
It should provide a image like below:
<img width="940" alt="docker-images" src="https://github.com/Talismanic/capstone_project_udacity/blob/master/Images/docker_images.PNG">

Or we can check the docker hub for the image. My own image is [here](https://hub.docker.com/r/talismanic/capstone-restapi) in Dockerhub.
To test it locally you can run:
```
sudo docker run --name capstone_project -d -p 8001:8080 talismanic/capstone-restapi
```
#### Step 02: Operationalizing the Kubernetes Cluster
To spin up the cluster with eksctl we need to run the following command:
```
eksctl create cluster --name MyCluster01 --version 1.16 --region us-east-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 3 --node-ami auto
```
It will take some time to initialize the cluster. Once the cluster is ready we can deploy the application there with the help of kubectl tool. For that we need to go to k8s folder and run the following command:
```
kubectl apply -f .
```
Next we can check the deployment status with the following command:
```
kubectl get all
```
This should give an output like below image:
<img width="940" alt="kubernest get all" src="https://github.com/Talismanic/capstone_project_udacity/blob/master/Images/kubernetes_get_all.PNG">
If we see that pods are in ContainerCreating state we need to wait for sometime and then again the above command to get the resource definition. We will get a Domain Name from AWS for the service. It might take some time to update the DNS. So we should use postman collection to test this domain after couple of minutes.

#### Step 03: Integrating CI Tool
We have connected this repo with CI tool Travis-ci. After connecting this each commit is generating a build in Travis CI and we have also added Travis batch in the readme file so that everyone can see the build status.

#### Step 04: Integrating Jenkins
First we need to set up below credentials in the jenkins credential manager:
1. Dockerhub Credential
2. AWS IAM user's ID and Secret

We also need to connect kubernetes credentials with Jenkins. For this we need to run the following commands in the build machine:
```
sudo cp  .kube/config /var/lib/jenkins/.kube/config
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
```

Then we need to set up a multi stage jenkins pipeline using blue ocean. This pipeline does the follow stages:
1. Jenkins  watches for commits to the repo every minute
2. If any commit is found, jenkins immediately starts the build
3. Lint the Dockerfile with Hadolint.
4. Build the image from the Dockerfile.
5. Push the image to Dockerhub with the help of provided Dockerhub credential.
6. Then checks whether any kubernetes cluster is running or not.
7. If a cluster is running, jenkins deploy the application to the cluster.
8. Then check the resources to check the status of the application.
9. Finally delete the dangled images from the build machine to save storage.

Whole jenkins pipeline will be like below image:
<img width="940" alt="jenkins pipeline" src="https://github.com/Talismanic/capstone_project_udacity/blob/master/Images/jenkins_whole_pipeline.PNG">

Finally this application can be scaled independently and updated without an downtime in the kubernetes cluster. Current endpoint for this service is:
```
af8ad64268de94495ae895e3b83f0465-1616376944.us-east-1.elb.amazonaws.com:8080
```
It will be up till 25th May-2020. So you guys can test this.