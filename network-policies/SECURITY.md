\# Network Security Documentation



\## Security Model

This project implements zero-trust networking

using Kubernetes Network Policies.



\## Traffic Flow (Allowed)

| Source  | Destination | Port | Reason |

|---------|-------------|------|--------|

| vote    | redis       | 6379 | Store votes |

| worker  | redis       | 6379 | Read votes |

| worker  | db          | 5432 | Save results |

| result  | db          | 5432 | Read results |



\## Traffic Flow (Blocked)

| Source  | Destination | Result |

|---------|-------------|--------|

| vote    | db          | BLOCKED ❌ |

| vote    | result      | BLOCKED ❌ |

| result  | redis       | BLOCKED ❌ |

| redis   | db          | BLOCKED ❌ |



\## Tests Performed

1\. Verified vote cannot reach postgres ✅

2\. Verified vote can reach redis ✅

3\. Verified app still works end to end ✅



\## Why This Matters

\- Prevents lateral movement if a pod is compromised

\- Each service only has minimum required access

\- Follows principle of least privilege

