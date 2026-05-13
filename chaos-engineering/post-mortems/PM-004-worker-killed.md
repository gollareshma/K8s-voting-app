\# Post-Mortem 004 — Worker Pod Killed



\## Incident Details

\- Date: 2025-05-13

\- Experiment: Deleted worker pod

\- Duration: 10 seconds (deletion to ready)



\## What Happened

Worker pod deleted to simulate

processing service failure.



\## Timeline

\- T+00s - Worker pod worker-7b9765554d-n2b9p deleted

\- T+00s - Votes queued in Redis during downtime

\- T+01s - New worker pod Pending

\- T+02s - New worker pod ContainerCreating

\- T+10s - New worker pod Running 1/1

\- T+10s - Worker reconnected to Redis

\- T+10s - Queued votes processed automatically



\## Impact

\- Vote submission: Still worked (Redis queued votes)

\- Result updates: Stopped during 10s downtime

\- Data lost: No (Redis held the queue)

\- Recovery: Fully automatic, no intervention needed



\## What I Observed

\- Redis acted as buffer during worker downtime

\- Vote page continued accepting votes

\- Result page stopped updating during downtime

\- Worker processed backlog after recovery

\- Result page caught up automatically

\- Result page showed No votes yet because

&#x20; Redis was wiped in previous experiment

&#x20; (no persistence — confirmed finding from PM-002)



\## How System Responded

Kubernetes ReplicaSet immediately created

new worker pod in 10 seconds. Redis queue

preserved all votes during the downtime period.

New worker reconnected and processed backlog.



\## Lessons Learned

\- Message queue (Redis) prevents data loss

\- Microservices architecture isolates failures

\- Worker failure does not affect vote submission

\- This is the power of decoupled architecture

\- Redis persistence (PM-002 fix) is critical

&#x20; so queued votes survive a Redis restart too

