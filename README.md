# Deploy Django Notes App on Kubernetes

## Project Overview

In this project, we will prepare a Django Notes application for deployment on Kubernetes.

Before deploying an application on Kubernetes, we need to package the application into a Docker image. Kubernetes uses this image to create and run application containers inside Pods.

In this project, we will build a Docker image, push it to Docker Hub, and make it available for Kubernetes to pull during deployment.

## Project Workflow

**Create EC2 → SSH Login → Install Required Tools → Clone Repository → Build Docker Image → Docker Hub Login → Tag Image → Push Image to Docker Hub → Kubernetes Deployment**

## Step 1: Create an EC2 Instance

First, create an **Ubuntu EC2 instance** in AWS.

Once the instance is running, copy its public IP address and download the key pair used to connect to it.

## Step 2: Connect to EC2 Using SSH

Open your terminal and connect to the EC2 instance:

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

## Step 3: Install Required Tools

Install the tools required for the project:

- Docker
- kubectl
- kind

Docker is used to build and run containers, kubectl manages Kubernetes resources, and kind creates a Kubernetes cluster using Docker.

Verify the installation:

```bash
docker --version
kubectl version --client
kind --version
```

## Step 4: Create a Kubernetes Cluster

Create a Kubernetes cluster using kind:

```bash
kind create cluster --name django-cluster
```

Verify that the cluster is running:

```bash
kubectl get nodes
```

## Step 5: Clone the GitHub Repository

Clone the Django Notes application repository:

```bash
git clone https://github.com/nitikarawat82/deploy_django_appON_Kubernetes.git
```

Move into the project directory:

```bash
cd deploy_django_appON_Kubernetes
```

Check the project files:

```bash
ls
```

## Step 6: Build the Docker Image

Build the Docker image from the project directory:

```bash
docker build -t notes-app-k8s .
```

Verify the image:

```bash
docker images
```

## Step 7: Log in to Docker Hub

Create a Personal Access Token from your Docker Hub account settings.

Then log in:

```bash
docker login -u YOUR_DOCKERHUB_USERNAME
```

Enter your Docker Hub access token when prompted.

## Step 8: Tag the Docker Image

Tag the image with your Docker Hub username:

```bash
docker tag notes-app-k8s nitikadevops/notes-app-k8s:latesttag
```
<img width="1730" height="710" alt="image" src="https://github.com/user-attachments/assets/3a22aa6a-b286-41e0-b51b-e7ae33ba867c" />


Replace `nitikadevops` if you use a different Docker Hub username and replace `notes-app-k8s` with your docker hub repo name.

## Step 9: Push the Image to Docker Hub

Push the image:

```bash
docker push nitikadevops/notes-app-k8s:latest
```

Verify that the image is available in your Docker Hub repository.

## Step 10: Deploy the Application on Kubernetes

Create the Kubernetes Deployment and Service YAML files using the Docker image pushed to Docker Hub.

Apply the configuration:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Verify the deployment:

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```
<img width="1032" height="95" alt="image" src="https://github.com/user-attachments/assets/a92af09b-72cd-4164-8fb7-a91117a6fe5a" />

Step 11. Expose:
```bash
 kubectl port-forward service/notes-app-service -n notes-app 8000:8000 --address 0.0.0.0
```

Step 12. securit y goruop, add port 8000 

<img width="1742" height="212" alt="image" src="https://github.com/user-attachments/assets/0d9592f7-4ded-4020-8253-54e10f929c6a" />


## Conclusion

In this project, we prepare an AWS EC2 instance, install the required tools, create a Kubernetes cluster, clone the Django application, build a Docker image, and push it to Docker Hub.

Finally, we use the image to deploy the Django Notes application on Kubernetes.
