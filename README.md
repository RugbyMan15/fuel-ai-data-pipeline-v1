# 🚀 Fuel AI Data Pipeline — Full Rebuild (Phase 1 → 4)

### Author
**Carrim-Jamal Browne**  
Cloud Infrastructure Engineer @ Fuel AI  
[LinkedIn](https://linkedin.com/in/carrim-browne) • [GitHub](https://github.com/cjbrowne18)

---

## 📘 Overview
This repository documents the full rebuild of Fuel AI’s dataset-transfer pipeline — from initial manual scripts to a scalable, monitored, and policy-compliant automation framework.

The pipeline moves data from **Google Drive (uploads)** → **Google Cloud Storage (customer buckets)**, reducing manual effort, timeouts, and operational risk.

---

## 🧭 Project Phases

| Phase | Focus | Status | Key Skills / Tools |
|:------|:------|:------:|:------------------|
| **1. Foundation** | Script-based Drive → GCS transfer | ✅ Complete | rclone • gsutil • Bash • IAM • Logging |
| **2. Automation** | Schedule & trigger orchestration | 🟡 In Progress | Cloud Run • Scheduler • Pub/Sub |
| **3. Observability** | Metrics, alerts & dashboards | 🔜 Planned | Cloud Monitoring (MQL) • Alerts • Dashboards |
| **4. Hardening & Compliance** | Security, cost, & compliance optimization | 🔜 Planned | NIST SP 800-53 • CIS Controls • Cost Insights |

---

## 🧩 Architecture Evolution

### Phase 1 — Script Foundation
Google Drive (Source)
↓ (rclone)
Staging Bucket (GCS)
↓ (gsutil -m cp)
Customer Bucket (Target)
↓
Logs + Manifests (local)

- Modular bash scripts (each does one thing well)  
- Automatic folder creation & resumable transfers  
- Logging + manifest tracking for transparency  

### Phase 2 — Automation Layer
Drive Watcher / Cloud Scheduler
↓ (trigger)
Cloud Run Container → Transfer Workflow
↓
Pub/Sub messages for each dataset
↓
Run Phase 1 scripts inside managed jobs

- Fully automated “push-button once” operation  
- Configurable scheduling per dataset  
- Audit logs & standardized exit codes  

### Phase 3 — Observability & Alerting
- Logs exported to GCS and Cloud Logging  
- Custom metrics (e.g., `transfer_success_count`, `transfer_error_count`)  
- Alert policies: failed runs, high egress, missing datasets  
- Daily status digest (Cloud Function + Scheduler)  

### Phase 4 — Security & Compliance Hardening
- Enforce UBLA + PAP on buckets  
- Service Account least-privilege model  
- Secret rotation + encryption via KMS  
- Compliance mapping (NIST, CIS, GDPR alignment)  
- Cost optimization via lifecycle policies + alerts  

---

## 🗂 Repository Structure
fuel-ai-data-pipeline/
├── README.md
├── scripts/
│ ├── create_dest.sh
│ ├── drive_to_gcs.sh
│ ├── gcs_to_target.sh
│ └── run_dataset.sh
├── automation/
│ ├── scheduler_job.yaml
│ ├── run_container.sh
│ └── pubsub_trigger.py
├── monitoring/
│ ├── alert_policies/
│ ├── dashboards/
│ └── logs_based_metrics/
├── security/
│ ├── bucket_policies.yaml
│ └── iam_roles.tf
├── design/
│ ├── architecture_diagram.png
│ ├── audits_summary.md
│ └── phase1_outline.pdf
├── manifests/
├── logs/
└── CHANGELOG.md


---




