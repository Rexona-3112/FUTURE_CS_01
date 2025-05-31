# 📄 Vulnerability Report

This document provides technical details of the vulnerabilities identified during the assessment of the web application hosted at [http://testphp.vulnweb.com](http://testphp.vulnweb.com). The focus was on detecting and exploiting **Reflected XSS** and **SQL Injection** vulnerabilities.

---

## 🛑 1. Cross-Site Scripting (XSS)

### ✅ Type: Reflected XSS

### 🌐 Affected URL:
```

[http://testphp.vulnweb.com/search.php](http://testphp.vulnweb.com/search.php)

````

### 🔍 Vulnerable Parameter:
`test` (search input field)

### 🧪 Proof of Concept (PoC):

#### Payload Used:
```html
<?foo="><x foo='?><script>javascript:alert(1)</script>'>">
````

#### Steps to Reproduce:

1. Visit `http://testphp.vulnweb.com/search.php`
2. Enter the payload in the search box or use Burp Suite to inject it
3. Observe that the payload is reflected unescaped in the HTML response
4. An alert popup confirms successful XSS execution

#### Impact:

* JavaScript code execution in the victim's browser
* Session hijacking
* Credential theft
* Phishing attacks
* Website defacement

#### Mitigation:

* Sanitize and validate all user input
* Use output encoding (HTML context-aware)
* Implement a Content Security Policy (CSP)
* Avoid dangerous functions like `innerHTML` and `eval`
* Use frameworks that auto-escape output

---

## 💉 2. SQL Injection (SQLi)

### ✅ Type: GET-based SQL Injection

### 🌐 Affected URL:

```
http://testphp.vulnweb.com/listproducts.php?cat=1
```

### 🔍 Vulnerable Parameter:

`cat`

### 🧪 Proof of Concept (PoC):

#### Manual Payload:

```
' OR '1'='1
```

#### SQLMap Command:

```bash
sqlmap -u "http://testphp.vulnweb.com/listproducts.php?cat=1" --dbs
```

#### Exploitation Steps:

1. Identify the vulnerable parameter (`cat`) via browser testing
2. Confirm injection using `' OR '1'='1`
3. Use SQLMap to enumerate databases:

   ```bash
   sqlmap -u "http://testphp.vulnweb.com/listproducts.php?cat=1" --dbs
   ```
4. Extract tables and data from the `acuart` database

#### Impact:

* Unauthorized data access and extraction
* Account takeover
* Bypassing authentication mechanisms
* Full database compromise

#### Mitigation:

* Use parameterized queries (prepared statements)
* Employ stored procedures
* Validate and sanitize all inputs
* Restrict DB access with least privilege
* Enable WAF and conduct regular security assessments

---

## ✅ Summary

| Vulnerability | Severity | URL                                                 | Status    |
| ------------- | -------- | --------------------------------------------------- | --------- |
| Reflected XSS | High     | `http://testphp.vulnweb.com/search.php`             | Confirmed |
| SQL Injection | High     | `http://testphp.vulnweb.com/listproducts.php?cat=1` | Confirmed |

---

> This assessment was conducted in a legal environment designed for ethical hacking practice. Do not attempt to exploit vulnerabilities on production systems without permission.
