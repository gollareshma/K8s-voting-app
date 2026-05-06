# Kubernetes Voting App with CI/CD & Monitoring

## Project Overview
Cloud-native voting application deployed on Kubernetes with CI/CD automation, monitoring, ingress, and alerting.

---

## Tech Stack

- Kubernetes
- Docker
- GitHub Actions
- Prometheus
- Grafana
- NGINX Ingress
- Docker Hub

---

## Features

- Kubernetes deployment
- CI/CD pipeline using GitHub Actions
- Dockerized microservices
- Prometheus monitoring
- Grafana dashboards
- Alerting system
- Ingress routing
- Zero trust network policies

---

## Docker Hub Repositories

- vote
- result
- worker

---

## Monitoring Stack

- Prometheus
- Grafana
- Alert Rules
- Email Notifications

---

## Screenshots

### CPU Usage
![CPU Usage](screenshots/CPU%20usage.png)

### Memory Usage
![Memory Usage](screenshots/Memory%20Usage.png)

### Pod Status
![Pod Status](screenshots/Pod%20Status.png)

### Total Pods
![Total Pods](screenshots/Total%20Pods.png)

### Alert Rules
![Alert Rules](screenshots/Alert%20Rules%20page.png)

### Notification Configuration
![Notification](screenshots/Notification%20Configuration%20page.png)

### Docker Hub
![Docker Hub](screenshots/Docker%20Hub%20repositories.png)

### GitHub Actions
![GitHub Actions](screenshots/GitHub%20Actions%20successful%20pipeline.png)

### Vote Page
![Vote Page](screenshots/Vote%20page.png)

### Result Page
![Result Page](screenshots/Result%20page.png)

---

## Commands Used

### Check Pods

```bash
kubectl get pods -n dev
```

### Check Services

```bash
kubectl get svc -n monitoring
```

### Check Ingress

```bash
kubectl get ingress -n dev
```

---

## CI/CD Pipeline

GitHub Actions automatically:
- Builds Docker images
- Pushes images to Docker Hub
- Deploys updates to Kubernetes

---

## Monitoring & Alerting

- Prometheus collects metrics
- Grafana visualizes metrics
- Alerts configured for pod failures