\# Setup Guide

\## Running Voting App Locally



\## Prerequisites

\- Windows 10/11

\- Docker Desktop installed

\- 16GB RAM recommended

\- 4 CPU cores recommended



\## Tools Required

| Tool | Version | Purpose |

|------|---------|---------|

| Docker Desktop | Latest | Container runtime |

| Minikube | v1.38+ | Local Kubernetes |

| kubectl | v1.34+ | K8s CLI |

| Helm | v4.1+ | Package manager |

| Git | Latest | Version control |



\## Installation Steps



\### Step 1 — Install Tools

```bash

\# kubectl

winget install -e --id Kubernetes.kubectl



\# Helm

winget install -e --id Helm.Helm



\# Minikube from:

\# https://minikube.sigs.k8s.io/docs/start/

```



\### Step 2 — Start Minikube

```bash

minikube start \\

&#x20; --cpus=4 \\

&#x20; --memory=8192 \\

&#x20; --driver=docker \\

&#x20; --cni=calico

```



\### Step 3 — Enable Addons

```bash

minikube addons enable ingress

minikube addons enable metrics-server

```



\### Step 4 — Create Namespaces

```bash

kubectl create namespace dev

kubectl create namespace monitoring

kubectl create namespace logging

```



\### Step 5 — Deploy Application

```bash

kubectl apply -f k8s-specifications/ -n dev

```



\### Step 6 — Apply Network Policies

```bash

kubectl apply -f network-policies/ -n dev

```



\### Step 7 — Apply HPA

```bash

kubectl apply -f hpa/ -n dev

```



\### Step 8 — Install Monitoring

```bash

helm repo add prometheus-community \\

https://prometheus-community.github.io/helm-charts



helm install prometheus \\

prometheus-community/kube-prometheus-stack \\

\--namespace monitoring

```



\### Step 9 — Install Logging

```bash

helm repo add elastic https://helm.elastic.co



helm install elasticsearch elastic/elasticsearch \\

\--namespace logging \\

\-f logging/elasticsearch-values.yaml



helm install kibana elastic/kibana \\

\--namespace logging \\

\-f logging/kibana-values.yaml



kubectl apply -f logging/fluentd-configmap.yaml

kubectl apply -f logging/fluentd-daemonset.yaml

```



\### Step 10 — Access the App

```bash

\# Vote app

minikube service vote -n dev



\# Result app

minikube service result -n dev



\# Grafana (run in separate terminal)

kubectl port-forward svc/prometheus-grafana \\

3000:80 -n monitoring



\# Kibana (run in separate terminal)

kubectl port-forward svc/kibana-kibana \\

5601:5601 -n logging

```



\## Verify Everything Running

```bash

kubectl get pods -n dev

kubectl get pods -n monitoring

kubectl get pods -n logging

kubectl get hpa -n dev

kubectl get networkpolicies -n dev

```



\## Default Credentials

| Service | Username | Password |

|---------|----------|----------|

| Grafana | admin | (get from secret) |

| Kibana | No auth | No auth |

| Elasticsearch | No auth | No auth |

