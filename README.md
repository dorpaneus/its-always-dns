# 🚀 It's Always DNS: 21-Day Senior SRE Interview Prep (Google / GCP Edition)

> [!NOTE]
> **Target Roles:** Senior Site Reliability Engineer (SRE) / Systems Engineer (Daily Commitment ~3 hours every day)

This repository contains everything required to pass a heavy infrastructure, distributed systems, and behavioral interview loop at companies like Google. It is designed to bridge the gap between "I can fix a broken Linux server" and "I can design, observe, and recover a highly available, multi-region distributed system while maintaining an error budget."

---

## ⏱️ Daily Structure (~3 hours)

| Block | Focus |
| :--- | :--- |
| **🌅 Morning** | **Concepts + Reading.** New material lands here while you're fresh (e.g., Google SRE Book chapters). |
| **💻 Midday** | **Hands-on Labs / Whiteboarding.** Run commands, write IaC, design architectures (e.g., NALSD - Non-Abstract Large System Design). |
| **🎯 Afternoon** | **Drills + Behavioral.** Past-question repetition, aligning stories to GCA, RRK, Leadership, and Googliness. |

> [!TIP]
> **Standard Topic Flow:** 
> Context → Mental model → Tool walkthrough → Hands-on exercises → Self-check questions (with folded answers).

---

## 🗓️ Week 1 — The Bare Metal (SysEng & OS Internals)
*Proving you have the technical chops to debug when the abstractions break.*

| Day | Topic | GCP / SRE Context | Interview Dimension |
| :--- | :--- | :--- | :--- |
| 1 | [Linux fundamentals - files, permissions, hard/symlinks](21-days/day-01-linux-fundamentals.md) | Cloud IAM, GCS objects vs POSIX | **RRK: System Internals** |
| 2 | [Processes & the kernel - `ps`, signals, namespaces & cgroups](21-days/day-02-processes-namespaces.md) | Borg limits, GKE under the hood | **GCA: Complex Troubleshooting** |
| 3 | [Filesystems — mount, LVM recovery, LUKS, iSCSI](21-days/day-03-filesystems-lvm.md) | Persistent Disks (PD), Filestore | **Googliness: Navigating Ambiguity** |
| 4 | [Boot & systemd — GRUB, recovery, kernel modules, `dmesg`](21-days/day-04-boot-systemd.md) | Serial Console, Custom Images | **Leadership: Bias for Reliability** |
| 5 | [Networking I — TCP/IP, DNS, DHCP, "loading a website"](21-days/day-05-networking-tcp-dns.md) | Cloud DNS, VPC Subnets | **GCA: First Principles Thinking** |
| 6 | [Networking II — `tcpdump`, `ss`, `mtr`, firewalls, NAT](21-days/day-06-networking-firewalls.md) | VPC Firewalls, Cloud NAT | **RRK: Network Debugging** |
| 7 | [Host Resource Tuning — CPU (CFS), Memory (OOM, NUMA), Disk I/O](21-days/day-07-host-tuning.md) | GCE sizing, USE method | **GCA: Data-driven Tuning** |

## 🗓️ Week 2 — The Platform (Distributed Systems & Scale)
*Proving you can operate at Google scale, moving from a single server to a resilient global environment.*

| Day | Topic | GCP / SRE Context | Interview Dimension |
| :--- | :--- | :--- | :--- |
| 8 | [Observability (O11y) — SLIs, SLOs, SLAs, Error Budgets](21-days/day-08-observability-slos.md) | Cloud Monitoring, Monarch, Prometheus | **Googliness: Pushing back on Product** |
| 9 | [App Debugging & Tracing — `strace`, eBPF, Distributed Tracing](21-days/day-09-tracing-ebpf.md) | Cloud Trace, OpenTelemetry, Dapper | **RRK: Deep Dive into Code** |
| 10 | [System Design I — Load Balancing, Caching, DNS Routing](21-days/day-10-sysdesign-lb-cache.md) | GFE (Google Front End), Memorystore | **GCA: NALSD & Scale** |
| 11 | [System Design II — Consensus, State, & Blast Radius](21-days/day-11-sysdesign-ha-blast-radius.md) | Spanner, Cap Theorem, Global vs Regional | **GCA: Trade-offs in Architecture** |
| 12 | [Infrastructure as Code (IaC) — Terraform state, Drift, Blast radius](21-days/day-12-iac-terraform.md) | Terraform, Config Connector | **Leadership: Enforcing Standards** |
| 13 | [Release Engineering — Blue/Green, Canary, Rollbacks, Toil Reduction](21-days/day-13-release-engineering.md) | Cloud Build, Spinnaker, Safe Rollouts | **RRK: Automation over Toil** |
| 14 | [Containers & Orchestration — K8s architecture, Pods, CNI, Service Mesh](21-days/day-14-k8s-containers.md) | GKE, Borg, Istio (Anthos) | **Googliness: Continuous Learning** |

