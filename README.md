# devops-90 — master DevOps, Cloud & Platform-with-AI in 90 days, in public

**One concept a day. 30 minutes. Seven days a week.** The goal is real fluency in the stack that
runs modern software, and a working platform of your own to prove it.

This is built in the open, one day at a time. The notes here are the path I'm following, in
order. Each day produces **one visible artifact** (a commit) and **one sentence of proof** (a
post). If it isn't pushed and posted, the day didn't happen.

By Week 12 this repo isn't a pile of lessons — it's **one working, documented platform, built
layer by layer**: a FastAPI + Postgres app, containerized, running on Kubernetes, provisioned
with Terraform, shipped by GitOps, watched by Prometheus/Grafana, secured with TLS and policy,
serving a RAG endpoint, and gated by an LLM eval suite in CI. Walk the 90 days and you can build
and operate every layer of that yourself.

- **Cadence:** 30 min/day · 7 days/week · no skipped days · one green square per day
- **Format:** each day is a self-contained note you can read, run, and be tested on
- **Written by:** Etienne, working through it in public

---

## Who this is for

- You can use a terminal a little but want to **actually operate** real infrastructure.
- You're tired of tutorials that stop at "hello world" and never show the whole system.
- You want **judgment**, not just commands — what to deploy, how to tell it's healthy, how to
  prove a change is safe before it ships.

You don't need a CS degree or a DevOps job. You need 30 minutes a day.

---

## The daily ritual (same every day)

How each day is meant to be worked:

| Min | Phase | What you do |
|---|---|---|
| 0–5 | **Recall** | Without looking, re-type yesterday's key command(s) in a scratch terminal. Can't? That's today's first lookup. |
| 5–20 | **Learn + do** | Read *only* today's one concept, then immediately run it. Keep the note open; don't binge ahead. |
| 20–27 | **Commit** | Save the day's artifact, add a 1–2 line `LEARNING_LOG.md` entry, `git commit`, `git push`. |
| 27–30 | **Prove** | One sentence to X + LinkedIn: *what you did today + one thing that surprised you.* Link the commit. |

Daily post template (keep it to 2 minutes, same shape every day):

> Day N/90 — [topic]. Today I [did X]. Surprise: [one thing]. ↳ [repo link]

**Miss a day?** Don't double up tomorrow — that re-creates the overload that kills retention. Do
the missed day next, push it late, keep the chain. A late commit is still a commit.

---

## How the weeks work

Each week = **one theme, one small project that extends last week's, one article.** The days are
written as the series reaches them — never planning more than ~4 weeks ahead (that's a
focus/retention choice, not laziness).

- **Days 1–6:** one new concept each, hands on keyboard, one commit each.
- **Days 5–6:** re-type the week's core commands from memory until they flow without thinking.
- **Day 7 — consolidate + publish (the retention day):** no new topic. Re-type the week's
  commands from a blank terminal (your real gap list), then **write the week's article** by
  assembling the six days of notes and adding *why each thing matters*. Writing it up in your own
  words is what makes the week stick into the next.

Every day's note follows the same shape so you always know where you are: concept → commands at a
glance → details → real-world examples → hands-on exercise → cheat sheet → **a knowledge check you
can grade yourself on.**

---

## Repo layout

```
devops-90/
  README.md            <- this file, updated weekly as the architecture grows
  LEARNING_LOG.md      <- one dated line per day (the proof-of-work chain)
  week-01-linux-git/
    bin/               <- reusable scripts
    day1-note.md … day7-note.md
    README.md
  week-02-docker/
  week-03-compose/
  ...
  learn-platform/      <- the interactive platform these lessons are growing into
  articles/            <- markdown source of each weekly article (week-01.md …)
```

- **One commit per day, minimum.** Small is fine — a Dockerfile, a fixed command, a note. The
  chain matters more than the size.
- Real secrets stay out (`.gitignore` them); commit `*.example` files instead. (Security habit
  started early — see Week 10.)

---

## The 12-week spine — what you'll master

Each row is a week. Day-by-day notes get written as the series reaches that week.

| Wk | Theme | What you build (adds to this repo) | You'll be able to… |
|---|---|---|---|
| **1** | Linux & shell fluency + Git/GitHub | A `bin/` of small shell scripts + clean Git workflow + dotfiles | Move around any server and find the truth in a log, fast |
| **2** | Docker & containers | Containerize a FastAPI app into a lean, multi-stage image | Explain what an image really is and ship a small one |
| **3** | Docker Compose (multi-service) | FastAPI **+ Postgres** together via one `compose.yaml` | Run a real multi-service app stack locally |
| **4** | Kubernetes fundamentals | Deploy the same app to local K8s: pods, deployment, service | Get a real app running on a cluster |
| **5** | Kubernetes in depth | Helm chart, config/secrets, probes, ingress, a deliberate broken-pod debug | Debug a dead pod and find *why* |
| **6** | Terraform / Infrastructure as Code | Terraform modules for cluster infra; remote state, workspaces | Stand up infrastructure as code, not by hand |
| **7** | CI/CD + GitOps | GitHub Actions build/test → Argo CD deploys the app on merge | Ship on merge, automatically and safely |
| **8** | Observability | Prometheus + Grafana on the cluster; dashboards, an alert, one SLO | Tell whether a system is actually healthy |
| **9** | Cloud fundamentals (AWS, one cloud deeply) | IAM, VPC, S3, and a managed cluster (EKS) — all via Terraform from Wk 6 | Operate real cloud infrastructure |
| **10** | Networking & security | TLS via cert-manager, secrets handling, an OPA policy, network basics | Secure a cluster without the hand-waving |
| **11** | The AI layer: serving + RAG on your infra | A FastAPI RAG endpoint (pgvector) on the cluster; model-serving literacy (vLLM) | Put an LLM on infrastructure you control |
| **12** | LLM evaluation + MLOps + capstone | An eval suite that **gates deployment** in CI + MLflow/DVC basics; capstone write-up | Refuse to ship an AI change that regresses |

**Deliberately left out (anti-noise rule):** new JS frameworks, Web3, low-code, standalone
"prompt engineering." If a resource isn't teaching a skill in this table, skip it.

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

- **The only thing you need:** the official docs of that week's tool. Official docs > tutorials >
  blogs. Resource-shopping is procrastination disguised as progress.
- More will be added at the end of the marathon.

---

## Why master this

In a world where AI writes the code, the scarce thing is **judgment about systems** — what to
deploy, how to tell if it's healthy, how to prove a change is safe before it ships. That's what
Weeks 8, 10, and 12 build, and it's exactly what a model generating a Dockerfile can't
commoditize. Even if you never take a DevOps job, this skill lets you run the infrastructure of a
product AI built for you — before you hire anyone. The repo becomes the portfolio *and* the
operator's skill set.

Start at [Day 1](week-01-linux-git/day1-note.md).
