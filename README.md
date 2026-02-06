# Fuel AI — Secure Data Pipeline (GCP)
**In Progress | Infrastructure-First Design**

## Overview

This repository contains an **in-progress, production-oriented data ingestion pipeline** built on Google Cloud Platform (GCP). The primary goal of this project is to demonstrate **infrastructure design, security controls, debugging methodology, and documentation discipline** — not just a working “happy path” pipeline.

The pipeline is designed to automate batch data transfers from **Google Drive → Google Cloud Storage (GCS)** using **Cloud Run Jobs**, fully managed via **Terraform**.

This repo intentionally captures the *engineering thought process*: design tradeoffs, security boundaries, and real-world debugging challenges encountered while building cloud-native infrastructure.

---

## High-Level Architecture

**Core flow:**
1. Cloud Scheduler triggers a Cloud Run Job  
2. Cloud Run Job executes a containerized pipeline runner  
3. Pipeline securely authenticates to Google Drive  
4. Data is transferred to Google Cloud Storage  
5. Execution logs and results are emitted for observability and debugging

**Key services used:**
- Cloud Run (Jobs)
- Cloud Scheduler
- Google Cloud Storage
- IAM & Service Accounts
- Secret Manager
- Artifact Registry
- Terraform (Infrastructure as Code)

---

## Design Decisions

This project prioritizes **intentional architecture choices** over shortcuts.

### Cloud Run Jobs vs Cloud Run Services
- Chosen for **batch-oriented, finite execution**
- No always-on service or HTTP surface area
- Cleaner execution lifecycle and billing model

### Scheduler-Driven Execution
- Predictable, auditable execution timing
- Decouples pipeline logic from triggering mechanism
- Easier operational control compared to event-driven fanout

### Terraform-First Infrastructure
- All infrastructure defined declaratively
- Enables reproducibility, version control, and auditability
- Prevents configuration drift between environments

### Separation of Concerns
- Infrastructure provisioning (Terraform)
- Runtime logic (containerized pipeline)
- Secrets and configuration injected at runtime

---

## Security Model

Security is treated as a **first-class concern**, not an afterthought.

### Identity & Access Management
- Dedicated service accounts per workload
- Least-privilege IAM for:
  - Google Drive access
  - GCS write permissions
  - Secret Manager access

### Secrets Handling
- No credentials baked into container images
- OAuth and service account credentials injected securely at runtime
- Explicit handling of secret mount paths and permissions

### Platform Boundaries
- Clear separation between:
  - Developer access
  - Runtime service account permissions
  - Administrative controls

---

## Data Integrity & Corruption Prevention

This pipeline is designed to **detect and prevent silent data corruption** during ingestion, rather than relying solely on storage-layer guarantees.

### Integrity Strategy

Each pipeline run enforces **end-to-end integrity validation** across the data transfer lifecycle:

1. **Pre-upload validation**
   - Cryptographic checksums (SHA-256) are generated for each file at the source
   - File size and metadata are captured prior to transfer

2. **Verified upload**
   - Files are transferred to Google Cloud Storage using checksum-aware tooling
   - GCS-native checksums (CRC32C / MD5) are validated during write

3. **Post-upload verification**
   - Uploaded object metadata is inspected to confirm checksum consistency
   - Any mismatch results in a failed job and invalid pipeline run

4. **Manifest generation**
   - A per-run `manifest.json` is generated containing:
     - File name
     - File size
     - Cryptographic hash
     - Timestamp
   - The manifest is uploaded alongside the data and serves as a verifiable record of ingestion

### Failure Model

If **any integrity check fails**, the Cloud Run Job exits with a non-zero status.  
No downstream system should treat the dataset as valid unless the full pipeline run completes successfully.

### Why This Matters

This approach provides:
- Protection against network or transport corruption
- Detection of partial or truncated uploads
- Early warning of misconfigured tooling or runtime environments
- An auditable chain of custody for ingested data

The goal is not just to move data, but to **prove that the data arrived intact and unmodified**.

---

## Debugging & Reliability

A significant portion of this project involves **real-world debugging**, including:

- Tracing OAuth `invalid_grant` and authentication failures
- Debugging Cloud Run Job startup vs container runtime expectations
- Identifying mismatches between tool assumptions (e.g., rclone credential paths) and Cloud Run secret mounts
- Analyzing logs across:
  - Cloud Run execution metadata
  - Cloud Scheduler triggers
  - IAM permission errors
  - Container stdout/stderr

The repo intentionally documents these challenges to demonstrate **how issues are approached and resolved**, not just the final state.

---

## Repository Structure

```text
.
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── cloud_run.tf
│   ├── iam.tf
│   ├── scheduler.tf
│   └── outputs.tf
├── pipeline/
│   ├── Dockerfile
│   ├── entrypoint.sh
│   └── pipeline_runner/
├── docs/
│   ├── architecture.md
│   ├── design-decisions.md
│   └── debugging-notes.md
└── README.md



---




