# Deploy Django Notes App on Kubernetes

## Project Overview

In this project, we will prepare a Django Notes application for deployment on Kubernetes.

Before deploying an application on Kubernetes, we need to package the application into a Docker image. Kubernetes uses this image to create and run application containers inside Pods.

In this project, we will build a Docker image, push it to Docker Hub, and make it available for Kubernetes to pull during deployment.

## Project Workflow

**GitHub → Clone Repository → Build Docker Image → Docker Hub Login → Tag Image → Push Image to Docker Hub → Kubernetes Deployment**

## Step 1: Clone the GitHub Repository

First, clone the project repository to your EC2 instance.

```bash
git clone https://github.com/nitikarawat82/deploy_django_appON_Kubernetes.git
```

Move into the project directory:

```bash
cd deploy_django_appON_Kubernetes
```

Verify the project files:

```bash
ls
```

You should see files such as `Dockerfile`, `manage.py`, `requirements.txt`, and `docker-compose.yml`.

## Step 2: Build the Docker Image

Before deploying the application on Kubernetes, we need to build a Docker image containing the application and its required dependencies.

Run the following command from the project directory:

```bash
docker build -t notes-app-k8s .
```

Here:

- `docker build` builds a Docker image.
- `-t notes-app-k8s` assigns a name to the image.
- `.` tells Docker to use the current directory as the build context.

Verify that the image was created:

```bash
docker images
```

You should see `notes-app-k8s` in the list of local images.

## Step 3: Create a Docker Hub Access Token

To push the image to Docker Hub, you need to authenticate with your Docker Hub account.

1. Log in to Docker Hub.
2. Open **Account Settings**.
3. Navigate to **Personal access tokens**.
4. Create a new access token with the permissions needed to push images.
5. Copy and save the token securely.

Do not share your access token or commit it to GitHub.

## Step 4: Log in to Docker Hub from EC2

Run the following command on your EC2 instance:

```bash
docker login -u YOUR_DOCKERHUB_USERNAME
```

Replace `YOUR_DOCKERHUB_USERNAME` with your Docker Hub username.

When prompted for a password, enter your Docker Hub personal access token.

For a non-interactive login, you can use:

```bash
echo "YOUR_DOCKERHUB_TOKEN" | docker login -u YOUR_DOCKERHUB_USERNAME --password-stdin
```

Avoid saving your token directly in shell history or project files. The interactive login method is preferable for beginners.

## Step 5: Tag the Docker Image

Docker Hub images use a repository name that includes your Docker Hub username.

Tag the local image:

```bash
docker tag notes-app-k8s YOUR_DOCKERHUB_USERNAME/notes-app-k8s:latest
```

For example, if your Docker Hub username is `nitikarawat82`:

```bash
docker tag notes-app-k8s nitikarawat82/notes-app-k8s:latest
```

Verify the tagged image:

```bash
docker images
```

The image should now appear with your Docker Hub username and repository name.

## Step 6: Push the Image to Docker Hub

Push the tagged image:

```bash
docker push YOUR_DOCKERHUB_USERNAME/notes-app-k8s:latest
```

For example:

```bash
docker push nitikarawat82/notes-app-k8s:latest
```

Docker uploads the image to your Docker Hub repository.

After the push completes, open Docker Hub and verify that the `notes-app-k8s` repository contains the `latest` image tag.

## Step 7: Why Do We Push the Image to Docker Hub?

The image built in Step 2 exists locally on your EC2 instance. Kubernetes cannot automatically access an image stored only on that machine if its cluster nodes do not have access to it.

By pushing the image to Docker Hub, we make it available from a registry that Kubernetes nodes can access.

For a public Docker Hub repository, Kubernetes can generally pull the image without registry credentials. For a private repository, you must configure the appropriate image-pull credentials.

Use the image reference in your Kubernetes Deployment YAML:

```yaml
containers:
  - name: notes-app
    image: nitikarawat82/notes-app-k8s:latest
    ports:
      - containerPort: 8000
```

Replace the image name and container port with the values appropriate for your application.

**Note:** This is only the container section of a Deployment manifest, not a complete Deployment YAML file. The correct container port depends on how your Django application is configured to run.

## Final Workflow Summary

1. Clone the Django application from GitHub.
2. Build the Docker image named `notes-app-k8s`.
3. Create a Docker Hub personal access token.
4. Log in to Docker Hub from EC2.
5. Tag the local image with your Docker Hub username.
6. Push the image to Docker Hub.
7. Reference the image in your Kubernetes Deployment YAML.

## Conclusion

In this project, we prepared a Django Notes application for Kubernetes by building a Docker image and uploading it to Docker Hub.

This is an important step in the container deployment workflow because Kubernetes needs an accessible container image to create application Pods.

In the next step, we will create Kubernetes Deployment and Service YAML files, deploy the application, and verify that the Notes application is running.
