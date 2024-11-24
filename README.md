# Security Scans with GitHub Actions

This repository integrates several security scans into the CI/CD pipeline using GitHub Actions. The following security tests are performed:

1. **SCA (Software Composition Analysis)** with Snyk
2. **SAST (Static Application Security Testing)** with CodeQL
3. **DAST (Dynamic Application Security Testing)** with OWASP ZAP
4. **Container Security Scan** with Trivy

Each of these scans plays a crucial role in identifying security vulnerabilities across different layers of the application stack. The results are uploaded as artifacts and can be pushed to SonarCloud for further analysis.

---

## Security Scans Overview

### 1. **SCA (Software Composition Analysis) with Snyk**

**Purpose**:  
Snyk helps in identifying vulnerabilities in third-party dependencies (e.g., npm packages for JavaScript). This scan checks for vulnerabilities in the open-source packages used in the project, and it provides an actionable report with suggestions for fixes.

**Steps**:
- Checkout the repository.
- Set up Node.js environment.
- Install project dependencies (`npm install`).
- Run Snyk’s vulnerability scan with `snyk test`.
- Upload the results as an artifact for further inspection.

**Keys Needed**:
- `SNYK_TOKEN`: A token for authenticating with the Snyk service. This can be created from your [Snyk account](https://snyk.io/).

---

### 2. **SAST (Static Application Security Testing) with CodeQL**

**Purpose**:  
Static code analysis with CodeQL checks the codebase for common security issues, coding flaws, and vulnerabilities without executing the code. This analysis helps to detect potential issues like SQL injection, cross-site scripting (XSS), and more.

**Steps**:
- Checkout the repository.
- Initialize the CodeQL setup.
- Build the project automatically using CodeQL’s autobuild feature.
- Run CodeQL analysis and output the results as a JSON report.
- Upload the JSON report as an artifact.

**Keys Needed**:
- `GITHUB_TOKEN`: A GitHub token is required for the CodeQL integration to authenticate with GitHub’s services.

---

### 3. **DAST (Dynamic Application Security Testing) with OWASP ZAP**

**Purpose**:  
DAST testing simulates real-world attacks on the application while it is running to detect vulnerabilities that could be exploited in a production environment (e.g., XSS, SQL injection, etc.). OWASP ZAP is used for scanning the live application.

**Steps**:
- Checkout the repository.
- Install dependencies (`npm install`).
- Start the application.
- Optionally, expose the application via ngrok for external access.
- Run OWASP ZAP to perform the dynamic scan.
- Upload the ZAP scan results as an HTML report for further inspection.

**Keys Needed**:
- `GITHUB_TOKEN`: A GitHub token for authenticating with GitHub actions.
- `NGROK_TOKEN`: If using ngrok to expose your local app to the public, you’ll need a [ngrok token](https://ngrok.com/) for this feature.

---

### 4. **Container Security Scan with Trivy**

**Purpose**:  
Trivy is used to scan Docker images for vulnerabilities in the operating system and the application libraries. It checks for issues such as outdated or vulnerable packages within the container.

**Steps**:
- Checkout the repository.
- Build the Docker image.
- Scan the image with Trivy for known vulnerabilities.
- Upload the Trivy scan results in JSON format.

**Keys Needed**:
- `DOCKER_TOKEN`: This token may be required depending on the Docker registry being used. For public images, this may not be necessary.

---

## GitHub Actions Workflow

These security scans are integrated into the GitHub Actions CI/CD pipeline. Upon pushing to the `main` branch or opening a pull request, the following security scans will run:

1. **SCA with Snyk**: Identifies vulnerabilities in dependencies.
2. **SAST with CodeQL**: Analyzes source code for security flaws.
3. **DAST with OWASP ZAP**: Performs a live security scan of the application.
4. **Container Security Scan with Trivy**: Scans the container image for vulnerabilities.

After running all the scans, the results are uploaded as artifacts. These artifacts can be accessed for review, and can also be pushed to SonarCloud for centralized reporting.

---

## Configuration Secrets

For the security scans to function correctly, you’ll need to set up the following secrets in your GitHub repository:

### 1. **SNYK_TOKEN**  
- **Description**: Authentication token for Snyk.  
- **How to Get It**: Create a token by signing up at [Snyk](https://snyk.io/) and navigating to the account settings to generate a token.  
- **Set in GitHub**: Go to the repository's **Settings** > **Secrets** > **New repository secret**.

### 2. **GITHUB_TOKEN**  
- **Description**: GitHub token used for interacting with GitHub services (e.g., CodeQL analysis).  
- **How to Get It**: This token is automatically provided by GitHub for each action run, so you don't need to manually create it.  
- **Set in GitHub**: This token is automatically set in the environment during each GitHub Actions run.

### 3. **NGROK_TOKEN** (optional)  
- **Description**: Required if you are using ngrok to expose your local application to the public internet for OWASP ZAP testing.  
- **How to Get It**: Create an ngrok account and generate a token from [ngrok’s dashboard](https://dashboard.ngrok.com/get-started).  
- **Set in GitHub**: Add this token as a secret in your GitHub repository.

### 4. **DOCKER_TOKEN** (optional)  
- **Description**: Required for accessing private Docker registries if you’re using private images.  
- **How to Get It**: This token can be obtained from Docker Hub or your Docker registry service provider.  
- **Set in GitHub**: Add this token as a secret in your GitHub repository.

---

## SonarCloud Integration

After all the security scans have completed, the results are uploaded as artifacts and can also be sent to [SonarCloud](https://sonarcloud.io/). To configure SonarCloud, you need the following:

1. **SONAR_TOKEN**: A token for authenticating with SonarCloud.  
2. **SONAR_PROJECT_KEY**: The unique key for your project on SonarCloud.  
3. **SONAR_ORG**: The name of your organization on SonarCloud.

**How to Set Up SonarCloud**:
- Sign up for SonarCloud at [sonarcloud.io](https://sonarcloud.io/).
- Link your GitHub account and select the repository to analyze.
- In the SonarCloud project settings, generate a `SONAR_TOKEN` and get the `SONAR_PROJECT_KEY` and `SONAR_ORG`.

---

## Conclusion

By integrating these security scans into your CI/CD pipeline, you ensure that your code is continuously tested for security vulnerabilities at multiple levels. Regular scans help maintain the integrity and safety of your application by catching security issues early in the development cycle. 

