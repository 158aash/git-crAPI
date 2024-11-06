# Rate Limiting

**Rate Limiting** is a security mechanism in applications and APIs that restricts the number of requests a user or client can make within a specified time period. By limiting request frequency, Rate Limiting helps prevent abuse of system resources, protects against brute-force attacks, and improves overall application security. Rate Limiting is commonly implemented in sensitive areas like login endpoints, OTP verifications, and API request handlers to control access and reduce the risk of malicious activity.

### How Rate Limiting Works:

Rate Limiting enforces a threshold on the number of requests allowed within a given timeframe (e.g., 10 requests per minute) for each user, IP address, or API key. When a user exceeds this limit, the system temporarily blocks further requests or returns an error, such as `429 Too Many Requests`. Rate Limiting can be applied at various levels—globally, per endpoint, or per user session—depending on the security needs of the application. This mechanism is particularly effective in preventing brute-force attacks, where attackers might attempt to guess credentials, OTPs, or tokens by sending repeated requests in quick succession. By limiting request rates, Rate Limiting mitigates abuse, reduces server load, and helps maintain fair resource access for legitimate users.

Key elements of Rate Limiting:

- **Threshold setting** to control request rates based on specific actions or endpoints
- **Response codes** like 429, indicating the user exceeded the rate limit
- **Logging and monitoring** to detect patterns of abuse or potential attacks

Analyzing the request to contact the mechanic is configured with`repeat_request_if_failed` as `false` and set `number_of_repeats` to `1`. The API returned a `200 OK` response, indicating successful handling of the request with no issues.

![image.png](Rate%20Limiting%201354a9b54b6a809c8e3cd5cffcc677e4/image.png)

In this case, `repeat_request_if_failed` is set to `true`, and `number_of_repeats` is set to `1000`.

The API returned a `503 Service Unavailable` response with a message hinting at a "Layer 7 DoS" (Denial of Service).

By sending a high volume of requests (1000) in a short period, we managed to trigger a denial of service scenario, causing the API to become unavailable. This outcome suggests that the API lacks effective rate limiting controls, allowing a single user to overload the service, potentially impacting other users.

![image.png](Rate%20Limiting%201354a9b54b6a809c8e3cd5cffcc677e4/image%201.png)

Since there is no rate limiting which is set for the requests in crAPI, we use the Intruder tool with `email` and `password` as parameters to test with a list of potential password values in a brute-force manner.

The attack type is set to `Sniper`, which allows targeting specific parameters like `password` for each request.

This setup demonstrates an attempt to brute-force login credentials without encountering any immediate rate limiting or lockout mechanisms. If the API allows this without limitations, it could expose user accounts to brute-force attacks.

![image.png](Rate%20Limiting%201354a9b54b6a809c8e3cd5cffcc677e4/image%202.png)

By sending these payloads sequentially to the API, we are testing whether it allows multiple login attempts without implementing any throttling or rate limiting. If rate limiting is absent, this brute force attack could be successful in guessing passwords, which is a significant security vulnerability.

![image.png](Rate%20Limiting%201354a9b54b6a809c8e3cd5cffcc677e4/image%203.png)

The results table indicates multiple `401 Unauthorized` responses, with a single `200 OK` response at the end for the correct password.

This suggests that the API did not limit the login attempts based on the IP or user session, allowing numerous tries until a valid password was found. The `200 OK` response confirms that the correct password was eventually guessed, highlighting the lack of rate limiting and exposing the API to brute-force attacks.

![image.png](Rate%20Limiting%201354a9b54b6a809c8e3cd5cffcc677e4/image%204.png)

Mitigations to protect APIs against rate-limiting and brute-force attacks:

### 1. **Rate Limiting and Throttling**

Rate limiting enforces a cap on the number of requests a client can make within a specific time period (e.g., 100 requests per minute). Throttling can dynamically adjust these limits based on demand and server load.

**Implementation**: Rate limiting is often implemented on a per-IP, per-user, or per-API key basis, with dynamic rate limiting allowing higher limits for trusted users or specific endpoints.

**Tools**: API gateways (such as Kong, Apigee, or AWS API Gateway) and reverse proxies (like NGINX) often have built-in rate limiting features.

### 2. **Captcha Integration for Sensitive Endpoints**

Adding CAPTCHA verification (e.g., reCAPTCHA or hCaptcha) to endpoints that handle sensitive actions, like login or account creation, helps block automated bot traffic while allowing legitimate users through.

**Implementation**: CAPTCHAs are typically presented after a certain number of failed login attempts or on repeated requests from the same IP.

**Tools**: Google reCAPTCHA, hCaptcha, and other CAPTCHA providers offer integrations that are easy to implement on API-driven applications.

### 3. **IP and Behavioral Anomaly Detection**

Behavioral analytics monitor usage patterns and detect anomalies such as high request rates, unusual IP geolocations, or abnormal request sequences.

**Implementation**: This is often achieved through AI-powered security solutions that can detect and block abnormal behavior patterns in real-time.

**Tools**: Security platforms like Cloudflare, AWS WAF, and Akamai provide bot detection and adaptive filtering, as well as anomaly-based detection mechanisms.