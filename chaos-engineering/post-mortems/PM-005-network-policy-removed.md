\# Post-Mortem 005 — Network Policy Removed



\## Incident Details

\- Date: 2025-05-13

\- Experiment: Removed default-deny-all policy

\- Duration: \~10 seconds (policy was missing)



\## What Happened

Default deny network policy was removed

simulating accidental policy deletion

or misconfiguration during a deployment.



\## Timeline

\- T+00s - default-deny-all policy deleted

\- T+00s - All pods could communicate freely

\- T+00s - Security gap opened

\- T+10s - Policy restored via kubectl apply

\- T+10s - Zero trust networking restored

\- T+10s - All 6 policies confirmed present



\## Policies Present Before Experiment

\- default-deny-all  (zero trust baseline)

\- db-policy         (app=db)

\- redis-policy      (app=redis)

\- result-policy     (app=result)

\- vote-policy       (app=vote)

\- worker-policy     (app=worker)



\## Impact

\- App functionality: Not affected

\- Security posture: Severely degraded for \~10s

\- Data exposure risk: High during gap

\- Time policy was missing: \~10 seconds



\## Security Risk Created

Without default-deny-all:

\- Any pod could reach any other pod

\- vote could directly query postgres (db)

\- Potential for lateral movement attacks

\- Blast radius of any compromise increased

\- Individual service policies alone

&#x20; are not sufficient without default deny



\## How I Fixed It

Immediately reapplied the policy:

kubectl apply -f network-policies/default-deny.yaml

Policy was recreated instantly.



\## Prevention

\- Use GitOps to manage network policies

\- Never manually delete security policies

\- Add policy existence checks to CI/CD pipeline

\- Alert when critical policies are deleted

\- Store all policies in version control (done)



\## Lessons Learned

\- Network policies are critical security layer

\- Absence of default-deny = full access = dangerous

\- Policy restoration is instant with kubectl apply

\- GitOps prevents accidental deletion

\- Individual service policies need the

&#x20; default-deny baseline to be effective

