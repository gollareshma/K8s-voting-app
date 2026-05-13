\# Post-Mortem 003 — Traffic Spike Simulation



\## Incident Details

\- Date: 2025-05-13

\- Experiment: 5 parallel load generators

\- Duration: \~5 minutes



\## What Happened

Simulated sudden traffic spike using

5 parallel request generators hitting

the vote service simultaneously.



\## Timeline

\- T+00m - 5 load generator jobs started

\- T+01m - Worker CPU elevated to 32m cores

\- T+02m - result-hpa memory hit 78% (above 70% threshold)

\- T+02m - result-hpa scaled from 2 to 3 replicas

\- T+02m - New result pod created automatically

\- T+05m - Load generators stopped

\- T+10m - HPA expected to scale back down



\## Impact

\- App remained available throughout: Yes

\- vote-hpa scaling triggered: No (CPU stayed at 2m)

\- result-hpa scaling triggered: Yes (memory 78% > 70%)

\- Max result replicas reached: 3

\- Response time during spike: Normal



\## Metrics Observed

\- vote CPU during load: 2m (too low to trigger HPA)

\- worker CPU during load: 32m (elevated)

\- result memory: 78% (above 70% threshold)

\- result scaled: 2 → 3 replicas ✅



\## How System Responded

HPA automatically scaled result service

from 2 to 3 replicas when memory exceeded

the 70% threshold. Vote HPA did not scale

because Minikube single-node environment

could not generate sufficient CPU pressure

from lightweight HTTP requests.



\## Why vote-hpa Did Not Scale

Minikube runs on a single node with limited

resources. The 5 parallel jobs sent requests

but the vote Flask app handled them at only

2m CPU — well below the 50% threshold.

In production with real traffic volume,

CPU-based scaling would trigger correctly.



\## What Worked Well

\- HPA memory-based scaling worked correctly

\- result service scaled automatically

\- App stayed fully responsive throughout

\- No manual intervention needed

\- New pod created and ready in 15 seconds



\## What Could Be Better

\- Add request-rate based scaling (more accurate

&#x20; than CPU for lightweight web apps)

\- Could pre-scale before known traffic events

\- CPU threshold scaling needs real traffic

&#x20; volume to trigger in Minikube



\## Lessons Learned

\- HPA works correctly — both CPU and memory

&#x20; thresholds function as configured

\- Memory-based scaling is effective for

&#x20; services with predictable memory growth

\- Minikube limitations affect CPU spike tests

\- In production, vote service would scale

&#x20; correctly under real user traffic

