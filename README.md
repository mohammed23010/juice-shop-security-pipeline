# ![Security Pipeline](https://github.com/mohammed23010/juice-shop-security-pipeline/actions/workflows/security.yml/badge.svg)

# Every push to `main` triggers all three scans in parallel. Results are uploaded

# as downloadable artifacts in the \*\*Actions\*\* tab.

# 

# \## 🚀 Pipeline Jobs

# 

# \### 1. SCA — Trivy

# Scans the repository filesystem for known vulnerabilities in dependencies,

# reporting `CRITICAL` and `HIGH` severity issues.

# 

# \### 2. SAST — Semgrep

# Runs static analysis using Semgrep's `auto` ruleset to detect insecure coding

# patterns (injection, hardcoded secrets, etc.), outputting results in SARIF format.

# 

# \### 3. DAST — OWASP ZAP

# Spins up a live Juice Shop container and runs a ZAP Baseline scan against it,

# identifying runtime vulnerabilities such as missing security headers and

# misconfigurations.

# 

# \## 📊 Viewing Results

# 

# 1\. Go to the \*\*Actions\*\* tab of this repository.

# 2\. Select the latest workflow run.

# 3\. Download the report artifacts (`trivy-report`, `semgrep-report`, ZAP report).

# 

# \## 🎯 Key Concepts Demonstrated

# 

# \- \*\*Shift-left security\*\* — catching issues early in the development lifecycle

# \- \*\*Defense in depth\*\* — combining static, dynamic, and dependency analysis

# \- \*\*Security automation\*\* — no manual steps; security runs on every commit

# \- \*\*CI/CD integration\*\* — security as a first-class citizen in the pipeline

# 

# \## 🔧 Tech Stack

# 

# GitHub Actions · Docker · Trivy · Semgrep · OWASP ZAP

# 

# \---

# 

# > ⚠️ This project uses OWASP Juice Shop, a deliberately insecure application

# > built for security training. All scanning is performed in an isolated CI

# > environment for educational purposes.

