# Broken Object Level Authorization

**Broken Object Level Authorization (BOLA)** is a common security vulnerability in web applications, particularly with REST APIs, where proper access control checks are not enforced when users try to access objects they don't have permissions for. This can lead to unauthorized access to data or resources, posing significant security risks.

### How BOLA Works:

In applications, users often interact with various objects (e.g., user profiles, orders, documents) identified by unique IDs in URLs or API requests. BOLA occurs when the application doesn’t verify whether a user is authorized to access a specific object ID, allowing an attacker to manipulate object IDs to access or modify resources that belong to other users.

Exploring crAPI, we can find parts of the app that are specific to a user's account include:

- The user dashboard with the user's vehicle added
- The user's profile
- The user's past orders in the shop
- The user's posts in the community forum

We could get excessive data by making a GET request to the `/community/api/v2/community/posts/recent` endpoint. The response shows posts from multiple users, including information about the author such as `nickname`, `email`, and `vehicleid`. This response indicates that the API does not restrict which posts a user can view, potentially exposing data of other users without proper authorization checks.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image.png)

Case 1: Changing the Post ID in GET request 

In order to check the post of user 1 , we collect the GET request and access a specific post directly by using its `post_id` in the `/post` endpoint. Here, the API retrieves the post details of another user, showing the title, content, author’s nickname, email, vehicle ID, and creation date. This direct access without authorization validation allows you to view another user's post details, highlighting the BOLA vulnerability.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%201.png)

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%202.png)

If we change the Post ID of the user from POST 1 to ID of post 2, the data of the user 2 is revealed by manipulating the GET request by changing the User ID. 

This again demonstrates that the API does not enforce object-level authorization checks, as it allows one user to access another user’s post data simply by knowing the post ID.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%203.png)

Case 2: Changing the order ID in the order GET request

We access the `/orders` endpoint to view order details using the `order_id` parameter. The response reveals billing information, including the email address and phone number associated with the order. This sensitive information is exposed without checking if the requesting user is authorized to view this particular order, showcasing the BOLA vulnerability.
We can obtain the order details of the Seat purchased by providing the order ID in the GET request.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%204.png)

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%205.png)

However if the order ID is changed, it can reveal the order of a wheel as well using the same GET request used by the Seat order.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%206.png)

The response contained detailed order information, including personal data of the user associated with that order. This again confirms that the API fails to verify user permissions for accessing specific orders, allowing unauthorized data exposure.

![image.png](Broken%20Object%20Level%20Authorization%201304a9b54b6a80e596fdef9343490474/image%207.png)

To mitigate Broken Object Level Authorization (BOLA) vulnerabilities in APIs:

### 1. **Implement Object-Level Access Control (OLAC)**

Ensure that access to each object (e.g., user data, orders, posts) is restricted to only those users who are authorized to view or modify it. This involves enforcing access control checks at the object level for every endpoint that exposes user-specific data.

**Implementation**: Use role-based and attribute-based access control mechanisms to verify that each user has the necessary permissions before granting access to specific resources. This can be managed with frameworks or middleware that enforce these rules consistently across the API.

Tools: **Auth0, AWS IAM, Kong API Gateway, Curity and WSO2**

### 2. **Use Secure API Design Patterns with Least Privilege**

Design APIs to adhere to the principle of least privilege, where each user is granted only the minimal level of access needed to perform their tasks. Avoid exposing endpoints that allow users to access objects outside their scope.

**Implementation**: Restrict endpoints to serve only necessary data and consider implementing separate endpoints for different roles or user types (e.g., admin vs. regular user). Also, avoid allowing direct access to resources by unique IDs in the URL unless absolutely necessary.

Tools: **Postman, OpenAPI/SwaggerAWS ,API Gateway and Azure API Management**

### 3. **Adopt Strong Authentication and Token-Based Authorization (JWT/OAuth)**

Use robust authentication (e.g., OAuth 2.0 with JSON Web Tokens) to securely identify users and ensure that each request is made on behalf of an authenticated user. Incorporate claims within the token to validate user permissions at runtime.

**Implementation**: Include relevant user and role claims within JWTs to allow the API to validate authorization for each request based on the user’s permissions. This allows the API to enforce object-level checks efficiently by validating the user's token and claims.

Tools: **Okta, Auth0, Firebase [Authentication , Keycloak, JWT.io](http://AuthenticationKeycloakJWT.io) and Ping Identity**

### 4. **Perform Regular Security Testing and Code Reviews**

Conduct routine security testing, including penetration tests, vulnerability scans, and code reviews, to identify and address BOLA vulnerabilities before they can be exploited. Ensure security is an ongoing process, integrated into the software development lifecycle.

**Implementation**: Use automated security testing tools (like OWASP ZAP or Burp Suite) during development and staging environments to catch BOLA vulnerabilities. Regularly review code for authorization checks and enforce security best practices through continuous integration/continuous deployment (CI/CD) pipelines.

Tools: **OWASP ZAP , Burp Suite, Veracode, Checkmarx and Postman API Security**