\# Lessons Learned

\## Building Production Grade Kubernetes App



\## Overview

Key lessons from building this project

over 9 days as a DevOps learning exercise.



\---



\## Technical Lessons



\### 1. Kubernetes

\- ReplicaSets provide automatic self healing

\- Resource limits are mandatory not optional

\- Health probes prevent bad traffic routing

\- Namespaces organize workloads cleanly

\- CNI plugin choice affects features available



\### 2. Networking

\- Default deny all is the right starting point

\- Network policies need careful testing

\- Ingress simplifies external access

\- DNS resolution works by service name



\### 3. CI/CD

\- GitHub Actions is powerful and free

\- Secrets management is critical

\- Image tagging with git SHA enables rollback

\- Every push should trigger a pipeline



\### 4. Monitoring

\- Prometheus + Grafana is industry standard

\- Always monitor CPU memory and restarts

\- Alerts prevent you finding out from users

\- Custom dashboards show what matters to you



\### 5. Logging

\- Centralized logging is non negotiable

\- Fluentd as DaemonSet captures everything

\- Structured logs are easier to search

\- EFK stack is resource intensive



\### 6. Chaos Engineering

\- Breaking things teaches more than building

\- Post mortems build institutional knowledge

\- Redis needs persistence for production

\- Single replicas cause brief downtime



\### 7. Cost

\- Local development costs nothing

\- Production AWS costs \~$326/month

\- Spot instances save 70% on compute

\- Reserved instances save 40% long term



\---



\## What I Would Do Differently



\### 1. Start with GitOps

Use ArgoCD from day one to manage

all Kubernetes manifests declaratively.



\### 2. Use Managed Services

Replace self managed Redis and PostgreSQL

with ElastiCache and RDS from the start.



\### 3. Add More Replicas

Run minimum 2 replicas of each service

to avoid downtime during pod restarts.



\### 4. Better Secret Management

Use HashiCorp Vault or AWS Secrets Manager

instead of Kubernetes secrets.



\### 5. Add Service Mesh

Istio or Linkerd would add:

\- mTLS between services

\- Better observability

\- Traffic management

\- Circuit breaking



\### 6. Implement RBAC

Role based access control to limit

what each service account can do.



\---



\## What I Would Add Next



1\. ArgoCD for GitOps deployment

2\. Istio service mesh

3\. HashiCorp Vault for secrets

4\. Terraform for infrastructure

5\. SonarQube for code quality

6\. Trivy for container scanning

7\. OPA Gatekeeper for policy enforcement

8\. Velero for backup and restore



\---



\## Skills Gained



| Skill | Level Before | Level After |

|-------|-------------|-------------|

| Docker | Beginner | Intermediate |

| Kubernetes | None | Intermediate |

| Helm | None | Intermediate |

| GitHub Actions | None | Intermediate |

| Prometheus | None | Intermediate |

| Grafana | None | Intermediate |

| EFK Stack | None | Beginner |

| Network Policies | None | Intermediate |

| Chaos Engineering | None | Beginner |

| Cost Estimation | None | Intermediate |



\---



\## Final Thoughts



This project taught me that DevOps is not

just about deploying applications. It is

about reliability, observability, security,

and cost efficiency working together.



The most valuable lesson was chaos engineering

— intentionally breaking the system taught

me more about how Kubernetes works than

any tutorial could.

