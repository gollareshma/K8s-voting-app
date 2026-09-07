# Kubernetes Voting App — CI/CD, Monitoring & Chaos Engineering
  
  A polyglot microservice application deployed on **Kubernetes**, wrapped in the operational layer a real
  production service needs: automated delivery, autoscaling, network policy isolation, metrics and alerting,
  centralised logging, and documented failure testing.

  ---

  ## What this project is

  The application is Docker's well-known
  [example-voting-app](https://github.com/dockersamples/example-voting-app) — five services in four languages,
  chosen because it's a realistic distributed system rather than a single container.

  **The application code is upstream. Everything operational around it is the work of this project:**

  | | |
  |---|---|
  | ☸️  | Kubernetes manifests for all five services |
  | 🔄 | GitHub Actions pipeline — build, push, deploy |
  | 📈 | Prometheus metrics with Grafana dashboards |
  | 🚨 | Alert rules with email notification |
  | 🔒 | Zero-trust network policies |
  | ⚖️  | Horizontal Pod Autoscaling |
  | 📋 | Centralised logging |
  | 💥 | Chaos experiments with written post-mortems |

  ## Architecture

  ```mermaid
  flowchart LR
      U([User]) --> I[NGINX Ingress]
      I --> V["vote<br/>Python / Flask"]
      I --> R["result<br/>Node.js"]
      V --> RD[(Redis<br/>queue)]
      RD --> W["worker<br/>.NET"]
      W --> DB[(PostgreSQL)]
      DB --> R
  ```

  A vote is written to **Redis** rather than straight to the database. The **worker** drains that queue into
  **PostgreSQL** in its own time, and **result** reads from Postgres and pushes live updates to the browser.

  That indirection is the interesting part: a traffic spike lands in Redis, which absorbs it, instead of
  overwhelming Postgres. The vote service stays responsive even when writes are backing up — the queue is what
  decouples the two. The chaos experiments below test exactly that claim.

  | Service | Language | Role |
  |---|---|---|
  | `vote` | Python (Flask) | Front-end for casting a vote |
  | `redis` | Redis | Queue buffering incoming votes |
  | `worker` | .NET | Drains the queue into Postgres |
  | `db` | PostgreSQL | Durable vote storage |
  | `result` | Node.js | Live results dashboard |

  ---

  ## Deploying

  ### Prerequisites

  - A Kubernetes cluster (minikube, kind, EKS, or similar)
  - `kubectl` configured against it
  - NGINX Ingress Controller installed

  ### Deploy

  ```bash
  git clone https://github.com/gollareshma/K8s-voting-app.git
  cd K8s-voting-app

  kubectl create namespace dev
  kubectl apply -f k8s-specifications/ -n dev
  ```

  ### Verify

  ```bash
  kubectl get pods -n dev
  kubectl get svc -n dev
  kubectl get ingress -n dev
  ```

  Wait for every pod to reach `Running` — `worker` may restart a few times while it waits for Postgres to accept
  connections, which is expected.

  ### Run it locally instead

  ```bash
  docker compose up
  ```

  Vote on `localhost:8080`, results on `localhost:8081`.

  ---

  ## Operational layer

  ### CI/CD — GitHub Actions

  `.github/workflows/deploy.yml` runs on every push to `main`: it builds the `vote`, `result`, and `worker`
  images, pushes them to Docker Hub, and rolls the deployments forward.

  Configure these repository secrets under **Settings → Secrets and variables → Actions**:

  | Secret | Purpose |
  |---|---|
  | `DOCKERHUB_USERNAME` | Docker Hub account |
  | `DOCKERHUB_TOKEN` | Docker Hub access token — not your password |
  | `KUBE_CONFIG` | Base64-encoded kubeconfig for the target cluster |

  ### Autoscaling

  Manifests in `hpa/` scale pods on CPU utilisation, so a burst of votes adds replicas instead of degrading
  latency.

  ### Network policies

  `network-policies/` applies a **default-deny** posture: pods cannot talk to each other unless a policy
  explicitly permits it. `vote` may reach Redis; it may not reach Postgres directly. This limits lateral movement
  — a compromised front-end pod can't read the vote database.

  ### Monitoring

  Prometheus scrapes cluster and pod metrics; Grafana dashboards cover CPU, memory, pod status and counts. Alert
  rules fire on pod failures and route to email.

  ### Logging

  `logging/` centralises pod logs, so a crashed and restarted pod's output survives the container that produced
  it.

  ---

  ## Chaos engineering

  `chaos-engineering/` documents five deliberate failure experiments, each with a written post-mortem:

  | Experiment | What was broken | Post-mortem |
  |---|---|---|
  | PM-001 | Vote pod killed | [PM-001](chaos-engineering/post-mortems/PM-001-vote-pod-killed.md) |
  | PM-002 | Redis crashed | [PM-002](chaos-engineering/post-mortems/PM-002-redis-crashed.md) |
  | PM-003 | Traffic spike | [PM-003](chaos-engineering/post-mortems/PM-003-traffic-spike.md) |
  | PM-004 | Worker killed | [PM-004](chaos-engineering/post-mortems/PM-004-worker-killed.md) |
  | PM-005 | Network policy removed | [PM-005](chaos-engineering/post-mortems/PM-005-network-policy-removed.md) |

  A summary of all five is in [`CHAOS-REPORT.md`](chaos-engineering/CHAOS-REPORT.md).

  The reasoning: a resilience claim you haven't tested is a guess. Killing Redis and watching what the vote
  service actually does is the only way to know whether the queue really decouples it — and the post-mortems
  record what happened, not what was supposed to.

  ---

  ## Documentation

  | Document | Contents |
  |---|---|
  | [`ARCHITECTURE.md`](docs/ARCHITECTURE.md) | System design and component interaction |
  | [`SETUP-GUIDE.md`](docs/SETUP-GUIDE.md) | Step-by-step deployment |
  | [`COST-ESTIMATION.md`](docs/COST-ESTIMATION.md) | Cloud cost breakdown |
  | [`LESSONS-LEARNED.md`](docs/LESSONS-LEARNED.md) | What went wrong and what it taught |
  | [`FUTURE-IMPROVEMENTS.md`](docs/FUTURE-IMPROVEMENTS.md) | Planned next steps |
  | [`PROJECT-SUMMARY.md`](docs/PROJECT-SUMMARY.md) | Executive summary |

  ---

  ## Screenshots

  | | |
  |---|---|
  | ![Vote Page](screenshots/Vote%20page.png) | ![Result Page](screenshots/Result%20page.png) |
  | **Vote page** | **Result page** |
  | ![CPU Usage](screenshots/CPU%20usage.png) | ![Memory Usage](screenshots/Memory%20Usage.png) |
  | **Grafana — CPU** | **Grafana — memory** |
  | ![Pod Status](screenshots/Pod%20Status.png) | ![Alert Rules](screenshots/Alert%20Rules%20page.png) |
  | **Pod status** | **Alert rules** |
  | ![GitHub Actions](screenshots/GitHub%20Actions%20successful%20pipeline.png) | ![Docker
  Hub](screenshots/Docker%20Hub%20repositories.png) |
  | **CI/CD pipeline** | **Docker Hub images** |

  ---

  ## Credits

  The voting application — `vote`, `result`, `worker`, and their Dockerfiles — comes from
  [**dockersamples/example-voting-app**](https://github.com/dockersamples/example-voting-app), maintained by Bret
  Fisher and Michael Irwin, and is used here under the **Apache License 2.0** (see [`LICENSE`](LICENSE) and
  [`MAINTAINERS`](MAINTAINERS)).

  The Kubernetes manifests, CI/CD pipeline, monitoring and alerting stack, network policies, autoscaling,
  logging, chaos experiments, and documentation are original to this project.

  ---

  ## Author

  **Golla Reshma** — B.Tech (CSE — AI & ML)
