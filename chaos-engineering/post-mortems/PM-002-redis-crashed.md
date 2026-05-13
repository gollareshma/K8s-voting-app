\# Post-Mortem 002 — Redis Pod Crashed



\## Incident Details

\- Date: 2025-05-13

\- Experiment: Deleted Redis pod

\- Duration: 6 seconds (deletion to recovery)



\## What Happened

Redis pod was deleted to simulate

a cache/queue failure.



\## Timeline

\- T+00s - Redis pod deleted

\- T+00s - Vote service lost connection to Redis

\- T+00s - New Redis pod creation started (Pending)

\- T+02s - New Redis pod ContainerCreating

\- T+06s - New Redis pod Ready 1/1

\- T+07s - Worker pod crashed with Error (lost Redis connection)

\- T+07s - Worker pod auto-restarted by ReplicaSet

\- T+07s - Normal operation resumed



\## Impact

\- Votes during Redis downtime: Lost (no persistence)

\- Vote page behavior: Showed "No votes yet" after recovery

\- Result page affected: Yes — vote counts reset to zero

\- Worker affected: Yes — crashed and auto-restarted

\- Data lost: Yes — all in-memory votes lost



\## Root Cause

Redis pod terminated simulating

a cache failure or OOM kill.

Redis had no persistent volume,

so all data was stored in memory only.



\## How System Responded

ReplicaSet immediately created new Redis pod

in 6 seconds. Worker self-healed automatically

after losing Redis connection. Vote and result

services recovered without manual intervention.



\## Recovery Time

6 seconds for Redis, 7 seconds for worker



\## What I Observed

\- Redis has no persistent volume

\- Vote count reset to zero after recovery

\- Worker crashed but immediately self-healed

\- After recovery app worked normally

\- Vote page showed 50/50 split was gone



\## Improvements Made

\- Added PersistentVolumeClaim for Redis

&#x20; to preserve data across restarts

\- Redis AOF (appendonly) persistence

&#x20; will be applied in Part 3



\## Lessons Learned

\- In-memory stores need persistence

&#x20; for production use

\- Redis AOF persistence prevents data loss

\- Worker is tightly coupled to Redis —

&#x20; needs retry logic for resilience

\- Health checks reduce recovery time

