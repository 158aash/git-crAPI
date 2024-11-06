# Broken Function Level Authorization

**Broken Function Level Authorization (BFLA)** is a security vulnerability in applications and APIs where proper access controls are not enforced for specific functions or actions, allowing unauthorized users to perform privileged operations. BFLA occurs when an application fails to verify whether a user has the necessary permissions to access a particular function, often exposing sensitive actions to unprivileged users.

### How BFLA Works:

In applications, various functions (like updating a profile, managing users, or accessing administrative settings) are typically intended for users with specific roles or permissions. BFLA vulnerabilities arise when these roles and permissions are either misconfigured or not checked properly, allowing regular users to access or execute privileged functions.

For BFLA we can look for:

1. Resource ID: a resource identifier will be the value used to specify a unique resource.
2. Requests that perform authorized actions. In order to test if you can access another update, delete, or otherwise alter other the resources of other users.
3. Missing or flawed access controls. In order to exploit this weakness, the API provider must not have access controls in place.

**Challenge 7 - Delete a video of another user**

We attempt to change the name of a video in our account. The PUT request was sent to an endpoint like `/identity/api/v2/user/videos/107`, specifying the `videoName` parameter with a new name.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image.png)

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%201.png)

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%202.png)

An OPTIONS request is made to `/identity/api/v2/user/videos/107`, and the response shows allowed methods such as DELETE, PUT, and GET.

By sending an OPTIONS request, we are testing which HTTP methods are allowed on this endpoint. DELETE being allowed indicates that deleting resources (like videos) is possible.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%203.png)

Here, we sent a DELETE request to `/identity/api/v2/user/videos/107` with the intent of deleting the video. 
The response shows a `403 Forbidden` error, with a message indicating that only admins can perform this action.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%204.png)

We sent the same request by changing to admin user `/identity/api/v2/admin/videos/107.`The response shows a successful deletion with a `200 OK` status. This step verifies that you can delete videos within your own account using admin permissions.

The API correctly processes a DELETE request from an authenticated user to delete the video.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%205.png)

When you log back in again into the account, the video is deleted successfully:

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%206.png)

We test the same vunerability for another user account: test@gmail.com

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%207.png)

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%208.png)

Here, we change the endpoint to `/identity/api/v2/admin/videos/108` and resend the DELETE request. This time, the response is `200 OK`, indicating that the deletion was successful.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%209.png)

After performing the DELETE operation, we return to the user profile to confirm that the video with ID `108` has been removed.

![image.png](Broken%20Function%20Level%20Authorization%201314a9b54b6a8009ae60efc3b507a5a0/image%2010.png)

The  following mitigations are used to prevent Broken Function Level Authorization (BFLA) vulnerabilities in APIs:

### 1. **Role-Based Access Control (RBAC)**

RBAC restricts access to API endpoints based on user roles (e.g., user, admin, moderator). Each role has predefined permissions that dictate what functions it can perform, ensuring only authorized users can access sensitive functions.

**Implementation**: Define roles and assign permissions in the API, allowing only certain roles (like "admin") to access specific endpoints. Use middleware or an API gateway to check user roles and permissions for each request.

**Tools**: Identity management platforms like Okta, Auth0, or AWS IAM support RBAC configurations, making it easier to define and enforce role-based permissions.

### 2. **Attribute-Based Access Control (ABAC)**

 ABAC extends RBAC by considering additional attributes, such as the user’s department, location, or the resource’s owner. It uses a rules-based approach to evaluate access based on context, making it more flexible than traditional RBAC.

**Implementation**: Define policies that incorporate multiple attributes, such as resource ownership (allowing users to only modify their resources), user role, and other contextual attributes. Policies are enforced at the API level or within a gateway.

**Tools**: Platforms like AWS IAM and Open Policy Agent (OPA) can implement ABAC for APIs, offering fine-grained access control based on multiple attributes.

### 3. **Endpoint-Level Authorization Checks**

Each API endpoint should have dedicated authorization checks to ensure that users can only access resources they are authorized for. These checks confirm if the user has the correct permissions for each function, regardless of endpoint or URL modifications.

**Implementation**: Apply authorization checks within API business logic, ensuring that each function (e.g., DELETE, PUT) verifies the user’s access rights before executing. Use access control middleware or authorization layers to validate permissions consistently.

**Tools**: Integrate authorization libraries such as Casbin or implement custom middleware in frameworks like Django, Express, or Spring Boot to enforce endpoint-level access control.

### 4. **Use of API Gateways with Fine-Grained Access Control**

API gateways act as an intermediary, enforcing security policies and access controls before requests reach the API. They can manage authentication, rate limiting, and authorization at a central level, reducing direct exposure of sensitive endpoints.

**Implementation**: Configure the API gateway (such as Kong, Apigee, or AWS API Gateway) to enforce fine-grained access policies based on user roles, attributes, and ownership. The gateway checks permissions for each request, applying policies before routing the request to the API.

**Tools**: Leading API gateways like Kong, Apigee, and AWS API Gateway support advanced access control configurations, allowing enterprises to enforce fine-grained policies effectively.