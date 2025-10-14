# Radar CLI - GitHub Action

The Eureka DevSecOps platform centralizes management of application and cloud security tools, aggregates and correlates the output from security scanners and manual sources into a single pane of glass for easy triage. 

The Eureka DevSecOps GitHub Action seamlessly integrates the Eureka DevSecOps platform into your GitHub workflows, enabling automated security scanning across your repositories.

By integrating this GitHub Action, you can run a customized suite of security scans on each repository to identify vulnerabilities, and efficiently manage security risks directly from the Eureka Portal.

## Usage

To use the Eureka DevSecOps GitHub Action, you must generate values for the required secrets and repository variables from the Eureka Portal listed below. Once generated, add each secret and repository variable name and its corresponding value to your repository’s GitHub Actions secrets under *Settings* > *Secrets and variables* > *Actions*.

Secrets:

- **EUREKA_AGENT_TOKEN**

Repository variables:

- **EUREKA_PROFILE**

For more detailed information on required secret variables and generating them from the Eureka Portal, see the [Eureka DevSecOps User Guide](https://docs.eurekadevsecops.com/).

## Adding the Eureka DevSecOps Github Action to your GitHub workflow

To integrate the Eureka DevSecOps Github Action into your GitHub workflow, add the following YAML code to the `jobs` section of your existing workflow file:

```yml
  eureka-radar-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Run Scan
        uses: EurekaDevSecOps/scan-action@main
```

## Input arguments

Below is a table of input arguments that can be optionally provided to the Eureka DevSecOps GitHub action in your workflow.

Specific scanners that are enabled may require API tokens or credentials passed as additional input arguments. Refer to the [Eureka DevSecOps User Guide](https://docs.eurekadevsecops.com/) for more information on the required values to run these scanners.

| Input argument            | Description                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| `folder_to_scan`          | (Optional) Target folder to scan. Default: github.workspace                                                  |
| `scanners`                | (Optional) List of scanners to run. Example: 'opengrep,gitleaks,grype,depscan'. Default: all                 |
| `export_findings_to_ghas` | (Optional) Upload findings to Github Advanced Security. Default: false                                       |
| `token`                   | (Optional) Eureka auth token, used to upload findings to the Eureka ASPM platform. Default: none             |
| `profile`                 | (Optional) Eureka profile ID, used to map findings to a profile in the Eureka ASPM platform. Default: none   |

### Example: Full Github Workflow with Eureka DevSecOps GitHub Action

If you're creating a new GitHub workflow, you can use the following complete example:

```yml
name: Full GitHub Workflow with Eureka DevSecOps Scan Action

on:
  push:
    branches:
      - "*"

jobs:
  eureka-radar-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Run Scan
        uses: EurekaDevSecOps/scan-action@main
        with:
          scanners: gitleaks,opengrep,grype
```

Once you've added or updated your GitHub workflow, commit the changes to your repository.

Eureka DevSecOps can now scan your code using the configured scanners and settings from the Eureka system. Detected vulnerabilities will be reported to the Eureka system and can be viewed on the Eureka Portal.  

> Some scanners may require additional workflow steps for successful scanning. Consult the official [Eureka DevSecOps User Guide](https://docs.eurekadevsecops.com/) for more detailed information.
