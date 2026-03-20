# ☸️ K8-prjct: Kubernetes Test Application

[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Flask](https://img.shields.io/badge/flask-%23000.svg?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)](https://www.python.org/)

A professional, containerized Python Flask web application designed to demonstrate the fundamentals of Kubernetes orchestration. This project provides a hands-on implementation of deploying a highly available web service using Docker and Kubernetes, complete with resource quotas, replica management, and internal load balancing.

---

## 🏗️ Architecture & Traffic Flow

The application follows a robust Kubernetes deployment architecture. External requests are routed through a Kubernetes Service which acts as an internal load balancer, distributing traffic across multiple identical Pod replicas to ensure high availability and fault tolerance.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'fontFamily': 'monospace', 'primaryColor': '#1f2937', 'primaryTextColor': '#f3f4f6', 'primaryBorderColor': '#374151', 'lineColor': '#9ca3af', 'secondaryColor': '#111827', 'tertiaryColor': '#374151' }}}%%
graph TD
    classDef user fill:#374151,stroke:#9ca3af,stroke-width:2px,color:#f3f4f6
    classDef k8s fill:#1e3a8a,stroke:#60a5fa,stroke-width:2px,color:#ffffff
    classDef container fill:#0f172a,stroke:#3b82f6,stroke-width:1px,color:#e2e8f0

    Client([🖥️ Client / Web Browser])

    subgraph Cluster [☸️ Kubernetes Cluster]
        Service[📶 Service<br/>Name: kubernetes-test-app<br/>Type: ClusterIP<br/>Port: 8080]

        subgraph Deployment [📦 Deployment: kubernetes-test-app]
            subgraph Pod1 [🟢 Pod Replica 1]
                App1[🐳 Container<br/>Image: python:3.9-slim<br/>App: Flask Web Server<br/>Port: 5000]
            end
            
            subgraph Pod2 [🟢 Pod Replica 2]
                App2[🐳 Container<br/>Image: python:3.9-slim<br/>App: Flask Web Server<br/>Port: 5000]
            end
        end
    end

    Client -- "HTTP Request
(kubectl port-forward 8080)" --> Service
    Service == "TCP Load Balancing
(Target Port: 5000)" ==> App1
    Service == "TCP Load Balancing
(Target Port: 5000)" ==> App2

    class Client user
    class Service k8s
    class App1,App2 container

    style Cluster fill:#111827,stroke:#4b5563,stroke-width:2px,color:#d1d5db,stroke-dasharray: 5 5
    style Deployment fill:#1f2937,stroke:#374151,stroke-width:2px,color:#9ca3af
    style Pod1 fill:#111827,stroke:#10b981,stroke-width:2px,color:#d1d5db
    style Pod2 fill:#111827,stroke:#10b981,stroke-width:2px,color:#d1d5db
```

### 🔄 Detailed Request Lifecycle:
1. **Ingress / Port-Forward:** The user accesses the application via a local port-forward mapped to the Kubernetes Service on port `8080`.
2. **Service Routing:** The `kubernetes-test-app` Service intercepts the traffic. Acting as a stable endpoint, it abstracts the underlying ephemeral Pods.
3. **Load Balancing:** The Service distributes the incoming HTTP request to one of the two active Pod replicas on target port `5000`.
4. **Application Processing:** The lightweight Flask container processes the request, renders the HTML template, and returns a personalized response.

---

## ✨ Core Features & Best Practices

- **High Availability:** Configured with `replicas: 2` to ensure the application remains accessible even if one pod fails.
- **Resource Optimization:** Explicit `requests` (32Mi RAM, 100m CPU) and `limits` (64Mi RAM, 200m CPU) prevent noisy-neighbor issues and ensure cluster stability.
- **Lightweight Containerization:** Utilizes `python:3.9-slim` to minimize the attack surface and reduce image pull times.
- **Stateless Architecture:** The Flask application is designed to be completely stateless, allowing seamless horizontal scaling.

---

## 📂 Project Structure

```text
K8-prjct/
├── app.py                # Main Flask application logic & routing
├── deployment.yaml       # K8s manifests (Deployment & Service definitions)
├── dockerfile            # Multi-layer Docker build instructions
├── requirements.txt      # Pinned Python dependencies (Flask==2.3.2)
├── static/
│   └── style.css         # UI styling assets
└── templates/
    └── index.html        # Jinja2 HTML template for the frontend
```

---

## 🚀 Deployment Guide

### Prerequisites
Ensure your local development environment is equipped with:
- [Docker Engine](https://docs.docker.com/get-docker/)
- A local Kubernetes cluster (e.g., [Minikube](https://minikube.sigs.k8s.io/docs/start/), [Docker Desktop](https://docs.docker.com/desktop/kubernetes/), or [kind](https://kind.sigs.k8s.io/))
- [kubectl](https://kubernetes.io/docs/tasks/tools/) CLI tool

### 1. Clone the Repository
```bash
git clone https://github.com/Rupeshbhardwaj002/K8-prjct.git
cd K8-prjct
```

### 2. Build the Docker Image
Build the container image directly into your local Docker registry.
```bash
docker build -t kubernetes-test-app:latest .
```
> **Note for Minikube Users:** You must point your terminal to Minikube's internal Docker daemon before building so Kubernetes can find the image:
> ```bash
> eval $(minikube docker-env)
> docker build -t kubernetes-test-app:latest .
> ```

### 3. Apply Kubernetes Manifests
Deploy the Application and Service to your cluster:
```bash
kubectl apply -f deployment.yaml
```

### 4. Verify Cluster State
Ensure all resources have been successfully provisioned:
```bash
# Check if both pod replicas are Running
kubectl get pods

# Verify the Service is exposing port 8080
kubectl get services
```

### 5. Access the Application
Establish a secure tunnel to the Kubernetes Service:
```bash
kubectl port-forward service/kubernetes-test-app 8080:8080
```
Navigate to [http://localhost:8080](http://localhost:8080) in your web browser to interact with the application.

---

## 🤝 Contributing
Contributions, issues, and feature requests are highly encouraged! Feel free to check the [issues page](https://github.com/Rupeshbhardwaj002/K8-prjct/issues) to get involved.

## 📝 License
This project is open-source and distributed under the [MIT License](LICENSE).
