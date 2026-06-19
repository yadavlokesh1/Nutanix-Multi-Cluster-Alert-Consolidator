# Nutanix Multi-Cluster Alert Consolidator

If you manage Nutanix clusters, you know the drill — every morning someone has to log into Prism, check each cluster one by one, and figure out if anything broke overnight.

In environments with multiple clusters across different locations, this is slow and easy to miss things. Alerts come in separate emails per cluster, so there's no single place to see the full picture.

I built this script to fix that.

---

## What it does

Reads health data from multiple Nutanix clusters and generates one HTML report with everything in it:

- All critical alerts across every cluster
- All warning alerts
- DR status per cluster — OK or Rebuilding
- Any host where CPU or memory is above threshold

Open the report, 30 seconds, you know exactly what needs action today.

![Report Output](docs/sample-report.png)

---

## Why DR Status matters

When Nutanix shows **Rebuilding** — it means a disk or node recently failed and the cluster is re-protecting your data automatically. Nutanix handles it on its own, but it still needs to be tracked. If another failure happens during rebuild, you have a problem.

This is something most people miss when they skim through Prism quickly.

---

## Running it

```powershell
git clone https://github.com/yadavlokesh1/nutanix-multi-cluster-alert-consolidator.git
cd nutanix-multi-cluster-alert-consolidator
.\Get-NutanixReport.ps1
```

No installs. No modules. Just PowerShell — which is already on every Windows machine.

Report opens automatically in your browser when done.

---

## Thresholds

| Metric | Default Threshold |
|---|---|
| CPU Usage | Above 85% = flagged |
| Memory Usage | Above 80% = flagged |

Both are configurable at the top of the script.

---

## Currently uses mock data

The script runs against a `mock-data.json` file that mirrors the exact structure of Nutanix Prism Central REST API responses. This means you can test and see the report without needing access to a real cluster.

To connect to a live Prism Central, replace the JSON read with an API call to your Prism Central endpoint on port 9440.

---

## Files

```
├── Get-NutanixReport.ps1   — main script
├── mock-data.json          — sample data
├── sample-report.html      — example output
└── docs/
    └── sample-report.png   — screenshot
```

---

## Requirements

- Windows PowerShell 5.1 or later
- Network access to Prism Central on port 9440 (for live use)

---

Author
Lokesh Yadav — Infrastructure Specialist | Windows Server · VMware · Nutanix · Azure · AWS
