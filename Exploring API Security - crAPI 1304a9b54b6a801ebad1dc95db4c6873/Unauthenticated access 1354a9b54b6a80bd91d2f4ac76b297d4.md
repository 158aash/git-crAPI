# Unauthenticated access

**Unauthenticated Access** is a security vulnerability where a system, application, or API fails to enforce authentication, allowing users to access certain resources or functionalities without verifying their identity. This lack of enforced authentication makes the system susceptible to unauthorized access and can expose sensitive data or functionalities to anyone, including potential attackers.

### How Unauthenticated Access Works:

In secure applications, authentication mechanisms ensure that users prove their identity (e.g., through login credentials) before accessing resources. In cases of unauthenticated access, these mechanisms may be missing or improperly configured for specific endpoints, actions, or data, allowing anyone to bypass authentication checks and directly access sensitive resources.

For instance, if a public URL endpoint for accessing user account information does not enforce authentication, an attacker can directly access it and retrieve data without needing a valid user session.

Key elements of Unauthenticated Access:

- **Lack of Authentication Checks** allows open access to sensitive endpoints or functionalities.
- **Access Control Weaknesses** enable anyone to interact with resources that should be restricted.

This shows a list of past orders for a user, each item has an “Order Details” button, which allows users to view more information about each specific order. We query this using BurpSuit and intercept the Order Details page

![image.png](Unauthenticated%20access%201354a9b54b6a80bd91d2f4ac76b297d4/image.png)

**The GET request** to the `/workshop/api/shop/orders/59` endpoint is intercepted through Burp, which fetches details for the order with ID `59`. 

The `Authorization` header in this request includes a **Bearer token**, which is typically used for authenticated access to confirm the identity of the user.

The response from the server (on the right side of the image) contains JSON data about the order, including the user’s email (`test@gmail.com`), user ID, product information, and price.

This implies that, in a properly secured environment, an authenticated token is necessary to access this data.

![image.png](Unauthenticated%20access%201354a9b54b6a80bd91d2f4ac76b297d4/image%201.png)

 The request to the `/workshop/api/shop/orders/59` endpoint is repeated without the authorization header.

Despite the missing authentication, the server still responds with a **200 OK** status and provides the order details in the response body.

This response indicates a **vulnerability**: the API endpoint is accessible without a valid authentication token, meaning anyone can access order details if they know the order ID.

The JSON response still includes sensitive information such as the user’s email and product details, which should be protected by authentication controls.

![image.png](Unauthenticated%20access%201354a9b54b6a80bd91d2f4ac76b297d4/image%202.png)

In an enterprise-level setting, mitigating unauthenticated access vulnerabilities requires a combination of strong authentication, role-based access control, and modern security practices. 

1. **Use OAuth 2.0 and OpenID Connect (OIDC) for Authentication and Authorization**

OAuth 2.0 is an industry-standard protocol for authorization, while OpenID Connect is an identity layer on top of OAuth 2.0 that provides user authentication. When a user logs in, they receive a short-lived access token and a refresh token. The access token is used to access APIs securely, while the refresh token can request a new access token when needed, reducing the risk of long-lived tokens.

- All API requests should require a valid access token, which can be obtained only by authenticating through a secure Identity Provider (IdP) compliant with OAuth 2.0 and OIDC.
- Tokens should be scoped so they only grant necessary permissions, and they should include expiration times to minimize risks of misuse.
- This helps prevent unauthenticated access by ensuring only users who have passed identity verification can access sensitive resources.

**Industry Standard Tools**: Use well-supported identity management platforms like **Auth0**, **Okta**, **AWS Cognito**, or **Azure AD** that offer robust OAuth 2.0 and OIDC integrations.

2. **Implement Role-Based Access Control (RBAC) and Attribute-Based Access Control (ABAC)**
RBAC restricts access based on the user’s role within the organization (e.g., admin, user, guest). ABAC is an extension that grants access based on attributes of the user, resource, and environment (e.g., time, location, device).

- Define clear roles for users and restrict each role to access only what’s necessary for their functions. For example, a regular user should only access their own orders, not orders belonging to others.
- With ABAC, define additional rules to restrict access based on contextual data, such as requiring that a request originates from an internal network or a trusted device.
- Enforcing RBAC and ABAC mitigates unauthorized access by ensuring even authenticated users have access only to data relevant to their role and context.

**Industry Standard Tools**: **AWS IAM** for AWS environments, **Azure RBAC** for Azure, and **Open Policy Agent (OPA)** or **Kong Enterprise** for advanced, customizable access control.

3. **API Gateway and Centralized Access Management**

An API gateway acts as a single entry point for all API requests, enforcing security policies like authentication, authorization, rate limiting, and logging.

- Deploy an API gateway (such as **Kong**, **Apigee**, **AWS API Gateway**, or **Nginx**) to manage all API traffic.
- Configure the gateway to enforce OAuth 2.0 authentication, ensuring that only authenticated requests are forwarded to backend services.
- Enable additional security layers such as IP whitelisting, rate limiting, and content filtering to mitigate potential abuse.
- API gateways also provide centralized logging and monitoring, allowing security teams to detect and respond to suspicious activities in real-time.

### **Implement Zero Trust Architecture**

Zero Trust is a security model that assumes no entity, whether inside or outside the network, can be trusted by default. It requires continuous verification of all users and devices.

- Implement multi-factor authentication (MFA) for all users accessing sensitive resources. This provides an additional layer of verification beyond the password.
- Continuously validate identity, device posture, and context (e.g., location, IP address) before granting access.
- Use micro-segmentation to isolate sensitive parts of the application, ensuring that users and services can only access necessary resources.
- Zero Trust reduces risks associated with unauthenticated access by continually verifying and restricting access at every point within the network.

**Industry Standard Tools**: **Microsoft Azure Zero Trust**, **Google BeyondCorp**, and **Palo Alto Networks Zero Trust** solutions.