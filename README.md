# Nutanix-Prism-Report

# Nutanix Multi-Cluster Alert Consolidator

> Built to eliminate the manual morning health-check that every Nutanix L1 team does by logging into Prism one cluster at a time.

---

## The Problem

In enterprise environments running multiple Nutanix clusters, L1 monitoring teams start every shift by manually logging into each Prism Central instance — checking alerts, DR status, and resource thresholds one by one.

With 5-10 clusters across multiple datacenters, this takes 30-45 minutes of repetitive, error-prone work before the team can even begin responding to issues.

Nutanix does send alert emails — but each cluster sends them separately. There is no single consolidated view unless you are logged into Prism Central.

---

## The Solution

A PowerShell script that pulls health data across all Nutanix clusters and generates a single, consolidated HTML report — covering every critical alert, warning, DR status, and resource breach in one place.

The L1 team opens one report. In 30 seconds they know exactly what needs attention across the entire infrastructure.

![Sample Report Output](docs/sample-report.png)

---

## What the Report Shows

| Section | What it tells you |
|---|---|
| Critical Alerts | All P1 alerts across every cluster and host — with impact and resolution steps |
| Warning Alerts | All P2 alerts that need monitoring before they escalate |
| DR Status | Whether each cluster can survive a node failure right now |
| CPU & Memory Breaches | Every host exceeding threshold — flagged in red automatically |

**Thresholds:** CPU > 85% / Memory > 80% (configurable in script)

---

## Why This Matters in Production

- **DR Status = Rebuilding** means the cluster recently had a disk or node failure and is currently re-protecting data. This needs immediate attention even though Nutanix handles it automatically.
- **Critical alerts** like CVM RAM high or disk failure indicate hardware issues that Nutanix Support needs to be engaged on.
- **License expiry warnings** are easy to miss in daily operations — a missed license renewal can restrict cluster features overnight.

---

## How It Works

```
mock-data.json          (or live Prism Central API)
       ↓
Get-NutanixReport.ps1   reads data, applies thresholds
       ↓
NutanixAlertReport.html opens automatically in browser
```

Currently uses mock data that mirrors the exact JSON structure returned by the Nutanix Prism Central REST API v3. Switching to live data requires adding API credentials and replacing the JSON read with an `Invoke-RestMethod` call to your Prism Central endpoint.

---

## Quick Start

```powershell
# Clone the repo
git clone https://github.com/yadavlokesh1/nutanix-multi-cluster-alert-consolidator.git
cd nutanix-multi-cluster-alert-consolidator

# Run the script
.\Get-NutanixReport.ps1
```

Report generates and opens automatically in your default browser.

**Requirements:**
- Windows PowerShell 5.1+ (built into Windows — no installs needed)
- No external modules or dependencies

---

## Connecting to Live Prism Central

To use against a real environment, replace the mock data section in the script with:

```powershell
$Headers = @{
    Authorization = "Basic " + [Convert]::ToBase64String(
        [Text.Encoding]::ASCII.GetBytes("${Username}:${Password}"))
    "Content-Type" = "application/json"
}
$Data = Invoke-RestMethod -Uri "https://<PrismCentralIP>:9440/api/nutanix/v3/clusters/list" `
        -Method POST -Headers $Headers -Body '{"kind":"cluster"}' -SkipCertificateCheck
```

---

## Project Structure

```
nutanix-multi-cluster-alert-consolidator/
├── Get-NutanixReport.ps1     # Main script
├── mock-data.json            # Sample data matching Prism Central API response
├── sample-report.html        # Example HTML output
└── docs/
    └── sample-report.png     # Screenshot of report output
```

---

## Background

Built from hands-on experience managing Nutanix HCI infrastructure in enterprise environments. The alert consolidation gap is real — most teams solve it with manual checks. This script solves it with 200 lines of PowerShell and zero additional infrastructure.
