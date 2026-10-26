# devops-90 — 90 Days of DevOps / Cloud / Platform-with-AI, in public

One concept a day, 30 minutes, 7 days a week. Every day produces **one visible artifact** (a commit) and **one sentence of proof** (a post). If it isn't pushed and posted, the day didn't happen.

By Week 12 this repo is not a pile of tutorials — it's **one working, documented platform** I built layer by layer: a FastAPI + Postgres app, containerized, running on Kubernetes, provisioned with Terraform, shipped by GitOps, watched by Prometheus/Grafana, secured with TLS and policy, serving a RAG endpoint, and gated by an LLM eval suite in CI.

- **Start date:** _fill in day 1_
- **Cadence:** 30 min/day · 7 days/week · no skipped days · one green square per day
- **Owner:** Etienne

---

## The daily ritual (same every day)

| Min | Phase | What I do |
|---|---|---|
| 0–5 | **Recall** | Without looking, re-type yesterday's key command(s) in a scratch terminal. Can't? That's today's first lookup. |
| 5–20 | **Learn + do** | Read/watch *only* today's one concept, then immediately run it. Keep the doc open; don't binge ahead. |
| 20–27 | **Commit** | Save the day's artifact, add a 1–2 line `LEARNING_LOG.md` entry, `git commit`, `git push`. |
| 27–30 | **Post** | One sentence to X + LinkedIn: *what I did today + one thing that surprised me.* Link the commit. |

Daily post template (keep it to 2 minutes, every day the same shape):

> Day N/90 — [topic]. Today I [did X]. Surprise: [one thing]. ↳ [repo link]

**If I miss a day:** don't double up tomorrow — that re-creates the overload that kills retention. Do the missed day next, push it late, keep the chain. A late commit is still a commit.

---

## How the weeks work

Each week = **one theme, one small project that extends last week's, one article.** Days are split across the week as I reach it — I only expand the current month's day-by-day files, never planning more than ~4 weeks ahead (that's a focus/retention choice, not laziness).

- **Days 1–6:** one new concept each, hands on keyboard, one commit each.
- **Days 5–6:** re-type the week's core commands from memory (muscle-memory set) so they flow without thinking.
- **Day 7 — consolidate + publish (the retention day):** no new topic. I re-type the week's commands from a blank terminal (that's my real gap list), then **write the week's article** by assembling the 6 days of notes and adding *why each thing matters*. The article is the strongest memory operation there is — explaining it in my own words is what makes the week stick into the next one. **This is the day I look back at the article to remember what must not be forgotten before the next week builds on it.**

---

## Repo layout

```
devops-90/
  README.md            <- this file, updated weekly as the architecture grows
  LEARNING_LOG.md      <- one dated line per day (the proof-of-work chain)
  week-01-linux-git/
    bin/               <- reusable scripts
    day-01/ ... day-07/
    README.md
  week-02-docker/
  week-03-compose/
  ...
  articles/            <- markdown source of each weekly article (week-01.md …)
```

- **One commit per day, minimum.** Small is fine — a Dockerfile, a fixed command, a note. The chain matters more than the size.
- Real secrets stay out (`.gitignore` them); commit `*.example` files instead. (Security habit started early — see Week 10.)

---

## The 12-week spine

Each row is a week. Day-by-day files get written when I reach that week.

| Wk | Theme | Week project (adds to this repo) | Article (Day 7) |
|---|---|---|---|
| **1** | Linux & shell fluency + Git/GitHub | A `bin/` of small shell scripts + clean Git workflow + dotfiles | "The 20 Linux commands a DevOps engineer actually types all day" |
| **2** | Docker & containers | Containerize a FastAPI app into a lean, multi-stage image | "What a Docker image really is: layers, processes, and why your image is 900MB" |
| **3** | Docker Compose (multi-service) | FastAPI **+ Postgres** together via one `compose.yaml` | "Running a real app stack locally with Docker Compose" |
| **4** | Kubernetes fundamentals | Deploy the same app to local K8s: pods, deployment, service | "Kubernetes from Compose: the smallest real deployment that works" |
| **5** | Kubernetes in depth | Helm chart, config/secrets, probes, ingress, a deliberate broken-pod debug | "Debugging a dead pod: the kubectl commands that find the truth" |
| **6** | Terraform / Infrastructure as Code | Terraform modules for cluster infra; remote state, workspaces | "Terraform state done right: remote state, workspaces, reusable modules" |
| **7** | CI/CD + GitOps | GitHub Actions build/test → Argo CD deploys the app on merge | "GitOps from zero: GitHub Actions + Argo CD to Kubernetes" |
| **8** | Observability | Prometheus + Grafana on the cluster; dashboards, an alert, one SLO | "Build an observability stack from scratch: metrics, dashboards, SLOs" |
| **9** | Cloud fundamentals (AWS, one cloud deeply) | IAM, VPC, S3, and a managed cluster (EKS) — all via Terraform from Wk 6 | "AWS for engineers who already know Kubernetes" |
| **10** | Networking & security | TLS via cert-manager, secrets handling, an OPA policy, network basics | "Linux networking + TLS + policy: securing a cluster without the hand-waving" |
| **11** | The AI layer: serving + RAG on your infra | A FastAPI RAG endpoint (pgvector) on the cluster; model-serving literacy (vLLM) | "Putting an LLM on your own infrastructure: RAG + pgvector, honestly" |
| **12** | LLM evaluation + MLOps + capstone | An eval suite that **gates deployment** in CI + MLflow/DVC basics; capstone write-up | "The eval gate: refusing to ship an AI change that regresses" |

**Deliberately left out (anti-noise rule):** new JS frameworks, Web3, low-code, standalone "prompt engineering." If a resource isn't teaching a skill in this table, skip it.

---

## Progress

- [ ] Week 1 — Linux & Git
- [ ] Week 2 — Docker
- [ ] Week 3 — Docker Compose
- [ ] Week 4 — Kubernetes fundamentals
- [ ] Week 5 — Kubernetes in depth
- [ ] Week 6 — Terraform / IaC
- [ ] Week 7 — CI/CD + GitOps
- [ ] Week 8 — Observability
- [ ] Week 9 — Cloud fundamentals (AWS)
- [ ] Week 10 — Networking & security
- [ ] Week 11 — AI layer: serving + RAG
- [ ] Week 12 — LLM eval + MLOps + capstone

---

## Resources (the whole list — resist adding more)

- **The only thing you need:** the official docs of that week's tool. Official docs > tutorials > blogs. Resource-shopping is procrastination disguised as progress.
- **More will be added at the end of the marathon**

---

## Why build this skill?

Even if no job comes, this knowledge lets you run the infrastructure of a product AI built for you, before you hire anyone. In an AI-writes-the-code world the scarce thing is **judgment about systems** — what to deploy, how to tell if it's healthy, how to prove a change is safe before it ships. That's what Weeks 8, 10, and 12 build, and it's exactly what a model generating a Dockerfile can't commoditize. The repo is the portfolio *and* the operator's skill set.
