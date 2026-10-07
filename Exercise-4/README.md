# Exercise 4 - Docker Networking

## Objective

The objective of this exercise is to understand Docker container networking by creating a custom Docker bridge network and running multiple containers that communicate with each other.

The exercise demonstrates:

- Creating a custom Docker bridge network
- Running multiple containers on the same network
- Connecting Flask, MySQL, and Redis containers
- Container-to-container communication
- Docker's internal DNS-based service discovery
- Accessing a Flask application through a published port

## Technologies Used

- Docker
- Docker Bridge Networking
- Python
- Flask
- MySQL
- Redis

## Project Structure

```text
Exercise-4/
├── app.py
├── Dockerfile
├── requirements.txt
└── README.md
```

## 1. Flask REST API

A simple Flask REST API was created with an `/about` endpoint.

The application runs on port `5001`.

Example response:

```json
{
    "name": "Simple REST API",
    "version": "1.0",
    "description": "This is a simple REST API built with Flask."
}
```

## 2. Docker Image

The Flask application was containerized using Docker.

The image was built using:

```bash
docker build -t flask-api .
```

The resulting image is:

```text
flask-api:latest
```

## 3. Create a Docker Network

A custom Docker bridge network was created:

```bash
docker network create app-network
```

The network was verified using:

```bash
docker network ls
```

The network uses the Docker `bridge` driver.

## 4. Run MySQL

A MySQL container was created and connected to the custom network:

```bash
docker run -d \
  --name mysql \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=testdb \
  mysql:8.0
```

MySQL listens on its default port:

```text
3306
```

## 5. Run Redis

A Redis container was created on the same Docker network:

```bash
docker run -d \
  --name redis \
  --network app-network \
  redis:7
```

Redis listens on:

```text
6379
```

## 6. Run Flask

The Flask container was connected to the same network:

```bash
docker run -d \
  --name flask-api \
  --network app-network \
  -p 5001:5001 \
  flask-api
```

The Flask container listens on:

```text
5001
```

The port was published to the host as:

```text
localhost:5001
```

## 7. Docker Network Architecture

The final setup consists of three containers connected to the same custom bridge network:

```text
                         app-network
                              |
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
        ┌──────────┐    ┌──────────┐    ┌──────────┐
        │ flask-api│    │  mysql   │    │  redis   │
        │  :5001   │    │  :3306   │    │  :6379   │
        └──────────┘    └──────────┘    └──────────┘
```

The containers communicate through the Docker bridge network.

## 8. Docker DNS and Container Names

One of the key features demonstrated in this exercise is Docker's internal DNS.

Containers on the same custom network can communicate using their container names instead of hard-coded IP addresses.

For example:

```text
flask-api → mysql:3306
flask-api → redis:6379
```

The Flask container can resolve:

```text
mysql
redis
```

to the corresponding container IP addresses automatically.

This means that applications do not need to know the dynamically assigned IP addresses of other containers.

## 9. Network Verification

The network configuration was inspected using:

```bash
docker network inspect app-network
```

The network contained:

```text
flask-api
mysql
redis
```

Each container received an IP address from the Docker bridge network.

Example:

```text
Network: app-network
Subnet: 172.18.0.0/16

mysql      → 172.18.0.2
redis      → 172.18.0.3
flask-api  → 172.18.0.4
```

## 10. Testing Container Connectivity

Connectivity between the containers was verified using their container names.

### MySQL

```bash
docker exec flask-api python -c "import socket; s=socket.create_connection(('mysql',3306),5); print('Connected to MySQL'); s.close()"
```

Expected result:

```text
Connected to MySQL
```

### Redis

```bash
docker exec flask-api python -c "import socket; s=socket.create_connection(('redis',6379),5); print('Connected to Redis'); s.close()"
```

Expected result:

```text
Connected to Redis
```

These tests demonstrate that the Flask container can communicate with MySQL and Redis through the custom Docker network.

## 11. Testing the Flask API

The Flask API was tested using:

```bash
curl http://localhost:5001/about
```

The endpoint returned the expected JSON response.

## Result

The Docker networking exercise was successfully completed.

The exercise demonstrated how Docker containers can communicate securely and efficiently over a custom bridge network.

The main concepts demonstrated were:

1. Creating a custom Docker network.
2. Connecting multiple containers to the same network.
3. Running Flask, MySQL, and Redis containers.
4. Using Docker's internal DNS for container name resolution.
5. Communicating between containers without manually configuring IP addresses.
6. Publishing a container port to the host system.
7. Verifying container-to-container connectivity.