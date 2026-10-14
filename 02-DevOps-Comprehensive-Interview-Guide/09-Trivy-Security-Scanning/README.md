# Section 9: Trivy and Container Security

---

## Q84. What is Trivy?

**🔑 Keywords:** vulnerability scanner, open-source security tool, OS packages, language dependencies (npm, pip, maven), misconfigurations, secrets scanning

**Beginner-Friendly Answer:**
Trivy is a fast, comprehensive security scanner by Aqua Security. It scans Docker container images, Git repositories, and Kubernetes manifests for known security vulnerabilities (CVEs), outdated dependencies, embedded passwords/secrets, and infrastructure misconfigurations.

**Detailed Answer:**
Trivy is a security scanner that can identify issues such as known vulnerabilities in container images and dependencies. Its other scanning capabilities depend on the scan target and configuration.

---

## Q85. Why scan a Docker image before deployment?

**🔑 Keywords:** shift-left security, known CVEs, vulnerable base images, supply-chain defense, preventing zero-day risk

**Beginner-Friendly Answer:**
Even if your own Python or Go code is 100% bug-free, the base Linux image (like Ubuntu or Debian) or third-party libraries you pulled in via `npm` or `pip` might contain known security flaws that hackers can exploit. Scanning with Trivy catches these vulnerabilities *before* the image ever reaches production servers (shifting security left).

**Detailed Answer:**
An image can contain vulnerable operating-system packages or application dependencies. Scanning helps identify known issues before the image is promoted or deployed.

---

## Q86. What is a CVE?

**🔑 Keywords:** Common Vulnerabilities and Exposures, standardized identifier, NVD (National Vulnerability Database), CVSS scoring

**Beginner-Friendly Answer:**
CVE stands for **Common Vulnerabilities and Exposures**. It is a globally standardized dictionary of publicly known cybersecurity flaws (e.g., `CVE-2021-44228` for Log4Shell). Each CVE has a unique number and a severity score (CVSS from 0.0 to 10.0), allowing security teams and automated scanners to track and prioritize fixes.

**Detailed Answer:**
CVE stands for Common Vulnerabilities and Exposures. It is a system for identifying publicly disclosed cybersecurity vulnerabilities.

---

## Q87. What is the difference between vulnerability severity and exploitability?

**🔑 Keywords:** severity (CVSS theoretical impact) vs exploitability (real-world feasibility in your environment), context-aware risk

**Beginner-Friendly Answer:**
- **Severity:** How bad the damage could theoretically be if the bug is triggered (e.g. "Critical - can wipe server").
- **Exploitability:** How easy it actually is for a real hacker to exploit it in *your specific setup*. For example, a vulnerability might be labeled "Critical", but if the vulnerable component is not exposed to the internet and the vulnerable function is never invoked, its real-world exploitability is low.

**Detailed Answer:**
Severity estimates the potential impact of a vulnerability under defined conditions. Exploitability considers how feasible it is to exploit the vulnerability in a particular context. A high-severity finding still needs contextual assessment, and a lower-severity issue can matter in a sensitive environment.

---

## Q88. Should a pipeline fail on every vulnerability?

**🔑 Keywords:** risk-based policy, severity gates (`--severity CRITICAL,HIGH`), exit code (`--exit-code 1`), mitigation tracking, developer friction

**Beginner-Friendly Answer:**
No. If your CI/CD pipeline failed on every single minor or low vulnerability, developers would never be able to deploy any software! Teams establish clear policies: for example, the build only fails on `CRITICAL` or `HIGH` vulnerabilities that have an available fix. Lower-priority findings are logged in an issue tracker for scheduled updates.

**Detailed Answer:**
Not necessarily. Teams define a risk-based policy using factors such as severity, exploitability, exposure, available fixes, and organizational requirements. Critical findings may block a release, while other findings may require remediation tracking.

### Cross-question: What if Trivy reports a vulnerability but no fix is available?

**Answer:**
I would verify the finding, assess whether the vulnerable component is reachable or exposed, review mitigations, document the risk, and track remediation. I would not silently ignore it.

---

## Q89. Does a successful Trivy scan prove that an image is secure?

**🔑 Keywords:** scanner limitations, zero-day vulnerabilities, application logic bugs, defense in depth, runtime security

**Beginner-Friendly Answer:**
No! A clean Trivy scan only proves that your image has no *known* vulnerabilities currently documented in public databases. It cannot detect zero-day exploits (flaws discovered today that nobody has cataloged yet), bugs in your business logic (like forgetting to check passwords on an admin endpoint), or stolen credentials. True security requires layers: secure coding, network policies, IAM least privilege, and runtime monitoring.

**Detailed Answer:**
No. Scanners have coverage and detection limitations. A successful scan does not prove the absence of vulnerabilities, insecure application logic, leaked credentials, runtime weaknesses, or configuration problems.
