# OWASP-Juice-Shop-Mock-Staging-Penetration-Test 

## Project Overview

The OWASP Juice Shop Mock Staging Penetration Testing Lab is a hands-on cybersecurity project focused on performing a controlled penetration test against an intentionally vulnerable web application in an isolated local environment.

In this lab, I configured OWASP Juice Shop as a local mock staging environment using Docker and performed reconnaissance, HTTP enumeration, API endpoint enumeration, vulnerability testing, and controlled exploitation.

During the assessment, a SQL Injection vulnerability in the login functionality was successfully exploited to bypass authentication and gain administrator-level access within the lab environment.

The project also included documenting the exploit chain, analyzing the impact of the vulnerability, and preparing a remediation guide containing recommended security controls and a retest procedure.

This lab provided practical experience relevant to web application security, penetration testing, vulnerability validation, security documentation, and SOC/cybersecurity analysis.

# Objectives

* Set up an isolated mock staging environment for security testing
* Deploy OWASP Juice Shop using Docker
* Perform basic network reconnaissance
* Identify the exposed HTTP service
* Enumerate HTTP responses and application information
* Identify API endpoints used by the application
* Test accessible application functionality
* Identify and validate a SQL Injection vulnerability
* Demonstrate authentication bypass in the controlled lab
* Verify administrator-level access after exploitation
* Document the complete exploit chain
* Identify the security impact of the vulnerability
* Prepare a remediation guide
* Document a retest procedure for the vulnerability

## Lab Environment

| **Component**           | **Details**                                 |
| ----------------------- | ------------------------------------------- |
| Operating System        | Kali Linux                                  |
| Virtualization Platform | Oracle VirtualBox                           |
| Target Application      | OWASP Juice Shop                            |
| Deployment              | Docker                                      |
| Target URL              | `http://localhost:3000`                     |
| Network Scope           | Localhost / isolated lab environment        |
| Testing Type            | Controlled Web Application Penetration Test |

## Tools Used

* **Docker** – Used to deploy the OWASP Juice Shop application locally
* **Nmap** – Used for basic network and service reconnaissance
* **cURL** – Used for HTTP response and API enumeration
* **Web Browser** – Used to interact with the Juice Shop application
* **OWASP Juice Shop** – Used as the intentionally vulnerable mock staging target
* **Kali Linux** – Used as the penetration testing environment

# Testing Scenario

An intentionally vulnerable OWASP Juice Shop application was deployed locally as a controlled mock staging environment.

The objective was to perform a basic penetration test against the application and identify a security vulnerability that could demonstrate a realistic attack path.

The assessment focused on:

• Reconnaissance
• HTTP service identification
• Application enumeration
• API endpoint enumeration
• Vulnerability testing
• Controlled exploitation
• Impact verification
• Remediation documentation

All testing was performed against the local intentionally vulnerable application.

# Penetration Testing Process

## Step 1 - Set Up Docker

Docker was installed and configured on the Kali Linux testing system.

Docker was then used to deploy the OWASP Juice Shop container.

The application was started using:

```bash
sudo docker run -d --name juice-shop -p 3000:3000 bkimminich/juice-shop
```

The running container was verified using Docker:

```bash
sudo docker ps
```

The Juice Shop container was confirmed to be running and exposing port `3000`.

## Step 2 - Access the Mock Staging Environment

The application was accessed through the local browser:

```text
http://localhost:3000
```

The OWASP Juice Shop interface was successfully loaded.

This confirmed that the mock staging environment was operational and ready for controlled security testing.

## Step 3 - Perform Network Reconnaissance

Nmap was used to identify the exposed service and application port:

```bash
sudo nmap -sV -p 3000 localhost
```

The scan identified:

• TCP port `3000` as open
• An HTTP service responding on the port
• OWASP Juice Shop content in the HTTP response

The Nmap result confirmed the exposed application service before further enumeration was performed.

## Step 4 - Enumerate HTTP Response

The HTTP response headers were reviewed using cURL:

```bash
curl -I http://localhost:3000
```

The response returned:

• `HTTP/1.1 200 OK`
• `Content-Type: text/html`
• `X-Content-Type-Options: nosniff`
• `X-Frame-Options: SAMEORIGIN`
• Application-related response information

