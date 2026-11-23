# Security Policy & Zero-Trust Architecture

At **Tungsten**, security and zero-trust engineering are our fundamental design principles.

Most modern FinOps platforms require administrative read-access (IAM Roles, Cross-Account Trusts, ReadOnlyAccess policies) to scan cloud infrastructure. This introduces third-party supply chain risk and data governance violations.

Tungsten was architected specifically to eliminate this attack vector.

---

## 1. Zero Network Egress Policy

- **No Outbound Network Calls:** The Tungsten core engine performs strictly 0 network requests during billing analysis.
- **Local-Only Execution:** All data analysis (AWS Cost and Usage Reports in Parquet or CSV format) is executed entirely in-memory on your local workstation.
- **Offline Cryptographic Licensing:** License validation uses local HMAC-SHA256 signatures with public salt verifications. The binary does not phone home to any licensing server.

---

## 2. Air-Gapped Verification

You can run Tungsten inside an air-gapped machine, a sandboxed container, or an isolated developer workstation with Wi-Fi / Ethernet physically disconnected. The software runs with full functionality without Internet access.

You can audit Tungsten's network behavior at any time using standard network monitoring tools:
```powershell
# Monitor active connections on Windows
Get-NetTCPConnection | Where-Object { $_.OwningProcess -eq (Get-Process Tungsten).Id }
```
Or using Sysinternals `TCPView` / `Wireshark`. You will observe zero outbound packets.

---

## 3. Safe Remediation Scripts

Tungsten **never** automatically executes destructive cloud commands against your AWS accounts.
- The engine generates non-destructive audit reports (`.html` and `.json`).
- It generates shell remediation scripts (`remediation_script.sh` / `remediation_script.ps1`) where all decommissioning commands (`aws ec2 delete-volume`, `aws ec2 release-address`) are commented out by default with `#`.
- Your DevOps or SRE team maintains 100% human-in-the-loop audit control before any action is taken.

---

## 4. Reporting Security Vulnerabilities

If you discover any security consideration or anomaly, please report it directly by opening an issue or a GitHub Security Advisory in this repository.
