# Create a Jenkins CI/CD Pipeline for a React Web Application using DevSecOps Practices

## 🛠️ 1. Prerequisites
Before creating the Jenkins pipeline, make sure the following are available and configured:

- [ ] **Jenkins Server** (Installed and running with administrator access)
- [ ] **Git** (Installed on both your local machine and the Jenkins server)
- [ ] **GitHub Repository** (Containing the React application source code)
- [ ] **Node.js / npm** (Environment setup for building the React app)
- [ ] **Netlify Account & Site** (For deployment targets)
- [ ] **Snyk Account & API Token** (For security vulnerability scanning)

### 📦 Example Application Repository
* **Repository URL:** `https://github.com/Veverita-Engineering/Customer-Portal`
* *Note: The repository used in this lab contains the React web application that Jenkins builds, tests, scans, and deploys.*

---

## 🧩 2. Jenkins Plugins Required
Navigate to: **Jenkins** ➡️ **Manage Jenkins** ➡️ **Plugins** ➡️ **Available plugins**. 

Install the following plugins if they are not already installed (restart Jenkins after installation if prompted):

| Plugin Name | Purpose |
| :--- | :--- |
| **Pipeline** | Create and execute Jenkins Pipelines |
| **Git** | Checkout source code from Git repositories |
| **NodeJS** | Configure and use Node.js installations |
| **Credentials Binding** | Access credentials securely from pipelines |
| **Credentials** | Store secrets and authentication information |
| **Snyk Security** | Integrate Snyk security scanning |
| **JUnit** | Publish JUnit test reports |

---

## 🟢 3. Configure Node.js in Jenkins
The pipeline uses the Jenkins NodeJS plugin to provide a consistent Node.js environment.

### Configuration Steps:
1. Navigate to: **Jenkins** ➡️ **Manage Jenkins** ➡️ **Tools**
2. Scroll down to find **NodeJS installations**
3. Click **Add NodeJS**
4. Configure the settings exactly as follows:
   * **Name:** `node-lts`
   * **Install automatically:**  Checked / Enabled ✅
   * **Version:** Select an appropriate **LTS version**

> ⚠️ **Crucial Alignment:** The installation name configured in Jenkins must exactly match the value used inside your `Jenkinsfile`.

```text
Jenkins NodeJS installation name
            │
            ▼
        node-lts
            │
            ▼
Jenkinsfile references:
nodeJSInstallationName: 'node-lts'
```
*If the name in Jenkins is different, you must update your `Jenkinsfile` to match it.*

---

## 🔒 4. Configure Snyk
Snyk is utilized in this project for **Software Composition Analysis (SCA)**. The purpose of the SCA stage is to identify and surface known vulnerabilities inside your application dependencies.

Your pipeline script contains the following block:
```groovy
snykSecurity(
    snykInstallation: 'snyk@latest',
    snykTokenId: 'snyk-api-token'
)
```
This means Jenkins requires a specific Snyk installation named `snyk@latest` and a stored Jenkins credential mapped to the ID `snyk-api-token`.

### ⚙️ 4.1 Configure Snyk Installation
1. Navigate to: **Jenkins** ➡️ **Manage Jenkins** ➡️ **Tools**
2. Locate the **Snyk installation** configuration block provided by the Snyk plugin.
3. Click **Add Snyk** and configure it:
   * **Name:** `snyk@latest`
   * *Make sure this name matches the exact string defined in your `Jenkinsfile`: `snykInstallation: 'snyk@latest'`*

## 🔑 5. Create Snyk API Token
Log in to your Snyk account and generate an API token. 

> ⚠️ **Important Security Note:** The token value is a secret and must **never** be hard-coded or committed to GitHub.

* **Snyk API Token Format Example:** `snyk_xxxxxxxxxxxxxxxxxxxxxxxxx` *(Note: This value is a format example only. Use your actual Snyk token when configuring Jenkins).*

---

## 📌 6. Add Snyk Credential to Jenkins
Navigate to: **Jenkins** ➡️ **Manage Jenkins** ➡️ **Credentials**

Select the appropriate credentials store, then navigate to: **Global credentials** ➡️ **Add Credentials**. Configure the credential fields exactly as follows:

* **Kind:** `Secret text`
* **Scope:** `Global`
* **Secret:** `<YOUR_SNYK_API_TOKEN>`
* **ID:** `snyk-api-token` *(This field must match exactly)*
* **Description:** `Snyk API Token`

> 💡 **Mapping Check:** The field **ID:** `snyk-api-token` must match exactly because the `Jenkinsfile` references it via `snykTokenId: 'snyk-api-token'`.

