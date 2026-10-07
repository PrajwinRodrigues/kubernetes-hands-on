# Exercise 3 - Minikube Scaling Flask App with ReplicaSets

## Objective

Deploy a Flask application using a Kubernetes ReplicaSet and demonstrate:

- Running multiple replicas of an application
- Scaling replicas from 3 to 5
- Kubernetes self-healing
- Exposing multiple replicas using a Service
- Identifying which Pod handles requests

## Technologies Used

- Python
- Flask
- Gunicorn
- Docker
- Kubernetes
- Minikube
- kubectl

## Project Structure

```text
Exercise-3/
├── app.py
├── Dockerfile
├── flashsale-replicaset.yaml
├── service.yaml
└── README.md
```

## 1. Flask Application

The Flask application provides three endpoints:

- `/` - Welcome message and Pod hostname
- `/buy` - Simulates a purchase and identifies the Pod handling the request
- `/health` - Health check endpoint

The application runs on port `5000`.

## 2. Docker Image

The application was containerized using Docker.

The image was built inside Minikube's Docker environment:

```bash
docker build -t flashsale:1.0 .
```

## 3. ReplicaSet

A Kubernetes ReplicaSet named `flashsale-rs` was created.

The initial configuration used 3 replicas:

```yaml
replicas: 3
```

It was deployed using:

```bash
kubectl apply -f flashsale-replicaset.yaml
```

The Pods were verified using:

```bash
kubectl get pods -l app=flashsale
```

Three Pods were successfully running.

## 4. Scaling

The ReplicaSet was scaled from 3 to 5 replicas using:

```bash
kubectl scale rs flashsale-rs --replicas=5
```

The result was verified using:

```bash
kubectl get rs flashsale-rs
```

Five Pods were successfully running.

## 5. Self-Healing

One of the running Pods was manually deleted:

```bash
kubectl delete pod <pod-name>
```

The ReplicaSet detected that the number of running Pods had fallen below the desired count of 5.

Kubernetes automatically created a replacement Pod.

The final state was verified using:

```bash
kubectl get pods -l app=flashsale
```

Five Pods were again running.

This demonstrates the self-healing behavior of a Kubernetes ReplicaSet.

## 6. Kubernetes Service

A NodePort Service named `flashsale-service` was created to expose the application:

```bash
kubectl apply -f service.yaml
```

The Service was accessed using:

```bash
minikube service flashsale-service --url
```

## 7. Testing

The root endpoint was tested using:

```bash
curl.exe http://<service-url>/
```

The purchase endpoint was tested using:

```bash
curl.exe "http://<service-url>/buy?user=Prajwin"
```

The health endpoint was tested using:

```bash
curl.exe http://<service-url>/health
```

The responses included the hostname of the Pod that handled each request.

## Architecture

```text
                    ┌───────────────────┐
                    │   Browser / User  │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ flashsale-service │
                    │     NodePort      │
                    └─────────┬─────────┘
                              │
                 ┌────────────┼────────────┐
                 │            │            │
                 ▼            ▼            ▼
              Pod 1        Pod 2        Pod 3
                 │            │            │
                 └────────────┼────────────┘
                              │
                         ... 5 Pods
                              │
                              ▼
                    Flask / Gunicorn
```

## Result

The Flask application was successfully deployed using a Kubernetes ReplicaSet.

The exercise demonstrated:

1. Creating 3 application replicas.
2. Scaling the application from 3 to 5 replicas.
3. Automatic replacement of a deleted Pod.
4. Exposing the replicas through a Kubernetes NodePort Service.
5. Identifying the Pod that handled each request.