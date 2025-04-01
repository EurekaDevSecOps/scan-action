# Eureka DevSecOps GitHub Action

The Eureka DevSecOps platform centralizes management of application and cloud security tools, aggregates and correlates the output from security scanners and manual sources into a single pane of glass for easy triage. 

The Eureka DevSecOps GitHub Action seamlessly integrates the Eureka DevSecOps platform into your GitHub workflows, enabling automated security scanning across your repositories.

By integrating this GitHub Action, you can run a customized suite of security scans on each repository to identify vulnerabilities, and efficiently manage security risks directly from the Eureka Portal.

## Requirements

The Eureka DevSecOps GitHub Action is currently only available to existing Eureka DevSecOps users.

If you are not an existing Eureka DevSecOps user and would like to learn more about Eureka 
DevSecOps to get started, visit the official [Eureka DevSecOps website](https://www.eurekadevsecops.com/).

## Usage

To use the Eureka DevSecOps GitHub Action, you must generate values for the required secrets and repository variables from the Eureka Portal listed below. Once generated, add each secret and repository variable name and its corresponding value to your repository’s GitHub Actions secrets under *Settings* > *Secrets and variables* > *Actions*.

Secrets:

- **EUREKA_ENDPOINT** 
- **EUREKA_USER**
- **EUREKA_PASSWORD**

Repository variables:

- **PROFILE_INFO**

For more detailed information on required secret variables and generating them from the Eureka Portal, see the [Eureka DevSecOps User Guide](https://eurekadocs.netlify.app/).

## Adding the Eureka DevSecOps Github Action to your GitHub workflow

To integrate the Eureka DevSecOps Github Action into your GitHub workflow, add the following YAML code to the `jobs` section of your existing workflow file:

```yml
  eureka-scan:
    runs-on: ubuntu-latest
    steps:
   - name: Checkout GitHub Action
     uses: actions/checkout@v4
  
      - name: Run Eureka Scan
        uses: FWDSEC/eureka-scan-action@main
        env:
          EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
          EUREKA_USER: ${{ secrets.EUREKA_USER }}
          EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
          PROFILE_INFO: ${{ vars.PROFILE_INFO }} 
```

## Input arguments

Below is a table of input arguments that can be optionally provided to the Eureka DevSecOps GitHub action in your workflow.

Specific scanners that are enabled may require API tokens or credentials passed as additional input arguments. Refer to the [Eureka DevSecOps User Guide](https://eurekadocs.netlify.app/) for more information on the required values to run these scanners.

| Input argument            | Description                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| `enable_pr_scan_report`   | A boolean to enable the Pull Request Scan Reports feature                                                    |
| `snyk_token`              | A Snyk API token is required if Snyk scanner is enabled                                                      |
| `sonarqube_user_token`    | A SonarQube user security token is required if SonarQube scanner is enabled.                                 |
| `veracode_api_key_id`     | A Veracode SAST/SCA/Pipeline API key ID is required if Veracode SCA, SAST or Pipeline scanner is enabled     |
| `veracode_api_key_secret` | A Veracode SAST/SCA/Pipeline API key secret is required if Veracode SCA, SAST or Pipeline scanner is enabled |
| `dast_username`           | A username credential for DAST scanners (Required if Zap scanner is enabled)                                 |
| `dast_password`           | A password credential for DAST scanners (Required if Zap scanner is enabled)                                 |

### Enabling Pull Request Scan Reports (Optional)

To generate a Eureka scan report as a comment on an associated pull request, include the `enable_pr_scan_report` input parameter set to `true`:

```yml
eureka-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Checkout GitHub Action
      uses: actions/checkout@v4
        
    - name: Run Eureka Scan
      uses: FWDSEC/eureka-scan-action@main
      env:
        EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
        EUREKA_USER: ${{ secrets.EUREKA_USER }}
        EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
        PROFILE_INFO: ${{ vars.PROFILE_INFO }}
      with:
        enable_pr_scan-report: true 
```

- `enable_pr_scan_report: true` enables the Eureka scan report as a comment on the pull request.

## Workflow Examples for scanner inputs

Below are workflow examples for scanners requiring additional inputs—only required if those scanners are enabled in your Eureka profile.

If multiple scanners that need additional inputs are enabled in your Eureka Profile, you can provide all required input arguments together within a single workflow.

<details>
  <summary>Eureka workflow with Snyk</summary>

```yml
eureka-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Checkout GitHub Action
      uses: actions/checkout@v4
        
    - name: Run Eureka Scan
      uses: FWDSEC/eureka-scan-action@main
      env:
        EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
        EUREKA_USER: ${{ secrets.EUREKA_USER }}
        EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
        PROFILE_INFO: ${{ vars.PROFILE_INFO }}
      with:
        snyk_token: ${{ secrets.SNYK_TOKEN }} 
```

Ensure that the Snyk API token is configured as a secret called `SNYK_TOKEN`.
</details>

<details>
  <summary>Eureka workflow with SonarQube</summary>

```yml
eureka-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Checkout GitHub Action
      uses: actions/checkout@v4
        
    - name: Run Eureka Scan
      uses: FWDSEC/eureka-scan-action@main
      env:
        EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
        EUREKA_USER: ${{ secrets.EUREKA_USER }}
        EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
        PROFILE_INFO: ${{ vars.PROFILE_INFO }}
      with:
        sonarqube_user_token: ${{ secrets.SONARQUBE_TOKEN }} 
```

Ensure that the SonarQube user security token is configured as a secret called `SONARQUBE_TOKEN`.
</details>

<details>
  <summary>Eureka workflow for Veracode SAST/SCA/Pipeline</summary>

```yml
eureka-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Checkout GitHub Action
      uses: actions/checkout@v4
        
    - name: Run Eureka Scan
      uses: FWDSEC/eureka-scan-action@main
      env:
        EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
        EUREKA_USER: ${{ secrets.EUREKA_USER }}
        EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
        PROFILE_INFO: ${{ vars.PROFILE_INFO }}
      with:
        veracode_api_key_id: ${{ secrets.VERACODE_API_KEY_ID }}
        veracode_api_key_secret: ${{ secrets.VERACODE_API_KEY_SECRET }}
```

Ensure that the Veracode API key ID is configured as a secret called `VERACODE_API_KEY_ID`.
Ensure that the Veracode API key secret is configured as a secret called `VERACODE_API_KEY_SECRET`.

</details>

<details>
  <summary>Eureka workflow for DAST scanners (Zap)</summary>

```yml
eureka-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Checkout GitHub Action
      uses: actions/checkout@v4
        
    - name: Run Eureka Scan
      uses: FWDSEC/eureka-scan-action@main
      env:
        EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
        EUREKA_USER: ${{ secrets.EUREKA_USER }}
        EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
        PROFILE_INFO: ${{ vars.PROFILE_INFO }}
      with:
        dast_username: ${{ secrets.DAST_USERNAME }}
        dast_password: ${{ secrets.DAST_PASSWORD }}
```

Ensure that the DAST username is configured as a secret called `DAST_USERNAME`.
Ensure that the DAST password is configured as a secret called `DAST_PASSWORD`.

</details>

### Example: Full Github Workflow with Eureka DevSecOps GitHub Action

If you're creating a new GitHub workflow, you can use the following complete example:

```yml
name: Full GitHub Workflow with Eureka DevSecOps Scan Action

on:
  push:
    branches:
      - "*"

jobs:
  eureka-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout GitHub Action
        uses: actions/checkout@v3

      - name: Run Eureka Scan
        uses: FWDSEC/eureka-scan-action@main
        env:
          EUREKA_ENDPOINT: ${{ secrets.EUREKA_ENDPOINT }}
          EUREKA_USER: ${{ secrets.EUREKA_USER }}
          EUREKA_PASSWORD: ${{ secrets.EUREKA_PASSWORD }}
          PROFILE_INFO: ${{ vars.PROFILE_INFO }}
```

Once you've added or updated your GitHub workflow, commit the changes to your repository.

Eureka DevSecOps can now scan your code using the configured scanners and settings from the Eureka system. Detected vulnerabilities will be reported to the Eureka system and can be viewed on the Eureka Portal.  

> Some scanners may require additional workflow steps for successful scanning. Consult the official [Eureka DevSecOps User Guide](https://eurekadocs.netlify.app/) for more detailed information.
