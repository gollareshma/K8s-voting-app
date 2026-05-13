\# Post-Mortem 001 — Vote Pod Killed



\## Incident Details

\- Date: 2025-05-13

\- Experiment: Manually deleted vote pod

\- Duration: 13 seconds (deletion to ready)



\## What Happened

Manually deleted the vote deployment pod

to simulate a pod crash or node failure.



\## Timeline

\- T+00s - Pod deleted using kubectl delete pod

\- T+01s - Pod entered Terminating state

\- T+03s - New pod created by ReplicaSet (Pending)

\- T+06s - New pod entered Running state

\- T+13s - New pod Ready 1/1 — App fully recovered



\## Impact

\- Vote service unavailable for: \~13 seconds

\- Result service affected: No

\- Data lost: No (Redis persisted votes)



\## Root Cause

Pod was manually terminated simulating

a crash or eviction scenario.



\## How Kubernetes Handled It

ReplicaSet controller detected pod count

dropped below desired state (1) and

immediately created a new pod.



\## Recovery Time

13 seconds from deletion to ready state



\## What I Observed

\- Kubernetes self-healing worked correctly

\- Browser showed ERR\_CONNECTION\_REFUSED

&#x20; for \~13 seconds during pod restart

\- No manual intervention needed

\- Data was not lost

\- Other services were not affected



\## Improvements Made

\- Readiness probe ensures traffic only

&#x20; goes to healthy pods

\- Liveness probe restarts unhealthy pods

&#x20; automatically



\## Lessons Learned

\- Kubernetes ReplicaSet provides automatic

&#x20; pod recovery

\- Single replica means brief downtime (\~13s)

\- Solution: Run 2+ replicas in production

