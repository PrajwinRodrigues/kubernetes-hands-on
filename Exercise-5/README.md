# Exercise 5: Docker Security with AppArmor

## Objective

The objective of this exercise is to secure a Dockerized Flask application using AppArmor, a Linux Security Module that restricts the actions processes can perform.

A custom AppArmor profile is created and applied to the Docker container to restrict access to sensitive system files.

## Technologies Used

- Docker Engine
- Ubuntu on Windows Subsystem for Linux (WSL)
- AppArmor
- Python
- Flask

## Project Structure

```text
Exercise-5-AppArmor/
├── app.py
├── Dockerfile
├── my-apparmor-profile
└── README.md
```

## Implementation

### 1. Flask Application

The `app.py` file implements a simple Flask web application that listens on port 5000 and returns a response when the root endpoint is accessed.

### 2. Docker Image

The Dockerfile uses the `python:3.8-slim` base image, installs Flask, copies the application files, and starts the application inside the container.

Build the Docker image:

```bash
docker build -t flask-apparmor .
```

### 3. AppArmor Profile

The custom profile, `my-apparmor-profile`, defines security rules for the container. It allows basic file, network, and capability operations while denying read access to:

- `/etc/shadow`
- `/etc/gshadow`

Load or reload the profile:

```bash
sudo apparmor_parser -r /etc/apparmor.d/my-apparmor-profile
```

Verify that the profile is loaded:

```bash
sudo aa-status
```

### 4. Run the Secured Container

Start the container with the custom AppArmor profile:

```bash
docker run -d \
  --name flask-secure \
  --security-opt apparmor=my-apparmor-profile \
  -p 5001:5000 \
  flask-apparmor
```

The host port is 5001, which forwards requests to port 5000 inside the container.

### 5. Verify the Application

Access the application:

```bash
curl http://localhost:5001
```

Expected response:

```text
Hello, this is a secure Flask application running inside a Docker container!
```

Verify the configured profile:

```bash
docker inspect -f '{{.AppArmorProfile}}' flask-secure
```

Expected output:

```text
my-apparmor-profile
```

### 6. Security Testing

Attempt to read the protected file from inside the container:

```bash
docker exec flask-secure sh -c 'cat /etc/shadow'
```

**Observed result:**

```text
cat: /etc/shadow: Permission denied
```

This confirms that the configured AppArmor restriction blocked the attempted read of `/etc/shadow`.

## Results

- Successfully built and ran the Flask application inside a Docker container.
- Loaded the custom AppArmor profile in enforce mode.
- Verified that the container was configured with `my-apparmor-profile`.
- Confirmed that reading `/etc/shadow` was denied by the security policy.
- Verified that the Flask application remained accessible.

## Conclusion

This exercise demonstrates how AppArmor can provide an additional layer of security for Docker containers by enforcing process access restrictions. Applying a custom profile helps limit access to sensitive system resources while allowing the application to perform its intended function.

AppArmor is one component of container security and should be combined with other security practices, such as least-privilege execution and appropriate container capabilities.
