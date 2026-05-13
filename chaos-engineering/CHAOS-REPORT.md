\# Chaos Engineering Report

\## Voting App on Kubernetes



\## Overview

5 chaos experiments were conducted to test

system resilience, recovery capabilities,

and security posture of the voting app

running on Kubernetes (Minikube).



\## Experiments Summary



| # | Experiment | Recovery Time | Data Lost | Auto Recovered |

|---|-----------|---------------|-----------|----------------|

| 1 | Vote pod killed | 13s | No | Yes ✅ |

| 2 | Redis crashed | 6s | Yes (no persistence) | Yes ✅ |

| 3 | Traffic spike (5 generators) | N/A | No | Yes ✅ |

| 4 | Worker killed | 10s | No | Yes ✅ |

| 5 | Network policy removed | Instant fix | No | Manual ⚠️ |



\## Key Findings



\### What Worked Well

1\. Kubernetes ReplicaSet self-healing

&#x20;  → All pods recovered automatically every time

2\. Redis message queue

&#x20;  → Prevented vote data loss during worker failure

3\. HPA auto scaling

&#x20;  → result-hpa scaled 2→3 replicas on memory pressure

4\. Health checks

&#x20;  → Traffic only routed to healthy pods

5\. Network policies

&#x20;  → Restored instantly when removed



\### What Needs Improvement

1\. Redis persistence

&#x20;  → No PVC means data lost on Redis crash

&#x20;  → Fix: appendonly yes + volumeMount applied

2\. Multiple replicas for vote service

&#x20;  → Single replica caused 13s downtime

&#x20;  → Solution: Run 2+ replicas in production

3\. GitOps for policy management

&#x20;  → Prevent accidental policy deletion

4\. Worker retry logic

&#x20;  → Worker crashed when Redis disappeared

&#x20;  → Needs connection retry/backoff logic



\## Resilience Score

| Component | Score | Notes |

|-----------|-------|-------|

| Pod recovery | 10/10 | Instant auto recovery every time |

| Data preservation | 7/10 | Redis needs persistence |

| Traffic handling | 9/10 | HPA memory scaling works |

| Security | 8/10 | Policies need GitOps protection |

| Overall | 8.5/10 | Production ready with fixes |



\## Improvements Applied

1\. Redis persistence configured (appendonly yes)

2\. Post-mortems written for all 5 experiments

3\. All recommendations documented



\## Conclusion

The voting app demonstrated strong resilience

characteristics. Kubernetes self-healing,

message queue buffering, and auto scaling

worked correctly in all experiments.

Main areas for production improvement are

Redis persistence and GitOps policy management.

