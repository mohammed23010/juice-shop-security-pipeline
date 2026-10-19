\# Security Findings: Automated vs. Manual



This document compares vulnerabilities discovered by the \*\*automated pipeline\*\*

(SAST / DAST / SCA) against those found through \*\*manual testing\*\*, demonstrating

why both approaches are necessary in a mature DevSecOps program.



Scan target: OWASP Juice Shop · ZAP Baseline (automated in CI) · Burp Suite (manual).



\## Summary



| Approach | Findings | Highest Severity |

|----------|----------|------------------|

| Automated (OWASP ZAP DAST) | 2 Medium, 5 Low, 8 Informational | Medium |

| Manual testing (Burp Suite) | SQLi, XSS, Broken Access Control | \*\*Critical\*\* |



The automated scan reliably caught configuration and information-disclosure

issues, but the \*\*highest-impact vulnerabilities were only found through manual

testing.\*\*



\---



\## Automated Findings (OWASP ZAP Baseline, in CI)



Detected automatically by the DAST job on every pipeline run:



| Finding | Severity | CWE | Type |

|---------|----------|-----|------|

| Content Security Policy (CSP) Header Not Set | Medium | 693 | Missing hardening |

| Cross-Domain Misconfiguration (CORS `\*`) | Medium | 264 | Misconfiguration |

| Cross-Origin-Embedder-Policy missing | Low | 693 | Missing header |

| Cross-Origin-Opener-Policy missing | Low | 693 | Missing header |

| Dangerous JS Functions (`bypassSecurityTrustHtml`) | Low | 749 | Risky code pattern |

| Deprecated Feature-Policy header | Low | 16 | Outdated config |

| Full Path Disclosure (via `/ftp`) | Low | 209 | Information leak |

| Base64 Disclosure, Sec-Fetch headers, cacheable content | Info | — | Informational |



\*\*Characteristic:\*\* all are configuration, header, or information-disclosure

issues — exactly what passive automated scanning finds reliably and repeatably.



\---



\## Manual Findings (Burp Suite)



\*\*Not\*\* detected by the automated baseline scan; required manual testing to

discover and confirm:



\### 1. SQL Injection — Authentication Bypass (Critical)

\- \*\*Endpoint:\*\* `POST /rest/user/login`

\- \*\*Payload:\*\* `' OR 1=1-- `

\- \*\*Result:\*\* logged in as administrator (`admin@juice-sh.op`) without a password.

\- Full report: \[`sqli-login-bypass-report.md`](../sqli-login-bypass-report.md)



\### 2. DOM-based XSS (High)

\- \*\*Location:\*\* search field

\- \*\*Payload:\*\* `<iframe src="javascript:alert('XSS')">`

\- \*\*Result:\*\* arbitrary JavaScript executed in the browser.



\### 3. Broken Access Control (High)

\- \*\*Location:\*\* `/#/administration`

\- \*\*Result:\*\* accessed the admin panel; exposed all registered users and feedback.



\---



\## Analysis: Why the Gap?



The automated ZAP Baseline scan is a \*\*passive\*\* scan — it inspects responses and

headers but does not craft application-specific attacks. It also flagged the app

as a \*"Modern Web Application"\* (Angular SPA), which limits how deeply an

automated spider can explore client-rendered routes.



As a result:

\- \*\*Automation excelled at\*\* breadth: headers, CORS, information disclosure —

&#x20; consistently, on every commit.

\- \*\*Automation missed\*\* injection and business-logic flaws (SQLi, XSS, access

&#x20; control) — the highest-severity issues, which required a human tester who

&#x20; understands the application's behavior.



\---



\## Key Takeaway



A mature DevSecOps program combines both:

\- \*\*Automation\*\* for breadth, speed, and regression coverage on every commit.

\- \*\*Manual expertise\*\* for depth and high-impact vulnerabilities.



> DAST in a pipeline complements — but never replaces — manual security testing.

