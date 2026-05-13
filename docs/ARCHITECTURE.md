\# Architecture Document

\## Cloud Native Voting App on Kubernetes



\## System Overview

A cloud-native voting application built on

Kubernetes demonstrating production-grade

DevOps practices.



\## Tech Stack

| Layer | Technology | Purpose |

|-------|-----------|---------|

| Frontend | Python Flask | Vote UI |

| Queue | Redis | Vote buffering |

| Processing | .NET Worker | Vote processor |

| Database | PostgreSQL | Persistent storage |

| Results | Node.js | Results UI |

| Container | Docker | App packaging |

| Orchestration | Kubernetes | Container management |

| CI/CD | GitHub Actions | Automation |

| Monitoring | Prometheus + Grafana | Observability |

| Logging | EFK Stack | Centralized logs |

| Security | Network Policies | Zero trust network |

| Scaling | HPA | Auto scaling |

| CNI | Calico | Network policy enforcement |



\## Architecture Diagram

Developer

↓

GitHub (Source Code)

↓

GitHub Actions (CI/CD)

↓ Build + Push

Docker Hub (Images)

↓ Pull

Kubernetes Cluster (Minikube)

↓

┌─────────────────────────────────┐

│  dev namespace                  │

│                                 │

│  \[Ingress Controller]           │

│         ↓                       │

│  \[vote:Flask] → \[redis]         │

│                     ↓           │

│              \[worker:.NET]      │

│                     ↓           │

│            \[postgres:db]        │

│                     ↓           │

│         \[result:Node.js]        │

│                                 │

│  Network Policies (Calico)      │

│  HPA (Auto Scaling)             │

└─────────────────────────────────┘

↓              ↓

┌──────────────┐ ┌────────────────┐

│  monitoring  │ │    logging     │

│  Prometheus  │ │ Elasticsearch  │

│  Grafana     │ │ Fluentd        │

│  Alertmanager│ │ Kibana         │

└──────────────┘ └────────────────┘



\## Data Flow



1\. User opens vote.local in browser

2\. Ingress routes to vote service

3\. User clicks vote option

4\. Vote service stores vote in Redis

5\. Worker reads from Redis queue

6\. Worker writes result to PostgreSQL

7\. Result service reads from PostgreSQL

8\. User sees result at result.local



\## Namespace Structure

kubernetes cluster

├── dev (application)

│   ├── vote deployment

│   ├── result deployment

│   ├── worker deployment

│   ├── redis deployment

│   ├── db deployment

│   ├── ingress

│   ├── network policies

│   └── hpa

├── monitoring

│   ├── prometheus

│   ├── grafana

│   └── alertmanager

├── logging

│   ├── elasticsearch

│   ├── kibana

│   └── fluentd

└── kube-system

&#x20;   ├── calico

&#x20;   ├── metrics-server

&#x20;   └── ingress-nginx



\## Security Architecture

Network Policy Rules:

\- vote    → redis     ✅ (port 6379)

\- vote    → db        ❌ BLOCKED

\- vote    → result    ❌ BLOCKED

\- worker  → redis     ✅ (port 6379)

\- worker  → db        ✅ (port 5432)

\- result  → db        ✅ (port 5432)

\- result  → redis     ❌ BLOCKED

\- \*       → \*         ❌ BLOCKED (default deny)



\## Resilience Features



\- ReplicaSet → Auto pod recovery

\- Health checks → Traffic routing

\- HPA → Auto scaling

\- Redis queue → No vote data loss

\- Network policies → Blast radius control

\- Resource limits → No resource starvation



\## CI/CD Pipeline

git push → GitHub

↓

GitHub Actions triggers

↓

Build vote image → Push to Docker Hub

Build result image → Push to Docker Hub

Build worker image → Push to Docker Hub

↓

Verify images pulled successfully

↓

Pipeline complete ✅

