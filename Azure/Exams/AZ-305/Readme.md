# AZ-305 Study Plan

I **just passed AZ-104**, so I want to use the momentum to start **AZ-305 (Azure Solutions Architect)**.

---

## What AZ-305 covers

* **Identity, Governance, Monitoring** – Who can do what, how I keep things compliant, and how I get alerted when something breaks.
* **Networking & Ingress** – Private connectivity, DNS, firewalls, App Gateway/Front Door, traffic control.
* **Compute** – Where my code runs (App Service, Functions, Container Apps, AKS, VMSS) and safe rollouts.
* **Data Storage** – Pick the right database/storage and the right redundancy (LRS/ZRS/GZRS/RA‑GZRS).
* **Security & Keys** – Secrets in Key Vault, Managed Identity everywhere, private access.
* **BCDR** – Backup/restore and disaster‑recovery (RPO/RTO and region pairs).
* **Cost & Well‑Architected** – Make trade‑offs across reliability, security, cost, operations, performance.

---

## My study folders (and why they exist)

```
az-305/
  diagrams/     # Pictures force clarity. I will draw every weekly design here.
  notes/        # Short summaries in my own words (3 bullets per topic max).
  dr/           # Disaster Recovery: RPO/RTO targets, failover steps, annual test plan.
  cost/         # Rough cost models, reservations vs. savings plans, storage tier notes.
  adr/          # ADRs: one‑page write‑ups of important choices (see below).
  practice/     # Logs of practice‑exam mistakes and the one‑line rule I learned.
```

### What is an ADR and why do I need it?

**ADR = Architecture Decision Record.** It’s a **single page** for **one important choice** I made while studying (e.g., *Private Endpoint vs Service Endpoint*). Writing an ADR forces me to explain:

* **Decision** – what I chose (1 sentence).
* **Context** – requirements/constraints (latency, compliance, cost, RPO/RTO).
* **Options considered** – A/B/C.
* **Consequences** – pros/cons, risks, follow‑ups.

I keep ADRs because AZ‑305 is about **trade‑offs**, not memorizing. If I can defend my choice on a page, I’m learning the architect mindset.

### Which decisions should I practice?

* Network access to PaaS: **Private Endpoint vs Service Endpoint**.
* Global entry: **Front Door vs App Gateway vs Traffic Manager**.
* Compute: **App Service vs Functions vs Container Apps vs AKS vs VMSS**.
* Data: **Cosmos vs Azure SQL/MI vs Postgres vs Storage**; and **consistency/HA** choices.
* Redundancy: **LRS/ZRS/GZRS/RA‑GZRS** (and when to pay for cross‑region).
* Identity: **Managed Identity + Key Vault** vs client secrets (don’t use secrets if I can avoid it).
* BCDR: **Azure Backup** scopes/retention vs **ASR** failover patterns; **DNS** cutover.
* Cost: **Reservations vs Savings Plans vs PAYG**; egress and storage tiering.

---

## Sources I’ll use

* **Microsoft Learn**: AZ‑305 learning paths (baseline reading + quizzes).
* **Azure Architecture Center & product docs**: the primary reference when I’m unsure.
* **John Savill’s “AZ‑305 Exam Cram”** (YouTube): fast, high‑yield review I’ll revisit during the loop.
* **Microsoft Exam Readiness Zone**: a fast recap.

---

## 8‑Week Plan + ongoing loop (checkbox table)