```text
Jenkins Credential
        │
        ├── ID: snyk-api-token
        │
        └── Secret: <actual Snyk API token>
                       │
                       ▼
                Jenkins Pipeline
                       │
                       ▼
              Snyk Security Scan
```

---

## 🕸️ 7. Configure Netlify
Netlify hosts and deploys the React web application. The pipeline invokes the Netlify CLI to deploy the generated production build. 

To configure this step, ensure you gather the following two pieces of information:
* **Netlify Site ID**
* **Netlify Personal Access Token**

---

## ⚡ 8. Create Netlify Personal Access Token
The Netlify Personal Access Token authenticates Jenkins to complete the upload. Generate a personal access token within your Netlify account settings.

* **Netlify Personal Access Token Format Example:** `nfp_xxxxxxxxxxxxxxxxxxxxxxxxx` *(Note: This value is a format example only. Do not commit actual tokens to GitHub).*

---

## 📥 9. Add Netlify Credential to Jenkins
Navigate to: **Jenkins** ➡️ **Manage Jenkins** ➡️ **Credentials** ➡️ **Global credentials** ➡️ **Add Credentials**

Create a new credential using these settings:
* **Kind:** `Secret text`
* **Scope:** `Global`
* **Secret:** `<YOUR_NETLIFY_PERSONAL_ACCESS_TOKEN>`
* **ID:** `netlify-personal-access-token` *(This field must match exactly)*
* **Description:** `Netlify Personal Access Token`

> 💡 **Mapping Check:** The field **ID:** `netlify-personal-access-token` is required because the `Jenkinsfile` maps it to an environment variable via:
> `NETLIFY_AUTH_TOKEN = credentials("netlify-personal-access-token")`

---

## 🆔 10. Configure Netlify Site ID
The Netlify Site ID explicitly identifies which web application target Jenkins should update. 

Replace the placeholder `'YOUR_SITE_ID'` in your `Jenkinsfile` environment block with your actual site identifier:

```groovy
environment {
    NETLIFY_AUTH_TOKEN = credentials("netlify-personal-access-token")
    NETLIFY_SITE_ID = '12345678-abcd-1234-abcd-123456789abc' // Replace with your actual Site ID
}
```

```text
Netlify Personal Access Token  ──► Used for authentication
Netlify Site ID                ──► Identifies the deployment target
```

---

## 📊 11. Environment Variables in Jenkins
Your final environment block configuration inside the pipeline will look like this:

```groovy
environment {
    NETLIFY_AUTH_TOKEN = credentials("netlify-personal-access-token")
    NETLIFY_SITE_ID = 'YOUR_SITE_ID'
```

### 🔒 Essential Security Best Practices
Never hard-code or commit the following items into your public or private Git repositories:
* Netlify Personal Access Tokens
* Snyk API Tokens
* Passwords / Private Keys

Always store these values inside **Jenkins Credentials** and safely reference their container IDs inside the application workflow script.

---

## 🔄 12. Git Checkout Configuration
The primary stage of the pipeline pulls down the target React repository application code directly from GitHub.

```groovy
stage('Checkout') {
    steps {
        git(
            branch: 'main',
            changelog: false,
            poll: false,
            url: 'https://github.com/Veverita-Engineering/Customer-Portal'
        )
    }
}
```
* **Repository Target URL:** `https://github.com/Veverita-Engineering/Customer-Portal`
* **Target Build Branch:** `main`

---

## 🛠️ 13. Using Jenkins Pipeline Syntax Generator
Instead of manually typing the Git checkout syntax block from scratch, utilize the integrated Jenkins **Pipeline Syntax / Snippet Generator** tool to safely format your pipelines steps without introducing manual syntax formatting errors.

### Accessing the Generator:
1. Navigate directly to your active Jenkins Pipeline project page.
2. Click on the **Pipeline Syntax** link located in the left sidebar menu context, or access it directly inside the pipeline configuration interface workspace.

## 📝 14. Generate Git Checkout Syntax
Inside the Jenkins **Pipeline Syntax Generator**, choose your settings to get structural pipeline code snippets:

1. Select **Sample Step** ➡️ **git: Git**
2. Fill out the target inputs:
   * **Repository URL:** `https://github.com/Veverita-Engineering/Customer-Portal`
   * **Branch:** `*/main`

Depending on your active version framework, the tool will produce a snippet structured like this:
```groovy
git branch: 'main',
    changelog: false,
    poll: false,
    url: 'https://github.com/Veverita-Engineering/Customer-Portal'
```
You can drop this generated execution block directly inside your step definitions:
```groovy
stage('Checkout') {
    steps {
        git branch: 'main',
            changelog: false,
            poll: false,
            url: 'https://github.com/Veverita-Engineering/Customer-Portal'
    }
}
```

