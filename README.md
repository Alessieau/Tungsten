<div align="center">

```
 _____ _   _ _   _  ____ ____ _____ _____ _   _ 
|_   _| | | | \ | |/ ___/ ___|_   _| ____| \ | |
  | | | | | |  \| | |  _\___ \ | | |  _| |  \| |
  | | | |_| | |\  | |_| |___) || | | |___| |\  |
  |_|  \___/|_| \_|\____|____/ |_| |_____|_| \_|
```

### High-Performance, Zero-Trust AWS FinOps Engine
*Scans gigabytes of AWS Cost & Usage Reports in milliseconds. Zero IAM credentials. Zero telemetry.*

[![Platform: Windows](https://img.shields.io/badge/Platform-Windows_x64-0078D6?style=flat-square&logo=windows)](https://www.trytungsten.dev)
[![Engine: Polars](https://img.shields.io/badge/Engine-Polars_Rust_Core-CD792C?style=flat-square)](https://pola.rs)
[![Security: Zero Egress](https://img.shields.io/badge/Security-Zero_Network_Egress-2ea44f?style=flat-square)](SECURITY.md)
[![License: Commercial](https://img.shields.io/badge/License-Commercial_%24999%2Fyr-blueviolet?style=flat-square)](https://www.trytungsten.dev)

[Website](https://www.trytungsten.dev) • [Detection Rules](rules.yaml) • [Security Model](SECURITY.md) • [Report an Issue](https://github.com/trytungsten/tungsten/issues)

---

</div>

## ⚡ The Problem with Traditional FinOps SaaS

Most cloud cost management platforms (Datadog, Vantage, CloudHealth) demand **read-access to your production AWS infrastructure** via cross-account IAM roles or billing export API integrations.

For security-conscious engineering teams, FinTechs, and healthcare organizations, this introduces:
1. **Third-party supply chain risk:** Giving an external startup access to metadata and billing telemetry.
2. **Exorbitant SaaS taxation:** Paying 2-5% of your total AWS bill or recurring monthly fees just to see where your money went.
3. **Sluggish web dashboards:** Navigating bloated SaaS UIs to find resources you could have deleted in a 5-second terminal command.

---

## 🛡️ The Tungsten Architecture: Local-First & Zero-Trust

**Tungsten** is an offline, standalone native CLI binary. You download your standard AWS **Cost and Usage Report (CUR)** (Parquet or CSV) directly from your S3 billing bucket, point Tungsten at it, and run your scan locally.

```
+---------------------+
| AWS S3 (CUR Export) |
+----------+----------+
           | (Downloaded directly to your laptop via AWS CLI)
           v
+-----------------------------------------------------------+
|                   LOCAL WORKSTATION                       |
|                                                           |
|   +-----------------------+     +---------------------+   |
|   | AWS CUR (Parquet/CSV) | --> | Tungsten CLI Engine |   |
|   +-----------------------+     +----------+----------+   |
|                                            |              |
|                     +----------------------+-------+      |
|                     v                              v      |
|          +--------------------+        +---------------+  |
|          | Interactive HTML/  |        | Non-Destructive|  |
|          | JSON Audit Reports |        | Shell Scripts |  |
|          +--------------------+        +---------------+  |
+-----------------------------------------------------------+
               [ STRICTLY 0 NETWORK PACKETS TRANSMITTED ]
```

- **Zero API Keys:** No `AWS_ACCESS_KEY_ID`, no cross-account IAM roles, no permissions granted to anyone.
- **Blazing Fast Polars Core:** Processes 10,000,000+ line items in under 2 seconds using multi-threaded SIMD memory layouts.
- **Human-In-The-Loop Remediation:** Generates safe, executable shell scripts (`remediation_script.sh` / `.ps1`) where all destructive CLI commands are commented out with `#` by default.

---

## 🔍 Transparent Detection Heuristics

Tungsten uses an open YAML specification for waste detection. You can inspect or extend the detection rules in [`rules.yaml`](rules.yaml):

| Leak ID | Resource Detected | Typical Monthly Leak | Remediated By |
| :--- | :--- | :--- | :--- |
| `ebs_unattached` | Unattached EBS volumes (`gp2`, `gp3`) | $10 - $200 / vol | `aws ec2 delete-volume` |
| `idle_eip` | Unassociated Elastic IP addresses | $3.60 / IP | `aws ec2 release-address` |
| `old_snapshots` | Retained historical EBS snapshots | $50 - $1,500+ / mo | `aws ec2 delete-snapshot` |
| `nat_orphaned` | Idle NAT Gateways in non-prod VPCs | $32.40 / mo / GW | `aws ec2 delete-nat-gateway` |

---

## 💻 Sample CLI Usage

```bash
# 1. Download your standard AWS CUR file locally
aws s3 cp s3://my-company-billing-bucket/cur/2026-11/cur.parquet ./cur.parquet

# 2. Run Tungsten local scan
tungsten --file ./cur.parquet --rules ./rules.yaml

# Output:
# [OK] Engine initialized (Polars 0.20+ SIMD core)
# [SCAN] Ingested 1,482,910 billing rows in 1.24s
# [FINDING] 42 Unattached EBS Volumes ($1,420.00/mo waste)
# [FINDING] 14 Idle Elastic IPs ($50.40/mo waste)
# [FINDING] 189 Obsolete Snapshots ($840.10/mo waste)
# ---------------------------------------------------------
# [TOTAL DETECTED LEAKS]: $2,310.50 / month ($27,726.00 / yr)
# [SAVED]: Scans/Scan_20261123/audit_report.html
# [SAVED]: Scans/Scan_20261123/remediation_script.sh
```

---

## 💼 Commercial Licensing & Guarantees

Tungsten is distributed as a single annual license (**$999/yr flat per organization**, unlimited scans across all accounts).

### 🤝 The $1,000 Waste-Find Guarantee
> If Tungsten does not uncover at least **$1,000 in immediate cloud waste** on your very first scan, we issue a prompt, 100% full refund within 14 days of purchase. No questions asked.

To purchase an annual commercial license or download the evaluation build, visit:
👉 **[https://www.trytungsten.dev](https://www.trytungsten.dev)**

---

## 🤝 Community & Support

- **Bug Reports & Feature Requests:** Please open an issue on the [Issue Tracker](https://github.com/trytungsten/tungsten/issues).
- **Custom Rules Submission:** Have an idea for a custom CUR leak detection rule? Submit a PR adding it to `rules.yaml`.
- **Direct Founder Contact:** Contact `alessio@trytungsten.dev` for enterprise licensing inquiries.
