# Docker Build and Deploy Cheatsheet

This document provides a step-by-step guide for dockerizing a simple Node.js application, pushing it to Docker Hub, and running it on another system.

## 1. Prerequisites

1.  **Install Docker Desktop:** [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
2.  **Create a Docker Hub Account:** [hub.docker.com](https://hub.docker.com/)
3.  **Verify Installation:**
    ```bash
    docker --version
    ```
4.  **Log in to Docker Hub via CLI:**
    ```bash
    docker login
    ```

## 2. Application Setup

1.  Create a project directory.
    ```bash
    mkdir docker-demo
    cd docker-demo
    ```

2.  Create a `package.json` file.
    ```json
    {
      "name": "docker-demo-app",
      "version": "1.0.0",
      "description": "A simple Node.js app for a Docker demo.",
      "main": "server.js",
      "scripts": {
        "start": "node server.js"
      },
      "dependencies": {
        "express": "^4.18.2"
      }
    }
    ```

3.  Create a `server.js` file.
    ```javascript
    const express = require('express');
    const PORT = 8080;
    const HOST = '0.0.0.0';
    const app = express();
    app.get('/', (req, res) => {
      res.send('Hello from inside a Docker Container!');
    });
    app.listen(PORT, HOST, () => {
      console.log(`Running on http://${HOST}:${PORT}`);
    });
    ```

## 3. Building the Docker Image

1.  Create a file named `Dockerfile` (no extension).
    ```dockerfile
    FROM node:18-alpine
    WORKDIR /app
    COPY package*.json ./
    RUN npm install
    COPY . .
    EXPOSE 8080
    CMD [ "node", "server.js" ]
    ```

2.  Build the image. **(Replace `your-docker-id` with your Docker Hub username)**.
    ```bash
    docker build -t your-docker-id/my-app:1.0 .
    ```

3.  Verify the image was created.
    ```bash
    docker images
    ```

## 4. Running the Container Locally

1.  Run the container from the image.
    ```bash
    docker run -d -p 3000:8080 your-docker-id/my-app:1.0
    ```
    *   `-d`: Detached mode (runs in background).
    *   `-p 3000:8080`: Maps host port 3000 to container port 8080.

2.  Verify the container is running.
    ```bash
    docker ps
    ```
    *   Access the application at `http://localhost:3000`.

## 5. Pushing the Image to Docker Hub

1.  Push the tagged image to your Docker Hub repository.
    ```bash
    docker push your-docker-id/my-app:1.0
    ```

## 6. Pulling and Running on a Different System

1.  Pull the image from Docker Hub.
    ```bash
    docker pull your-docker-id/my-app:1.0
    ```

2.  Run the container from the pulled image (the command is identical).
    ```bash
    docker run -d -p 3000:8080 your-docker-id/my-app:1.0
    ```

## 7. Cleanup Commands

1.  Stop a running container.
    ```bash
    docker stop <container_id>
    ```

2.  Remove a stopped container.
    ```bash
    docker rm <container_id>
    ```

3.  Remove a Docker image (all dependent containers must be removed first).
    ```bash
    docker rmi your-docker-id/my-app:1.0
    ```