The HTTP response confirmed that the web application was accessible and provided information useful for further application enumeration.

## Step 5 - Identify Application JavaScript Files

The main application JavaScript resources were identified from the application's HTML response.

The following files were observed:

• `polyfills.js`
• `scripts.js`
• `main.js`

The main JavaScript file was then reviewed for application routes and API references.

## Step 6 - Enumerate API Endpoints

The application's JavaScript code was searched for API references:

```bash
curl -s http://localhost:3000/main.js | grep -oE 'api/[A-Za-z0-9_./?=&${}-]+' | head -30
```

Several API endpoints were identified, including:

• `/api/Users`
• `/api/Products`
• `/api/Feedbacks`
• `/api/BasketItems`
• `/api/Challenges`
• `/api/Addresss`
• `/api/Cards`
• `/api/SecurityAnswers`
• `/api/SecurityQuestions`

This enumeration helped identify application functionality and potential areas for further testing.

## Step 7 - Test the Products API

The Products API was accessed using:

```bash
curl -s http://localhost:3000/api/Products
```

The endpoint returned product information in JSON format.

The response demonstrated that product information was accessible through the application's API.

This was documented as part of application enumeration and was not independently treated as a confirmed vulnerability.

## Step 8 - Test Product Search Functionality

The product search functionality was tested using:

```bash
curl -i http://localhost:3000/rest/products/search?q=test
```

The endpoint returned a successful HTTP response containing matching product information.

A special input was then tested:

```bash
curl -i "http://localhost:3000/rest/products/search?q=%27"
```

The application returned a normal response without a database error.

No SQL Injection vulnerability was claimed from this test alone because the response did not provide sufficient evidence of SQL query manipulation.

## Step 9 - Test Login Functionality

The application's login functionality was tested within the intentionally vulnerable Juice Shop environment.

A controlled SQL Injection authentication-bypass payload was entered into the login functionality:

```text
' OR 1=1--
```

A test password was supplied along with the crafted input.

The application accepted the crafted input and successfully authenticated the session as an administrator.

This confirmed a SQL Injection-based authentication bypass within the controlled lab environment.

## Step 10 - Verify Administrator Access

After successful authentication bypass, administrator-level access was verified within the application.

The Juice Shop challenge confirmation indicated that the administrator login challenge had been successfully solved.

The resulting administrator access demonstrated the impact of the authentication bypass.

The testing session was subsequently logged out.

# Exploit Chain

The identified exploit chain was:

```text
Application Enumeration
        ↓
Login Functionality Identified
        ↓
SQL Injection Payload Submitted
        ↓
Authentication Logic Bypassed
        ↓
Administrator Authentication
        ↓
Privileged Application Access
```

The vulnerability demonstrated how insufficiently protected authentication input could result in unauthorized privileged access.

# Vulnerability Finding

## SQL Injection – Authentication Bypass

### Description

The login functionality in the intentionally vulnerable staging application was susceptible to SQL Injection.

A crafted input was able to alter the application's authentication logic and bypass the expected authentication process.

### Exploitation

The following controlled test input was used:

```text
' OR 1=1--
```

The payload was submitted through the application's login functionality.

The application successfully authenticated the session as an administrator.

### Impact

Successful exploitation resulted in:

• Authentication bypass
• Unauthorized administrator authentication
• Access to privileged application functionality
• Potential compromise of sensitive application functionality

The demonstrated impact was limited to the intentionally vulnerable local Juice Shop environment.

# Remediation

The following security controls were documented as remediation recommendations:

* Use parameterized/prepared database queries.
* Never concatenate user-controlled input directly into SQL queries.
* Apply server-side input validation.
* Use secure password hashing and proper authentication controls.
* Return generic authentication errors without exposing database details.
* Apply appropriate authentication and authorization controls to privileged functionality.

A separate remediation document was created:

```text
remediation.md
```

# Retest Procedure

After applying the recommended remediation, the original authentication test should be repeated using the same controlled input.

The expected secure behavior is:

