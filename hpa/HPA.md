\# Horizontal Pod Autoscaler Documentation



\## Overview

HPA automatically scales pods based on

CPU and memory usage.



\## HPAs Configured



\### Vote Service HPA

\- Min replicas: 1

\- Max replicas: 5

\- CPU threshold: 50%

\- Memory threshold: 80%

\- Scale up: 2 pods per 30 seconds

\- Scale down: 1 pod per 60 seconds



\### Result Service HPA

\- Min replicas: 1

\- Max replicas: 3

\- CPU threshold: 50%

\- Memory threshold: 70%



\### Worker Service HPA

\- Min replicas: 1

\- Max replicas: 3

\- CPU threshold: 60%

\- Memory threshold: 70%



\## Load Test Results



\### Vote Service

\- Normal CPU usage: \~0%

\- Under load CPU usage: \~160%

\- Time to scale up: \~30 seconds

\- Time to scale down: \~5 minutes

\- Max pods observed: 5



\## Why HPA Matters

\- Handles traffic spikes automatically

\- No manual intervention needed

\- Saves resources during low traffic

\- Ensures app stays responsive



\## Commands

\# Check HPA status

kubectl get hpa -n dev



\# Watch HPA live

kubectl get hpa -n dev -w



\# Detailed HPA info

kubectl describe hpa vote-hpa -n dev



\# Check pod count

kubectl get pods -n dev