| Done | Week | Focus                           | What I produce (short & concrete)                                                                                                                                    | Core sources                                                                                           |
| :--: | :--: | ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| \[ ] |   0  | Setup                           | Repo with the folders above. Add an ADR template (`decisions/adr-0000-template.md`). Install Azure CLI/Bicep.                                                        | Read this plan; watch John Savill’s cram once at 1.5–2×.                                               |
| \[ ] |   1  | Identity + Governance + Monitor | **Role map** (who can do what), **management‑group/policy** layout, **alerting plan** (metric/log → action group → owner). 1–2 ADRs (e.g., *Policy: deny vs audit*). | Learn identity/gov modules; Docs: Azure AD RBAC/PIM, Policy/Blueprints; Savill: identity/gov sections. |
| \[ ] |   2  | Networking & Ingress            | **Hub‑and‑spoke diagram** with address spaces, NSGs/UDRs, Firewall, **Private Endpoints**, DNS zones; choose **Front Door** or **App Gateway** and write why (ADR).  | Learn infra modules; Arch Center hub‑spoke; Front Door/AppGW docs; Savill networking.                  |
| \[ ] |   3  | Compute                         | **Decision sheet**: App Service vs Functions vs Container Apps vs AKS vs VMSS + a **rollout plan** (slots/canary). 1 ADR on compute choice.                          | Learn compute parts; product docs; Savill compute.                                                     |
| \[ ] |   4  | Data Storage                    | **Data matrix** (SQL/MI, Cosmos, Postgres, Storage) with **RPO/RTO, throughput, consistency, cost**; pick redundancy (**LRS/ZRS/GZRS/RA‑GZRS**) and justify (ADR).   | Learn data path; SQL/Cosmos docs; Savill data.                                                         |
| \[ ] |   5  | Security & Keys                 | **Secrets plan** (Key Vault + rotation), **Managed Identity everywhere**, and **private access** for KV/Storage. ADR on *MSI+KV vs secrets*.                         | KV/MSI docs; Private Link docs; Savill security.                                                       |
| \[ ] |   6  | BCDR                            | **DR playbook**: per‑tier RPO/RTO, failover order, DNS change, backup policy (what, where, retention). ADR on *ASR vs backup‑only*.                                  | Learn BCDR path; Backup/ASR docs; Savill BCDR.                                                         |
| \[ ] |   7  | Cost + Well‑Architected         | **3‑year rough cost** (compute/storage, reservations/savings plans), plus a **WAF checklist** for my sample design. ADR on *Reservations vs Savings Plans*.          | Cost Mgmt/Advisor docs; WAF; Savill cost.                                                              |
| \[ ] |   8  | Synthesis + Mock                | **Mini design doc** for a pretend app using everything above. Timed practice test; log every miss in `practice/` with a one‑line rule.                               | Practice exam; Savill cram rewatch for weak areas.                                                     |
| \[ ] |  N+1 | Practice Loop (repeat)          | New practice test → bucket the misses → read the exact doc page → update ADR/diagram → do a tiny lab → retest in a few days.                                         | Practice exam provider; Docs/Arch Center; Savill cram for refresh.                                     |

---

## Plain‑English notes

* **RPO** = Recovery Point Objective, how much time’s worth of data I can afford to lose (e.g., 15 minutes).
* **RTO** = Recovery Time Objective, how long I can be down (e.g., 30 minutes).
* **Hub‑and‑spoke** = one central VNet with shared services (firewall/DNS) and separate app/data VNets connected to it.
* **Why draw (draw\.io)**: diagrams make gaps obvious (DNS, routes, subnets). I’ll save `.drawio` files in `diagrams/` and also export **PNG/SVG** for quick viewing.

---

## Tiny ADR template I’ll use

One file per decision (e.g., adr/0003-private-endpoint-vs-service-endpoint.md). Statuses: Proposed → Accepted → Superseded/Deprecated.

```
# ADR 0001: <Short title>
Status: Accepted | Proposed | Superseded
Date: <YYYY-MM-DD>
Decision: <1 sentence>
Context: <Requirements/constraints incl. RPO/RTO, cost, security, latency>
Options: <A>, <B>, <C>
Consequences: <Pros/cons, risks, follow-ups>
Links: <Docs, diagram files>
```

### Exit criteria (my personal “ready” checklist)

* I can draw hub‑spoke + Private Link + AppGW/Front Door + KV + data replicas **from memory**.
* For any feature, I can state **one killer use‑case** and **one trade‑off**.
* In practice tests I consistently hit my target score **with explanations**, not guesses.