• The crafted SQL Injection input should not alter the authentication query.
• Authentication should fail.
• The application must not authenticate the user as an administrator.
• No unauthorized privileged access should be granted.

The application was not modified during this lab to claim a successful remediation retest; therefore, the retest section documents the procedure and expected result rather than claiming that the vulnerability was successfully fixed.

# Key Findings

During the penetration testing exercise, the following activities were successfully completed:

• OWASP Juice Shop deployed as a local mock staging environment
• Docker container verified as running
• TCP port `3000` identified through Nmap
• HTTP service successfully enumerated
• Application JavaScript files identified
• Multiple API endpoints enumerated
• Products API response reviewed
• Product search functionality tested
• SQL Injection authentication bypass successfully demonstrated
• Administrator authentication successfully achieved in the controlled lab
• Exploit chain documented
• Remediation recommendations prepared

# Skills Demonstrated

• Web application reconnaissance
• Network/service enumeration
• HTTP enumeration
• API endpoint enumeration
• cURL-based testing
• Vulnerability identification
• SQL Injection testing
• Authentication bypass analysis
• Privilege/access impact verification
• Penetration testing methodology
• Vulnerability documentation
• Security remediation
• Security report writing
• Controlled security testing

# Lessons Learned

Through this hands-on penetration testing lab, I gained practical experience in performing a structured assessment of an intentionally vulnerable web application.

I learned how reconnaissance and application enumeration can help identify potential attack surfaces before vulnerability testing.

The lab also helped me understand how SQL Injection can affect authentication functionality and how a vulnerability in an authentication mechanism can result in unauthorized privileged access.

Documenting the exploit chain and remediation recommendations helped me understand the importance of not only identifying vulnerabilities but also clearly communicating their impact and recommended security controls.

This project provided practical exposure to web application security testing, vulnerability validation, penetration testing documentation, and security remediation.

---

# Screenshots

## Docker Installation

Screenshot showing the installed Docker version used to deploy the mock staging environment.

**01 - Docker Installed**

`01-docker-installed.png`

## OWASP Juice Shop Container Running

Screenshot showing the OWASP Juice Shop Docker container running and exposing port `3000`.

**02 - Juice Shop Container Running**

`02-juice-shop-container-running.png`

## Mock Staging Environment

Screenshot showing the OWASP Juice Shop application successfully running at `localhost:3000`.

**03 - Mock Staging Environment**

`03-mock-staging-environment.png`

## Nmap Reconnaissance

Screenshot showing the Nmap scan identifying the open TCP port `3000` and HTTP response from the application.

**04 - Reconnaissance**

`04-recon.png`

## HTTP Enumeration

Screenshot showing the HTTP response headers obtained using cURL.

**05 - HTTP Enumeration**

`05-http-enumeration.png`

## API Endpoint Enumeration

Screenshot showing API references identified from the application's JavaScript code.

**06 - API Endpoint Enumeration**

`06-api-endpoint-enumeration.png`

## Products API Response

Screenshot showing product information returned from the Products API.

**07 - Products API Response**

`07-api-products-response.png`

## Product Search Enumeration

Screenshot showing the product search endpoint responding to a controlled query.

**08 - Product Search Enumeration**

`08-product-search-enumeration.png`

## SQL Injection Authentication Bypass

Screenshot showing the successful authentication-bypass challenge after submitting the controlled SQL Injection payload.

**09 - SQL Injection Admin Login**

`09-sql-injection-admin-login.png`

## Administrator Access

Screenshot showing the resulting administrator-level access within the intentionally vulnerable application.

**10 - Administrator Access Impact**

`10-admin-access-impact.png`

# Conclusion

This project provided hands-on experience with performing a controlled penetration test against an intentionally vulnerable web application.

By deploying OWASP Juice Shop locally, performing reconnaissance and enumeration, identifying application endpoints, and testing authentication functionality, I successfully demonstrated a SQL Injection authentication bypass leading to administrator authentication.

The project also provided practical experience in documenting an exploit chain, understanding vulnerability impact, and preparing remediation recommendations.

The skills gained from this lab provide a practical foundation for further learning in web application security, vulnerability assessment, penetration testing, SOC operations, and security monitoring.