---

## 🔒 15. Git Checkout with a Private Repository
If your downstream project repository is shifted to a **private** visibility state, you must implement explicit access controls:
* Select **Kind:** `Username with password` (or choose another supported provider token mechanism).
* Select this credential binding while building your snippet syntax rules.
* *Note: Public infrastructure repositories do not require explicit credential declarations to complete execution blocks.*

---

## 📄 16. Complete Jenkinsfile
Create a file named `Jenkinsfile` (no extension) directly in the root directory of your project, and add the following complete pipeline architecture script:

```groovy
pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials("netlify-personal-access-token")
        NETLIFY_SITE_ID = 'YOUR_SITE_ID' // Replace with your actual Netlify Site ID
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    changelog: false,
                    poll: false,
                    url: 'https://github.com/Veverita-Engineering/Customer-Portal'
                )
            }
        }

        stage('Build') {
            steps {
                nodejs(nodeJSInstallationName: 'node-lts') {
                    sh 'node --version'
                    sh 'npm --version'
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Parallel Stage') {
            failFast true
            parallel {
                stage('Test') {
                    steps {
                        sh 'npm test -- --reporters=jest-junit'
                    }
                }
                stage('SCA') {
                    steps {
                        snykSecurity(
                            snykInstallation: 'snyk@latest',
                            snykTokenId: 'snyk-api-token'
                        )
                    }
                }
            }
        }

        stage('Deploy Staging') {
            steps {
                echo "Deploying to staging site ID: ${NETLIFY_SITE_ID}"
                nodejs(nodeJSInstallationName: 'node-lts') {
                    sh 'node_modules/.bin/netlify deploy --dir=build'
                }
            }
        }

        stage('Deploy Prod') {
            steps {
                nodejs(nodeJSInstallationName: 'node-lts') {
                    echo "Deploying site: ${NETLIFY_SITE_ID}"
                    sh 'node_modules/.bin/netlify status'
                    sh 'node_modules/.bin/netlify deploy --dir=build --prod'
                    
                    // Post-deployment smoke test
                    sh 'curl -s https://YOUR-WEBSITE-NAME | grep -q "React App"'
                }
            }
        }
    }

    post {
        success {
            archiveArtifacts(
                artifacts: 'build/**',
                fingerprint: true
            )
        }
        always {
            junit 'reports/junit.xml'
        }
    }
}
```

---

## 🏗️ 17. Create Jenkins Pipeline Job
Once your `Jenkinsfile` has been committed and pushed to your GitHub repository, set up the continuous execution process:

1. Navigate to: **Jenkins** ➡️ **New Item**
2. Input a distinct project name (e.g., `Customer-Portal-CI-CD`).
3. Highlight **Pipeline** from the asset templates list.
4. Click **OK**.

---

## ⚙️ 18. Configure Pipeline from SCM
Scroll down inside your newly provisioned pipeline orchestration window to find the **Pipeline** configuration workspace block, then configure the settings exactly as follows:

* **Definition:** `Pipeline script from SCM`
* **SCM:** `Git`
* **Repository URL:** `https://github.com/Veverita-Engineering/Customer-Portal`
* **Branch Specifier:** `*/main`
* **Script Path:** `Jenkinsfile`

```text
Definition
    │
    └── Pipeline script from SCM
            │
            ├── SCM: Git
            │
            ├── Repository URL ──► https://github.com/Veverita-Engineering/Customer-Portal
            │
            ├── Branch         ──► */main
            │
            └── Script Path    ──► Jenkinsfile
```
Click **Save**.

---

## 🚀 19. Run the Pipeline
From the main project panel page, select **Build Now** to execute the pipeline. The visual step execution maps to this logical pipeline track structure:

```text
Checkout
   ↓
Build
   ↓
┌───────────────┬───────────────┐
│     Test      │      SCA      │
│    (Jest)     │    (Snyk)     │
└───────────────┴───────────────┘
   ↓
Deploy Staging
   ↓
Deploy Prod
   ↓
Smoke Test
   ↓
Archive Artifacts
   ↓
Publish JUnit Results
```

---

## 📊 20. Expected Jenkins Result
When operations complete smoothly, all validation tracks will display green checkmarks within the dashboard interface:

