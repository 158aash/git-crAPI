# Broken User Authentication

**Broken User Authentication** is a security vulnerability in applications and APIs where mechanisms intended to verify and validate a user’s identity are flawed or poorly implemented, allowing attackers to bypass authentication controls and impersonate legitimate users. Broken User Authentication typically occurs due to weak password policies, inadequate session management, or improperly implemented authentication protocols, enabling unauthorized access to user accounts or sensitive data.

### How Broken User Authentication Works:

Applications rely on authentication mechanisms to establish a user’s identity before granting access to protected resources. Broken User Authentication vulnerabilities arise when these mechanisms—such as login forms, password reset processes, or session tokens—are weak or misconfigured. This can include issues like allowing brute-force login attempts, using default or weak passwords, insecure storage of authentication tokens, or failing to expire sessions. Attackers can exploit these weaknesses to gain unauthorized access, impersonate users, or hijack sessions, compromising the security and integrity of user accounts.

Key elements of Broken User Authentication:

- Weak password policies, allowing for easy brute-force attacks
- Improper session management, such as not expiring tokens after logout
- Use of default or easily guessable passwords
- Insecure storage or handling of authentication tokens

**Challenge 3 — Reset the password of a different user.**

In order to test for setting a new password, we generate an OTP and check the MailHog server http:127.0.0.1:8025/ for the OTP email. 

MailHog is a local email testing tool often used to capture and view outbound emails during development and testing.

This step verifies if the OTP sent by the CRAPI API is accessible and can be intercepted by an unauthorized party. If you see the OTP email in MailHog, you can proceed with password reset actions.

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image.png)

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%201.png)

The OTP is set and the email is verified and the reset process is successful, as indicated by the success message, while this request is intercepted in BurpSuit for further analyses

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%202.png)

The POST request uses version v3 and the shows a failed OTP verification attempt with a `503` status and a message indicating the exceeded number of attempts.

By repeatedly entering OTPs, we are testing if the API has rate limiting on OTP submissions to prevent brute-forcing.

The `503` response suggests that rate limiting is in place, preventing excessive attempts to verify the OTP. This is a positive indication, as it limits brute-force attacks. However, if rate limiting can be bypassed by exploring various versions of the POST request, this could still be exploited.

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%203.png)

In this Burp Suite Intruder setup, we are configuring an automated attack to brute-force the OTP field with the POST request version v2  by iterating through possible OTP values.

The purpose here is to determine if the API restricts the number of OTP attempts for v2, potentially allowing an attacker to guess the correct OTP through brute-force methods.

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%204.png)

This shows the payload settings configured to sequentially attempt OTP values from `0000` to `9999`, simulating a brute-force attack.

This setup tests the API’s resilience to brute-force attacks by iterating through all possible 4-digit OTP combinations.

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%205.png)

The results show various status codes, with one successful `200 OK` response for the OTP `2511`, confirming a valid OTP.

 This indicates that after brute-forcing the OTP by changing the version in the request, the correct OTP was eventually found, allowing unauthorized password reset.

If `v3` has rate limiting or stricter validation on OTP verification, but `v2` lacks these measures, this inconsistency introduces a vulnerability where attackers can intentionally use outdated API versions to circumvent security controls. This is a common issue in APIs when legacy versions are still accessible but not adequately maintained or secured.

![image.png](Broken%20User%20Authentication%201354a9b54b6a80e2b0faf7bd9583d5af/image%206.png)

To protect against Broken User Authentication vulnerabilities in APIs:

### 1. **Multi-Factor Authentication (MFA)**

Multi-Factor Authentication requires users to provide two or more verification factors to gain access to their accounts. This typically includes something the user knows (password), something the user has (a one-time code from an authenticator app or SMS), or something the user is (biometric verification).

**Implementation:** MFA can be enforced on sensitive actions like login, password reset, and account recovery, ensuring an extra layer of security even if one factor is compromised.

**Tools**: Common MFA providers include Google Authenticator, Microsoft Authenticator, and Authy, and MFA is often integrated through identity and access management (IAM) platforms such as Okta or Auth0.

### 2. **Comprehensive Rate Limiting and Lockout Mechanisms**

Rate limiting restricts the number of authentication attempts within a given timeframe to prevent brute-force attacks. Additionally, account lockouts can temporarily or permanently lock a user’s account after several failed login attempts, protecting against automated attacks.

**Implementation:** Apply rate limiting and lockouts on login attempts, OTP submissions, and password reset requests. These limits should be adaptive, based on user behavior and risk assessment.

**Tools**: Most API management platforms (e.g., Kong, Apigee, AWS API Gateway) offer built-in rate limiting features. Additionally, Web Application Firewalls (WAFs) like AWS WAF or Cloudflare WAF provide rate limiting configurations.

### 3. **Secure Token Management and Session Expiry**

Proper session management includes using secure, short-lived tokens (such as JSON Web Tokens - JWT) with strict expiration policies. Sessions should expire after a period of inactivity or upon logout, and refresh tokens should be used for re-authentication.

**Implementation**: Use secure storage for session tokens, ensure tokens are invalidated after logout, and use refresh tokens with expiration policies to prevent unauthorized long-term access.

**Tools**: Implementing session management and token security is commonly done through IAM platforms or API gateways that support secure token standards (e.g., OAuth 2.0 with JWT).

### 4. **Strong Password Policies and Credential Checks**

 Implement strong password policies to require complex passwords that are difficult to guess. Additionally, check for compromised credentials by comparing user passwords with known breached password lists and requiring users to reset compromised passwords.

**Implementation**: Enforce policies for minimum password length, complexity (uppercase, lowercase, numbers, special characters), and regular password expiration. Also, check against compromised credential databases.

**Tools**: Identity management solutions (e.g., Okta, Auth0) provide customizable password policies, and integration with compromised credential databases is available through APIs.