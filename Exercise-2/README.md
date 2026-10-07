# Exercise 2 - Minikube, Kubectl and Flask

## Objective

Deploy a Flask application inside a Docker container and run it on a Kubernetes cluster using Minikube.

## Technologies Used

- Python
- Flask
- Docker
- Kubernetes
- Minikube
- kubectl

## Project Structure

```text
Exercise-2/
├── app.py
├── Dockerfile
├── deployment.yaml
├── service.yaml
└── README.md
```

## 1. Flask Application

The Flask application runs on port `15000`.

The application listens on `0.0.0.0` so that it can receive connections from outside the container.

## 2. Docker Image

A Docker image named `flask-app:latest` was created.

The image was built using Minikube's Docker environment so that the Kubernetes cluster could use the local image.

```bash
docker build -t flask-app .
```

## 3. Kubernetes Deployment

The Flask application was deployed using `deployment.yaml`.

```bash
kubectl apply -f deployment.yaml
```

The Deployment creates a Pod running the `flask-app:latest` container.

The container listens on port `15000`.

## 4. Pod Verification

The Pod was verified using:

```bash
kubectl get pods
```

The Pod reached the `Running` state.

Application logs were checked using:

```bash
kubectl logs <pod-name>
```

The logs confirmed that Flask was running on port `15000`.

## 5. Kubernetes Service

A NodePort Service was created using `service.yaml`.

```bash
kubectl apply -f service.yaml
```

The Service exposes the Flask application outside the Pod.

The Service was verified using:

```bash
kubectl get services
```

## 6. Accessing the Application

The application was accessed using:

```bash
minikube service flask-app-service --url
```

Opening the generated URL in a browser displayed:

```text
Hello from Flask running inside Kubernetes!
```

## Architecture

```text
Browser
   |
   v
Minikube NodePort Service
   |
   v
flask-app-service
   |
   v
Flask Pod
   |
   v
Docker Container
   |
   v
Flask Application :15000
```

## Result

The Flask application was successfully containerized using Docker and deployed to Kubernetes using Minikube. The application was exposed using a NodePort Service and successfully accessed through a browser.