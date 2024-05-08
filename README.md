# Build Docker Images with GitHub Actions
## Automatically build Docker images with GitHub Actions
![dockergithub](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/f4cc3521-f027-48b7-aa37-fd13f9321680)


## Overview
This project demonstrates how to automatically build Docker images using GitHub Actions. By integrating Docker and GitHub Actions, DevOps engineers can create a seamless **CI/CD (Continuous Integration/Continuous Deployment)** pipeline for projects, ensuring that Docker images are built and pushed to a registry whenever changes are made by developers to the codebase.

## Step-by-Step Process

### 1. Create a GitHub Repository
Begin by creating a new GitHub repository for your project. This will be the central location for your code, configurations, and the GitHub Actions workflow.<p>
1. Open a new terminal in VScode.
2. Create a directory with a repo name you desire (named mine `Deploy-Webapp-with-Kubernetes`).
3. Initialise directory by running:
```
git init
```
4. Use **HuB** to change the local directory to a remote repo. Run:
```
hub create
```
5. Optional: Add README.md (but recommended).
```
echo "## Automatically build Docker images with GitHub Actions" >> README.md
```
6. Push the new repo to GitHub.
```
git add README.md
git commit -m "first commit"
git branch -M main
git push -u origin main
```
if you do not have Hub installed, manually create a github repo and run this command in the terminal:
```
git remote add origin https://github.com/<github-username>/<repo-name>.git
```
For step by step process of creating a github repo from thr terminal, check this [project](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes)

### 2. Create the Dockerfile and Package File
In the repository:
- Create a file called Dockerfile.
```
nano Dockerfile
```
- Add these lines of codes:
```
FROM node:14

WORKDIR /usr/src/app

COPY package.json .
RUN npm install 
COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```
- Perform the two steps above to create and add the lines below to the package.json
```
{
    "name": "docker_nodejs_demo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "dependencies": {
      "config": "^3.3.6",
      "express": "^4.17.1"
    }
  }
```
### 3. Set up the Docker Build Workflow
In the GitHub repository, create a new folder named `.github/workflows`. This is where the GitHub Actions workflow is defined.

Inside the `workflows` folder, create a new file named `dockerhub-push.yml`. This file will contain the configuration for the Docker build workflow.

```
name: Build and Push Docker image to Docker Hub

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  push_to_registry:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v4
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
      
    - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
    
    - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: DockerFileFolder/
          push: true
          tags: rekhugopal/testrepo:latest
```

This workflow will be triggered on push and pull request events to the `main` branch. It performs the following steps:

1. Checks out the repository code.
2. Sets up the Docker Buildx environment for multi-architecture builds.
3. Logs in to the GitHub Container Registry (ghcr.io) using the `GITHUB_TOKEN` secret.
4. Builds the Docker image and pushes it to the GitHub Container Registry, tagging it with the current commit SHA.

### 3. Configure Docker Build Options
In the root of your project, create a `Dockerfile` that defines the build instructions for your Docker image. This file should include the necessary steps to build and package your application.

Example `Dockerfile`:

```Dockerfile
FROM node:14-alpine
WORKDIR /app
COPY . .
RUN npm ci
CMD ["npm", "start"]
```

### 4. Enable GitHub Packages
To store your Docker images, you'll need to enable GitHub Packages for your repository. Go to your repository settings, navigate to the "Packages" section, and enable GitHub Packages.

### 5. Run the GitHub Actions Workflow
Commit and push your changes to the `main` branch of your GitHub repository. This will trigger the `docker-build.yml` workflow, which will automatically build and push your Docker image to the GitHub Container Registry.

You can monitor the progress and check the status of the workflow run in the "Actions" tab of your GitHub repository.

## Conclusion
By leveraging GitHub Actions, you can seamlessly integrate Docker into your development pipeline. This approach allows you to automatically build and push Docker images to a registry, ensuring that your deployments are consistent and up-to-date with the latest changes to your codebase. This setup provides a reliable and efficient way to manage your Docker-based applications.
