\# AWS Cost Estimation

\## Voting App on Amazon EKS



\## Overview

This document estimates the monthly cost

of running this voting app on AWS EKS

in a production environment.



\---



\## Architecture on AWS

Users

↓

Route 53 (DNS)

↓

Application Load Balancer

↓

EKS Cluster

├── Node Group (EC2 instances)

│     ├── vote pods

│     ├── result pods

│     ├── worker pods

│     ├── redis pods

│     └── db pods

├── Monitoring (Prometheus + Grafana)

├── Logging (EFK Stack)

└── HPA (Auto Scaling)

↓

RDS PostgreSQL (managed)

↓

ElastiCache Redis (managed)



\---



\## Cost Breakdown — Production Setup



\### 1. EKS Control Plane

| Item | Cost |

|------|------|

| EKS Cluster | $0.10/hour |

| Monthly total | $73/month |



\### 2. EC2 Worker Nodes

| Instance | Type | Count | Cost/month |

|----------|------|-------|------------|

| App nodes | t3.medium | 2 | $60/month |

| Monitoring node | t3.large | 1 | $60/month |

| Total | | 3 | $120/month |



\### 3. Database — RDS PostgreSQL

| Item | Cost |

|------|------|

| db.t3.micro | $15/month |

| Storage 20GB | $2.30/month |

| Multi-AZ (prod) | $30/month |

| Total | $47/month |



\### 4. Cache — ElastiCache Redis

| Item | Cost |

|------|------|

| cache.t3.micro | $12/month |

| Total | $12/month |



\### 5. Load Balancer

| Item | Cost |

|------|------|

| ALB | $16/month |

| LCU charges | \~$5/month |

| Total | $21/month |



\### 6. Storage

| Item | Cost |

|------|------|

| EBS volumes (3 nodes) | $30/month |

| EBS for RDS | $5/month |

| Total | $35/month |



\### 7. Data Transfer

| Item | Cost |

|------|------|

| Outbound data (\~100GB) | $9/month |

| Total | $9/month |



\### 8. Route 53

| Item | Cost |

|------|------|

| Hosted zone | $0.50/month |

| DNS queries | \~$1/month |

| Total | $1.50/month |



\### 9. CloudWatch Logging

| Item | Cost |

|------|------|

| Log ingestion | $5/month |

| Log storage | $3/month |

| Total | $8/month |



\---



\## Total Monthly Cost



| Component | Cost |

|-----------|------|

| EKS Control Plane | $73 |

| EC2 Worker Nodes | $120 |

| RDS PostgreSQL | $47 |

| ElastiCache Redis | $12 |

| Load Balancer | $21 |

| Storage | $35 |

| Data Transfer | $9 |

| Route 53 | $1.50 |

| CloudWatch | $8 |

| \*\*TOTAL\*\* | \*\*\~$326/month\*\* |



\---



\## Cost Optimization Strategies



\### Strategy 1 — Use Spot Instances

Current: On-demand EC2 = $120/month

With Spots: $120 \* 0.3 = $36/month

Saving: $84/month (70% reduction)

Use spot for:

\- Worker nodes (stateless)

\- Monitoring nodes

Keep on-demand for:

\- Database nodes (stateful)



\### Strategy 2 — Right Sizing

Current: t3.medium for all nodes

After analysis:

\- vote/result pods → t3.small ($30/month)

\- worker pods → t3.micro ($15/month)

\- Saving: \~$40/month



\### Strategy 3 — Auto Scaling Cluster

Current: 3 nodes running 24/7

With Cluster Autoscaler:

\- Scale down to 1 node at night

\- Scale up during business hours

\- Saving: \~$50/month



\### Strategy 4 — Replace Self-Managed with Managed

Current (self-managed):

\- Redis on pod = reliability risk

\- PostgreSQL on pod = data risk



Recommended:

\- ElastiCache Redis = more reliable

\- RDS PostgreSQL = automated backups

\- Cost increase: $59/month

\- Risk reduction: Very high

\- Worth it for production



\### Strategy 5 — Reserved Instances

Current: On-demand pricing

With 1-year reserved:

\- Save 40% on EC2 costs

\- $120 → $72/month

\- Saving: $48/month



\### Strategy 6 — Use Graviton (ARM) Instances

\- t3.medium (x86) = $30/month

\- t4g.medium (ARM) = $23/month

\- Saving: 23% on compute costs

\- Works perfectly with our containers



\---



\## Optimized Cost Estimate



| Optimization | Saving |

|-------------|--------|

| Spot instances | -$84 |

| Right sizing | -$40 |

| Cluster autoscaler | -$50 |

| Reserved instances | -$48 |

| Graviton instances | -$20 |

| \*\*Total Saving\*\* | \*\*-$242\*\* |

| \*\*Optimized Total\*\* | \*\*\~$84/month\*\* |



\---



\## Cost Comparison by Environment



| Environment | Setup | Monthly Cost |

|-------------|-------|-------------|

| Local (current) | Minikube | $0 |

| Dev/Staging | Single node EKS | \~$80 |

| Production | Full setup | \~$326 |

| Production optimized | Spots + Reserved | \~$84 |



\---



\## Free Tier Options for Small Scale



If running as a small project:

\- EKS → Use k3s on single EC2 t3.small

\- RDS → Use t3.micro (free tier 12 months)

\- Redis → Use ElastiCache t2.micro (free tier)

\- ALB → Use Nginx Ingress instead

\- Estimated cost: \~$15/month



\---



\## Cost Monitoring Recommendations



1\. Set up AWS Budgets alert at $350/month

2\. Use AWS Cost Explorer weekly

3\. Tag all resources with project name

4\. Use Kubecost for per-namespace cost tracking

5\. Review right-sizing recommendations monthly



\---



\## Summary



Running this voting app on AWS EKS costs

approximately $326/month for a production

setup. With optimizations like spot instances,

reserved pricing, and cluster autoscaling,

this can be reduced to approximately

$84/month — a 74% cost reduction.



For a real production app with thousands

of users, these costs would be justified

by the reliability, scalability, and

managed service benefits AWS provides.