## 🗓️ Week 3 — The Operator (Incidents, Security, Behavioral)
*Proving your operational maturity, judgment, and cultural fit.*

| Day | Topic | GCP / SRE Context | Interview Dimension |
| :--- | :--- | :--- | :--- |
| 15 | [Security & Identity — IAM, Roles, Policies, Zero Trust, SSO](21-days/day-15-security-iam.md) | BeyondCorp, Workload Identity, Org Policies | **Leadership: Influencing properly** |
| 16 | [Incident Management — IMOC, Mitigation vs Resolution, Load Shedding](21-days/day-16-incident-command.md) | Incident Response, Cascading Failures | **GCA: Working Under Pressure** |
| 17 | [Postmortems & COEs — Blameless culture, 5 Whys, Action Items](21-days/day-17-postmortems-coe.md) | Google Blameless Postmortems | **Googliness: Blameless Culture** |
| 18 | [Scripting & Automation under pressure — Python/Go, Idempotency](21-days/day-18-scripting-automation.md) | Cloud Functions, Eliminating Toil | **RRK: Coding / Scripting** |
| 19 | [Behavioral Intensive — Mentoring, Cross-team, Dealing with Ambiguity](21-days/day-19-behavioral-mentoring.md) | Performance evaluation, Growing others | **Leadership: Mentorship** |
| 20 | [Mock Loop Day — Timed NALSD whiteboarding, live debugging, behavioral](21-days/day-20-mock-loop.md) | Full Simulation | *(All Dimensions)* |
| 21 | [Light review + rest — drills, no new material, sleep early](21-days/day-21-rest-review.md) | Final prep | *(Final mock + rest)* |

---

## 📂 Repository Layout

```text
.
├── README.md                # this file
├── LICENSE
└── 21-days/
    ├── day-01-linux-fundamentals.md
    ├── day-02-processes-namespaces.md
    └── ... (Days 3 through 21)
```

---

## 📚 Story Bank Target — Google Interview Dimensions

Unlike Amazon's 16 LPs, Google evaluates candidates on four main dimensions. By Day 19, you should have **~12–15 stories** tagged to these areas, ready in STAR format. 

> [!WARNING]
> **The Four Dimensions for Google SRE:**
> Focus your strongest, most detailed technical stories here.
> 
> * **General Cognitive Ability (GCA):** How you solve hard problems, navigate ambiguity, learn new things, and weigh engineering trade-offs (e.g., consistency vs. availability).
> * **Role-Related Knowledge (RRK):** Your depth in Linux internals, networking (TCP/IP, BGP), coding/scripting (Python, Go, Bash), and large-scale system design.
> * **Leadership:** How you step up during an incident (IMOC), drive a blameless postmortem, mentor juniors, and influence teams without authority to adopt reliable practices.
> * **Googliness:** Working effectively in teams, valuing user feedback, maintaining a blameless culture, doing the right thing, and handling pushback gracefully (e.g., freezing feature releases when error budgets run out).

---

## ✅ Pre-loop Checklist (Day 21 Review)

### Architecture & SRE (NALSD Focus)
- [ ] Can clearly define the difference between an SLI, SLO, and SLA, and know how to calculate an Error Budget.
- [ ] Can explain how to prevent cascading failures using "Load Shedding", "Exponential Backoff & Jitter", and "Circuit Breakers".
- [ ] "How would you design a highly available photo storage service?" practiced end-to-end (DNS down to the distributed DB tier like Spanner).
- [ ] Understand the tradeoffs between CAP theorem constraints and can debate consistency vs. latency.

### Systems, Linux, & Code
- [ ] "What happens when I load a website?" walked end-to-end without notes (DNS, TCP handshake, TLS, HTTP, GFE/LB, Backend, DB).
- [ ] Brendan Gregg's **USE method** (Utilization, Saturation, Errors) opener memorized.
- [ ] Grasp of kernel-level isolation (Namespaces, cgroups) and how they power systems like Borg and Kubernetes.
- [ ] Live-coding data-parsing or API interaction script rehearsed (Python or Go) with clean, modular functions and error handling. (SREs code!)

### Behavioral & Operational
- [ ] Story bank covering GCA, RRK, Leadership, and Googliness, each rehearsed out loud.
- [ ] At least one **massive incident story** walked through end-to-end (timeline, IMOC role, mitigation, root cause, blameless postmortem).
- [ ] One **"pushed back on product/devs due to reliability concerns"** story.
- [ ] One **"eliminated a significant source of toil through automation"** story.
- [ ] 2–3 thoughtful questions prepared for *each* interviewer regarding their specific scale or reliability challenges.
