# 📖 Project Resources & Documentation References

This file aggregates all official documentation links, plugin repositories, and reference guides used to construct, validate, and troubleshoot this DevSecOps CI/CD pipeline.

---

### 🏗️ Jenkins Core & Pipeline Syntax
* [Jenkins Pipeline Getting Started Guide](https://www.jenkins.io/doc/book/pipeline/getting-started/) — Fundamental overview of designing pipelines as code.
* [Jenkins Declarative Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/) — Structural guide for stages, agents, and environmental injections.
* [Jenkins Parallel Stage Reference](https://www.jenkins.io/doc/book/pipeline/syntax/#parallel) — Optimizing pipelines using concurrent execution tracks.
* [Jenkins Post-Conditions Reference](https://www.jenkins.io/doc/book/pipeline/syntax/#post) — Using `always`, `success`, and `failure` blocks.

---

### 🔍 Snippet Generation & Credentials Management
* [Jenkins Snippet Generator Guide](https://www.jenkins.io/doc/book/pipeline/getting-started/#snippet-generator) — Using built-in tool utilities to draft pipeline steps dynamically.
* [Jenkins Environment Variables Tour](https://www.jenkins.io/doc/pipeline/tour/environment/) — Injecting runtime environments globally or per-stage.
* [Jenkins Credentials Store Management](https://www.jenkins.io/doc/book/using/using-credentials/) — Best practices for provisioning tokens, keys, and masking secrets securely.

---

### 🧪 Tests & Artifact Archival
* [Jenkins Recording Tests and Artifacts](https://www.jenkins.io/doc/pipeline/tour/tests-and-artifacts/) — Processing test suites and exporting telemetry logs.
* [Jenkins Core: archiveArtifacts Step](https://www.jenkins.io/doc/pipeline/steps/core/#archiveartifacts-archive-the-artifacts) — Syntax for persisting production-ready distribution builds.

---

### 🧩 Plugin Repositories
* [Jenkins Git Plugin Step Reference](https://www.jenkins.io/doc/pipeline/steps/git/) — Shorthand configurations for remote repository checkouts.
* [Jenkins NodeJS Plugin Portal](https://plugins.jenkins.io/nodejs/) — Global configuration profiles and auto-installation tools.
* [Jenkins Snyk Security Scanner Plugin](https://plugins.jenkins.io/snyk-security-scanner/) — Integrating automated Software Composition Analysis (SCA).

---

### 🕸️ Netlify Deployment & CLI Reference
* [Netlify Create Deploys Overview](https://docs.netlify.com/deploy/create-deploys/) — Learning structural methods to publish production assets.
* [Netlify Drag-and-Drop Dropzone Manual](https://docs.netlify.com/deploy/create-deploys/#drag-and-drop) — Manual fallback options for testing unlinked site assets.
* [Netlify CLI Getting Started Guide](https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/) — Local installations and automated authentication mapping.
* [Netlify CLI Deploy Command Reference](https://cli.netlify.com/commands/deploy/) — Flag controls for runtime production switches (`--prod`).

---

### 📊 Alternative Dashboards (Legacy)
* [Jenkins Blue Ocean UI Getting Started Guide](https://www.jenkins.io/doc/book/blueocean/getting-started/) — Visual state metrics tracking. *(Note: Blue Ocean has been officially deprecated since July 2026)*.