* ✅ **Checkout** — Codebase pulled successfully.
* ✅ **Build** — Build artifacts bundled without compiler flags.
* ✅ **Test** — Test suites evaluated cleanly.
* ✅ **SCA** — Dependency scan reports clear of risk markers.
* ✅ **Deploy Staging** — Deployed onto target sandbox hosts.
* ✅ **Deploy Prod** — Shifted cleanly onto production systems.
* ✅ **Smoke Test** — Target addresses respond cleanly.
* ✅ **Archive Artifacts** — Production bundles stored inside historical records.
* ✅ **JUnit Results** — Structural testing diagnostics logs generated.

*(Tip: You can use the Jenkins Blue Ocean UI extension for a highly detailed, real-time graphical representation of these stages).*

---

## 🛠️ 21. Troubleshooting Common Failures

### 🔴 Node.js Installation Not Found
* **Symptom:** Terminal reports `node-lts` cannot be successfully found or executed.
* **Resolution:** Navigate to **Manage Jenkins** ➡️ **Tools** ➡️ **NodeJS installations**. Verify that your tool block title string matches the layout inside your configuration parameter string (`node-lts`) exactly.

### 🔴 Snyk Installation Not Found
* **Symptom:** Pipeline fails at the security checkpoint tracking Snyk execution targets.
* **Resolution:** Review your global installations window and confirm that the tool flag configuration name string is set exactly to: `snyk@latest`.

### 🔴 Snyk/Netlify Authentication Failures
* **Symptom:** Process returns HTTP `401 Unauthorized` or `403 Forbidden` response rules.
* **Resolution:** Verify your settings under **Manage Jenkins** ➡️ **Credentials**. Ensure that `snyk-api-token` and `netlify-personal-access-token` exist, are mapped as **Secret Text**, and contain valid tokens.

### 🔴 Git Checkout Errors
* **Symptom:** SCM tracking throws exceptions connecting to host address spaces.
* **Resolution:** Double-check your target repository address string (`https://github.com/Veverita-Engineering/Customer-Portal`) and confirm that the branch rules match your remote source (`main`).

### 🔴 JUnit Report Missing
* **Symptom:** Pipeline raises warnings or fails during post-actions with `reports/junit.xml does not exist`.
* **Resolution:** Ensure your testing suites framework engine writes out outputs directly to the matching directory asset path location (`reports/junit.xml`).

---

## 📋 22. Configuration Summary
Before running the pipeline, verify that all configurations match the following baseline specifications:

| Component | Required Specification Value |
| :--- | :--- |
| **Git Repository** | `https://github.com/Veverita-Engineering/Customer-Portal` |
| **Git Branch** | `main` |
| **Jenkins NodeJS Name** | `node-lts` |
| **Jenkins Snyk Name** | `snyk@latest` |
| **Snyk Credential ID** | `snyk-api-token` |
| **Netlify Credential ID**| `netlify-personal-access-token` |
| **Netlify Site ID** | `<YOUR_SITE_ID>` |
| **Script Path** | `Jenkinsfile` |
| **JUnit Report Path** | `reports/junit.xml` |
| **React Build Directory**| `build/` |


---

## 🔐 Security Checklist
Before pushing your project infrastructure to GitHub, run through this crucial security audit:

- [ ] **No Snyk API Token** is hard-coded or committed.
- [ ] **No Netlify Personal Access Token** is hard-coded or committed.
- [ ] **No passwords** or admin credentials are exposed.
- [ ] **No private keys** are stored in the codebase.
- [ ] **No production secrets** are written in plain text inside the `Jenkinsfile`.
- [ ] **Jenkins Credentials store** is strictly utilized to inject all secrets.
- [ ] **`.gitignore` file** is actively configured to ignore local environment variables, `node_modules/`, and build artifacts.

> 💡 **The Golden DevSecOps Rule:**
> * **Configuration** ➡️ Commit securely to **GitHub**
> * **Secrets / Keys** ➡️ Inject safely via **Jenkins Credentials**

---

## 📌 Final Lab Architecture
Below is the continuous integration, continuous delivery, and DevSecOps structural pipeline map for your React application:

```text
                    GitHub
                      │
                      │
                      ▼
                  Jenkins
                      │
              ┌───────┴───────┐
              │   Checkout    │
              └───────┬───────┘
                      │
                      ▼
                   Build
                      │
             ┌────────┴────────┐
             │                 │
             ▼                 ▼
          Jest Test          Snyk SCA
             │                 │
             └────────┬────────┘
                      │
                      ▼
                Netlify Staging
                      │
                      ▼
                Netlify Production
                      │
                      ▼
                 Smoke Test
                      │
                      ▼
              Reports / Artifacts
```

*This completes the Jenkins CI/CD and DevSecOps automated pipeline configuration rules for the React web application codebase.*

