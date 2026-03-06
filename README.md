# Kubernetes Test App

A simple Flask web application demonstrating Kubernetes deployment and management.

## Overview

This is a lightweight Flask application designed to run in a Kubernetes cluster (Minikube). The app takes a user's name as input and displays a personalized welcome message.

## Project Structure

```
├── app.py                 # Main Flask application
├── requirements.txt       # Python dependencies
├── Dockerfile            # Docker container configuration
├── deployment.yaml       # Kubernetes Deployment and Service manifests
├── static/
│   └── style.css        # CSS styling for the web UI
├── templates/
│   └── index.html       # HTML template for the web interface
└── README.md            # This file
```

## Prerequisites

- **Docker**: For building container images
- **Minikube**: Local Kubernetes cluster
- **kubectl**: Kubernetes command-line tool
- **Python 3.9+** (for local development)

## Installation & Setup

### 1. Start Minikube

```bash
minikube start
```

### 2. Build the Docker Image

```bash
docker build -t kubernetes-test-app:latest .
```

### 3. Load Image into Minikube

```bash
minikube image load kubernetes-test-app:latest
```

### 4. Deploy to Kubernetes

```bash
kubectl apply -f deployment.yaml
```

### 5. Verify Pods are Running

```bash
kubectl get pods
```

You should see 2 pods in `Running` status:
```
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-test-app-xxxxxxxxxxxx-xxxxx   1/1     Running   0          XX
kubernetes-test-app-xxxxxxxxxxxx-xxxxx   1/1     Running   0          XX
```

## Accessing the Application

### Option 1: Port Forwarding

```bash
kubectl port-forward svc/kubernetes-test-app 8080:8080
```

Then open your browser and go to: **http://localhost:8080**

### Option 2: Minikube Service

```bash
minikube service kubernetes-test-app
```

This will automatically open the app in your default browser.

## Application Details

### Flask Routes

- **GET** `/` - Displays the web form
- **POST** `/` - Processes the form submission and displays a personalized greeting

### Kubernetes Resources

**Deployment** (`kubernetes-test-app`):
- 2 replicas for high availability
- Uses local Docker image with `imagePullPolicy: Never`
- Resource limits: 64Mi memory, 200m CPU
- Exposes port 5000

**Service** (`kubernetes-test-app`):
- Type: ClusterIP (default)
- Port: 8080
- Target Port: 5000

## Common Commands

```bash
# View deployment status
kubectl describe deployment kubernetes-test-app

# View service details
kubectl get svc kubernetes-test-app

# View pod logs
kubectl logs -f <pod-name>

# Delete the deployment
kubectl delete -f deployment.yaml

# Scale the deployment
kubectl scale deployment kubernetes-test-app --replicas=3

# Roll out a new image (after rebuilding)
kubectl rollout restart deployment kubernetes-test-app
```

## Troubleshooting

### Pods show "ErrImageNeverPull"

The Docker image hasn't been loaded into Minikube:

```bash
docker build -t kubernetes-test-app:latest .
minikube image load kubernetes-test-app:latest
kubectl delete pods -l app=kubernetes-test-app
```

### Cannot connect to localhost:8080

Ensure port-forwarding is active:

```bash
kubectl port-forward svc/kubernetes-test-app 8080:8080
```

### Minikube cluster is stopped

Restart it:

```bash
minikube start
```

## Development

### Run Locally (without Kubernetes)

```bash
pip install -r requirements.txt
python app.py
```

Open http://localhost:5000 in your browser.

### Rebuild and Redeploy

After making changes to the app:

```bash
docker build -t kubernetes-test-app:latest .
minikube image load kubernetes-test-app:latest
kubectl rollout restart deployment kubernetes-test-app
```

## License

See LICENSE file for details.
