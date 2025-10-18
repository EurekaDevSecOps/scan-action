<div align="center" style="text-align:center;">

<p align="center">
  <img src="assets/radar.png" alt="Eureka Radar Logo" width="320"/>
</p>

# Multi-Scanner AppSec Action — Radar by Eureka ASPM

![GitHub release](https://img.shields.io/github/v/release/eurekadevsecops/scan-action?color=2b82f6\&label=Release)
![Build](https://github.com/eurekadevsecops/scan-action/actions/workflows/test.yml/badge.svg)
![License](https://img.shields.io/github/license/eurekadevsecops/scan-action?color=green)
![Node](https://img.shields.io/badge/Node.js-22.x-blue?logo=node.js)
[![Radar CLI](https://img.shields.io/badge/Radar%20CLI-%40eurekadevsecops%2Fradarctl-blueviolet)](https://github.com/EurekaDevSecOps/radarctl)

</div>

**Scan your code for security vulnerabilities with [Eureka Radar CLI](https://github.com/EurekaDevSecOps/radarctl)** — the unified AppSec orchestration tool that runs multiple security scanners and uploads aggregated results to GitHub Advanced Security or Eureka ASPM.

This GitHub Action lets you run Radar CLI directly in your CI/CD pipeline.
Detect secrets, dependency vulnerabilities, insecure code patterns, and configuration issues — all in one step.

---

**Telemetry defaults to OFF** — nothing is uploaded unless you provide credentials.
When enabled, [Eureka ASPM](https://eurekadevsecops.com) gives you full visibility across repositories, environments, and scanner types.

---

## 🌟 Key Features

* **Multi-scanner orchestration:** Run one or many scanners (Opengrep, Gitleaks, Grype, OWASP Dep-Scan, and more)
* **Single SARIF report:** Consolidated findings across all scanners in one place
* **Optional Eureka ASPM integration:** Upload results for de-duplication, triage, and prioritization
* **Telemetry control:** Off by default — opt-in when you want centralized visibility
* **GitHub Advanced Security (GHAS) support:** Upload findings to your repository’s Security tab

---

## How It Works

1. Checks out your repository
2. Installs Node.js and Radar CLI (`npm i -g @eurekadevsecops/radar`)
3. Runs selected scanners
4. Generates a unified SARIF report
5. Optionally uploads results to:

   * GitHub Advanced Security
   * Eureka ASPM (if `token` + `profile` are provided)

---

## Supported Scanners

| Category          | Scanners       | Description                                        |
| ----------------- | -------------- | -------------------------------------------------- |
| **SAST**          | Opengrep       | Detects insecure code patterns                     |
| **Secrets**       | Gitleaks       | Finds hardcoded credentials                        |
| **SCA**           | Grype, DepScan | Detects vulnerable dependencies                    |
| **Container/IaC** | (coming soon)  | Scans Dockerfiles, Terraform, Kubernetes manifests |

---

## 🛠️ Usage

### Example 1 — Local Scans

You can run this scan action entirely locally — it’s free, open source, and always will be. Use this option when you **don’t** want to upload findings to Eureka ASPM — ideal for forks, open-source, or isolated pipelines.

```yaml
name: Code Scan
on:
  push:
    branches: [ main ]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: eurekadevsecops/scan-action@v1
        with:
          scanners: "opengrep,gitleaks,grype"
```

🧘 **Telemetry:** Disabled by default. No data leaves your environment.

---

### Example 2 — Upload Findings to Eureka ASPM

Use this when you want to see all your scan results in one place inside [Eureka ASPM](https://eurekadevsecops.com). Uploading findings adds extra perks: a clean dashboard, easier tracking over time, and smarter deduplication across scanners. If you’re working on an open-source project, we have a free plan for you in Eureka ASPM.

```yaml
name: Code Scan and Upload
on:
  push:
    branches: [ main ]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: eurekadevsecops/scan-action@v1
        with:
          scanners: "opengrep,gitleaks,grype"
          token: ${{ secrets.EUREKA_AGENT_TOKEN }}
          profile: "prod-backend"
```

> 💡 **Tip:**
>
> * `EUREKA_AGENT_TOKEN` authenticates uploads to your Eureka workspace.
> * `EUREKA_PROFILE` tags findings with your app or environment.
> * If you omit both, no telemetry or uploads occur.

---

### Example 3 — Upload Findings to Eureka ASPM and to GitHub Advanced Security

Use this if you like seeing security alerts where you work — in GitHub — but also want a full picture in Eureka: one dashboard for all scanners, deduped results, and long-term tracking across all of your repos.

```yaml
name: Code Scan and Upload
on:
  push:
    branches: [ main ]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: eurekadevsecops/scan-action@v1
        with:
          scanners: "opengrep,gitleaks,grype"
          token: ${{ secrets.EUREKA_AGENT_TOKEN }}
          profile: "prod-backend"
          export_findings_to_ghas: "true"
```

> 💡 **Tip:**
>
> * `EUREKA_AGENT_TOKEN` authenticates uploads to your Eureka workspace.
> * `EUREKA_PROFILE` tags findings with your app or environment.
> * If you omit both, no telemetry or uploads occur.

---

## ⚙️ Inputs

| Name                      | Description                                                                   | Required? | Default                     | Example                               |
| ------------------------- | ----------------------------------------------------------------------------- | --------- | --------------------------- | ------------------------------------- |
| `folder_to_scan`          | Directory to scan (relative to repo root).                                    | Optional  | `${{ github.workspace }}`   | `"./src"`                             |
| `scanners`                | Comma-separated list of scanners to run (`"all"` runs all).                   | Optional  | `"opengrep,gitleaks,grype"` | `"opengrep,gitleaks"`                 |
| `export_findings_to_ghas` | Upload findings to GitHub Advanced Security. Requires GHAS enabled.           | Optional  | `"false"`                   | `"true"`                              |
| `token`                   | **EUREKA_AGENT_TOKEN** — optional. Enables uploads to Eureka ASPM.            | Optional  | `""`                        | `"${{ secrets.EUREKA_AGENT_TOKEN }}"` |
| `profile`                 | **EUREKA_PROFILE** — optional. Associates results with an app or environment. | Optional  | `""`                        | `"staging-api"`                       |

---

## 📤 Outputs

| Name     | Description                                                                                                        |
| -------- | ------------------------------------------------------------------------------------------------------------------ |
| `report` | Consolidated SARIF report containing findings from all scanners. Can be uploaded to GHAS or parsed by other tools. |

Example:

```yaml
- name: Run Eureka Radar CLI Scan
  id: radar
  uses: eurekadevsecops/scan-action@v1

- name: Print SARIF Report
  run: echo "${{ steps.radar.outputs.report }}"
```

---

## 🔐 Telemetry & Privacy

Telemetry is **off by default**.
Radar CLI does **not** send any data externally unless you explicitly provide:

* `EUREKA_AGENT_TOKEN`
* `EUREKA_PROFILE`

When provided:

* Findings are securely uploaded to Eureka ASPM
* You gain unified dashboards, trend analysis, and contextual prioritization

When omitted:

* Scans remain fully local — ideal for forks and isolated use

---

## Local Development

Run the same scan locally with Radar CLI:

```bash
npm i -g @eurekadevsecops/radar
radar scan -s "opengrep,gitleaks" .
```

---

## Troubleshooting

| Issue                                             | Possible Cause                                               | Solution                                                                                                            |
| ------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **❌ `report.sarif` not found**                    | The scan failed or didn’t produce output.                    | Check your workflow logs for errors from Radar CLI. Ensure the `folder_to_scan` path exists and scanners are valid. |
| **⚠️ No findings uploaded to Eureka**             | Missing or invalid `EUREKA_AGENT_TOKEN` or `EUREKA_PROFILE`. | Make sure your token is stored as a GitHub Secret and your profile matches one in Eureka ASPM.                      |
| **🚫 “Permission denied” when uploading to GHAS** | Repository doesn’t have GitHub Advanced Security enabled.    | Enable GHAS under your repo’s *Security → Code scanning alerts* settings.                                           |
| **Network errors**                            | CI environment lacks outbound internet access.               | Ensure the runner can reach npmjs.org and eurekadevsecops.com endpoints.                                            |
| **Invalid Node.js version**                    | Runner uses an incompatible Node version.                    | This action installs Node 22 automatically, but check logs to confirm setup-node succeeded.                         |
| **“radar not found”**                          | CLI installation failed.                                     | Re-run the workflow; check npm logs for permission issues or caching problems.                                      |

> 🧩 **Tip:**
> You can debug locally using the same CLI command as the action:
> `radar scan -s "opengrep,gitleaks,grype" -o report.sarif`

---

## Why Integrate with Eureka ASPM?

Eureka Application Security Posture Management (ASPM) helps you:

* **One Source of Truth:** Aggregate and de-duplicate findings across scanners — no more hunting through separate reports.
* **Less Noise, More Signal:** Prioritize risks contextually using code + dependency data. Map findings to **OWASP ASVS**.
* **Faster Fixes:** Clear ownership, risk context, and remediation guidance help teams resolve issues quickly.
* **Built for Dev Workflows:** Integrates with GitHub, CI/CD, and local development so security fits naturally into your flow.
* **Track and Improve:** See how your project’s security posture evolves over time — from first commit to production.

Learn more at **[eurekadevsecops.com](https://eurekadevsecops.com)**

---

## 🪪 License

This GitHub Action is licensed under the terms of the MIT License — © [Eureka DevSecOps](https://eurekadevsecops.com)

---

## 💬 Support

📫 [security@eurekadevsecops.com](mailto:security@eurekadevsecops.com)

💡 For issues and feature requests: [GitHub Issues](https://github.com/eurekadevsecops/scan-action/issues)
