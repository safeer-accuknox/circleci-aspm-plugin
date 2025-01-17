### **AccuKnox CircleCI Plugin**

This CircleCI Plugin is built to run AccuKnox’s robust security scans, enabling teams to secure their applications and infrastructure effectively. It supports:

* **DAST** (Dynamic Application Security Testing)  
* **IaC** (Infrastructure as Code) Security Checks  
* **SAST** (Static Application Security Testing)  
* **Container Scans**

With this plugin, you can automate security testing and integrate it seamlessly into your CI/CD pipelines for improved DevSecOps practices.

### **Required Environment Variables**

The following environment variables are required for running AccuKnox scans in CircleCI pipelines:

#### **Common Environment Variables (Applicable to All Scans)**

| Variable | Description | Default Value |
| :---- | :---- | :---- |
| **ACCUKNOX\_TENANT** | The ID of the tenant associated with the CSPM panel. | N/A (Required) |
| **ACCUKNOX\_ENDPOINT** | The URL of the CSPM panel to push the scan results to. | N/A (Required) |
| **ACCUKNOX\_LABEL** | The label created in AccuKnox SaaS for associating scan results. | N/A (Required) |
| **ACCUKNOX\_TOKEN** | The token for authenticating with the CSPM panel. | N/A (Required) |

#### **SAST-Specific Environment Variables**

| Variable | Description | Default Value |
| :---- | :---- | :---- |
| **SONAR\_TOKEN** | Token for authenticating with SonarQube. | N/A (Required) |
| **SONAR\_HOST\_URL** | The SonarQube host URL. | N/A (Required) |
| **SONAR\_PROJECT\_KEY** | The project key in SonarQube. | N/A (Required) |

### **Container Scanning**

The container scanning section of the CI/CD pipeline is designed to integrate with AccuKnox to scan Docker images for security vulnerabilities.

Here’s the table that outlines the inputs and their descriptions, along with default values:

| Input | Description | Default Value |
| :---- | :---- | :---- |

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **DOCKERFILE** | The Dockerfile path relatively to build context. | `Dockerfile` |
| **BUILD\_CONTEXT** | The context of the Dockerfile to use for building the image | `"."` |
| **IMAGE\_NAME** | Docker image name |   |
| **TAG** | The tag for the Docker image. | `"$CIRCLE_SHA1"` |
| **SEVERITY** | Allows selection of severity level for the scan. Options include `UNKNOWN`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`. | `UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL` |
| **INPUT\_SOFT\_FAIL** | Do not return an error code if there are failed checks. | `true` |

#### **Example Pipeline Configuration**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.25
workflows:
  accuknox:
    jobs:
      - accuknox-scan/container:
          IMAGE_NAME: circleciplugin
```

### **Infrastructure as Code (IaC) scanning**

The Infrastructure as Code (IaC) scanning section of the CI/CD pipeline is designed to integrate with AccuKnox for scanning infrastructure code files (e.g., Terraform) for security vulnerabilities.

Here is the table that outlines the inputs and their descriptions, along with default values:

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **INPUT\_FILE** | Specify a file for scanning (e.g., “.tf” for Terraform). Cannot be used with directory input. | `""` (empty, optional) |
| **INPUT\_DIRECTORY** | Directory with infrastructure code and/or package manager files to scan | `"."` (current directory) |
| **INPUT\_COMPACT** | Do not display code blocks in the output | `true` (boolean) |
| **INPUT\_QUIET** | Display only failed checks | `true` (boolean) |
| **INPUT\_FRAMEWORK** | Run only on a specific infrastructure (e.g., Kubernetes or Terraform) | `""` (empty, optional) |
| **INPUT\_SOFT\_FAIL** | Do not return an error code if there are failed checks | `true` (boolean) |

#### **Example Pipeline Configuration**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.33
workflows:
  accuknox:
    jobs:
      - accuknox-scan/iac:
          INPUT_DIRECTORY: "."
          INPUT_COMPACT: true
          INPUT_QUIET: true
          INPUT_SOFT_FAIL: true
```

### **Dynamic Application Security Testing (DAST) Scanning**

The Dynamic Application Security Testing (DAST) scanning section of the  CI/CD pipeline integrates with AccuKnox for scanning live web applications for security vulnerabilities.

Here is the table that outlines the inputs and their descriptions, along with default values:

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **TARGET\_URL** | The URL of the web application to scan. | N/A (Required) |
| **SEVERITY\_THRESHOLD** | The minimum severity level (e.g., High, Medium, Low) that will cause the pipeline to fail if present in the report. | `High` |
| **DAST\_SCAN\_TYPE** | Type of ZAP scan to run: ‘baseline’ or ‘full-scan’. | `baseline` |
| **INPUT\_SOFT\_FAIL** | Do not return an error code if there are failed checks. | `true` (boolean) |

#### **Example**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.37
workflows:
  accuknox:
    jobs:
      - accuknox-scan/dast:
          INPUT_SOFT_FAIL: false
          TARGET_URL: "http://testhtml5.vulnweb.com"
```

This example shows a  CI configuration for a DAST scan job. It defines the necessary inputs such as the stage, target URL, severity threshold, scan type, and credentials for authentication. The results are generated and uploaded to the AccuKnox platform.

### **Static Application Security Testing (SAST) Scanning**

The **Static Application Security Testing (SAST)** scanning section of the  CI/CD pipeline integrates with **SonarQube** for analyzing code quality and security vulnerabilities. The scan results are then pushed to the AccuKnox platform for further analysis and tracking.

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **INPUT\_SOFT\_FAIL** | Do not return an error code if there are failed checks. | `true` (boolean) |

#### **Example**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.85
workflows:
  accuknox:
    jobs:
      - accuknox-scan/sast:
          INPUT_SOFT_FAIL: false
```

### Secret Scanning

The secret scanning section of the GitLab CI/CD pipeline is designed to integrate with AccuKnox to scan for hardcoded secrets and sensitive information in the git repositories.

Here's the table that outlines the inputs and their descriptions, along with default values:

| **Input Value**         | **Description**                                                                                     | **Default Value**                  |
| ------------------------ | --------------------------------------------------------------------------------------------------- | ---------------------------------- |
| **RESULTS**             | Specifies which type(s) of results to output: `verified`, `unknown`, `unverified`, `filtered_unverified`. Defaults to all types. | `""`                               |
| **BRANCH**              | The branch to scan. Use `all-branches` to scan all branches.                                        | `""`                               |
| **EXCLUDE_PATHS**       | Paths to exclude from the scan.                                                                     | `""`                               |
| **ADDITIONAL_ARGUMENTS**| Extra parameters for secret scanning.                                                               | `""`                               |
| **INPUT_SOFT_FAIL**     | Do not return an error code if secrets are found.                                                   | `true`                             |

#### Example

```yaml
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.101
workflows:
  accuknox:
    jobs:
      - accuknox-scan/secret:
          INPUT_SOFT_FAIL: false
```

This example defines a job for secret scanning and then uploads scan results to the AccuKnox platform