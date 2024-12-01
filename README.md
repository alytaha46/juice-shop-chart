# Juice Shop Helm Chart

This repository contains a Helm chart for deploying the OWASP Juice Shop application, a security training tool for web developers and security professionals.

## Chart Overview

- **Name**: Juice Shop
- **Version**: 1.0.0
- **App Version**: latest
- **Description**: Helm chart for deploying Juice Shop.

## Repository Structure

- **Chart.yaml**: Metadata for the Helm chart.
- **values.yaml**: Default configuration values for deploying the chart.
- **templates/**: Helm templates for Kubernetes resources:
  - `deployment.yaml`: Deployment configuration for Juice Shop.
  - `service.yaml`: Service definition for exposing the application.
  - `ingress.yaml`: Ingress configuration for external access.


## Prerequisites

- Kubernetes cluster (v1.20+ recommended)
- Helm 3.x or higher
- An Ingress controller (e.g., NGINX)

## Installation

1. Add the repository (if hosted remotely):
   ```bash
   helm repo add juice-shop https://your-repo-url
   helm repo update
   ```

2. Install the chart:
   ```bash
   helm install juice-shop ./juice-shop-chart
   ```

## Configuration

You can customize the chart by modifying the `values.yaml` file:

- **Replica Count**: Control the number of replicas:
  ```yaml
  replicaCount: 1
  ```
- **Image**:
  ```yaml
  image:
    repository: bkimminich/juice-shop
    tag: latest
    pullPolicy: IfNotPresent
  ```
- **Service**:
  ```yaml
  service:
    type: ClusterIP
    port: 80
    targetPort: 3000
  ```
- **Ingress**:
  ```yaml
  ingress:
    enabled: true
    className: nginx
    host: ""
    rewriteTarget: "/"
    path: "/"
    pathType: Prefix
  ```


## Workflows

The repository contains the following GitHub workflows:

- **nginx-ingress-controller.yaml**:
```yaml
name: Deploy NGINX ingress controller Helm Chart
on:
    workflow_dispatch:

jobs:
    deploy:
        runs-on: self-hosted

        steps:
        - name: Checkout repository
          uses: actions/checkout@v2

        - name: Deploy Helm chart
          run: |
            aws eks update-kubeconfig --name k8s-task-cluster --region eu-west-1 
            helm install ingress-nginx ingress-nginx/ingress-nginx \
            --namespace ingress-nginx \
            --create-namespace \
            --set controller.service.type=LoadBalancer
```

- **deploy.yaml**:
```yaml
name: Deploy juice-shop Helm Chart
on:
    workflow_dispatch:

jobs:
    deploy:
        runs-on: self-hosted

        steps:
        - name: Checkout repository
          uses: actions/checkout@v2

        - name: Deploy Helm chart
          run: |
            aws eks update-kubeconfig --name k8s-task-cluster --region eu-west-1 
            helm upgrade --install juice-shop . --namespace juice-shop --create-namespace
```

