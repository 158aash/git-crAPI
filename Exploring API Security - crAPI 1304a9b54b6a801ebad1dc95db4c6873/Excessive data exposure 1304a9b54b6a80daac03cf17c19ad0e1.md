# Excessive data exposure

**Excessive Data Exposure** is a security vulnerability that occurs when an application or API returns more data than is necessary or intended, often revealing sensitive information to users who shouldn't have access to it. This vulnerability typically arises due to a lack of filtering or access controls in server responses, making it possible for users to view sensitive fields that aren't needed for their request.

### How Excessive Data Exposure Works:

APIs are often designed to return data in a structured response, such as JSON or XML. In cases of excessive data exposure, the API might return all fields in an object, even those that include sensitive information, instead of limiting the response to only the fields necessary for the client.

API requests of interest include user accounts, forum posts, social media posts, and information about groups (like company profiles).

Ingredients for excessive data exposure:

- A response that includes more information than what was requested
- Sensitive Information that can be leveraged in more complex attacks

Exploring the community forum of the crAPI should lead us to viewing various posts by the users and their usernames and comments. 

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image.png)

However, when this page is forwarded to the BurpSuit proxy, it is found that the Email IDs and Post IDs of users are also obtained.

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%201.png)

The contact mechanic page should give us the list of mechanics available for raising a request to the mechanics along with a provision to describe the issues. However, it gives us the Email IDs of the mechanics as well. 

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%202.png)

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%203.png)

When products are bought, the orders page gives a list of the products purchased

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%204.png)

When any item is requested for return, it displays a QR code to scan in order to return it, however when the POST request is explored further, it is found that it reveals more information apart from the message and QR code, like, User, product and order details which is excessive data exposure.

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%205.png)

![image.png](Excessive%20data%20exposure%201304a9b54b6a80daac03cf17c19ad0e1/image%206.png)

To mitigate **Excessive Data Exposure** in APIs:

### 1. **Enforce Output Filtering at the API Layer**

Ensure that only necessary data fields are included in API responses by applying output filtering at the API level.

**Implementation**: Use API filtering mechanisms to limit the response to only the data required for each endpoint, thereby minimizing the risk of exposing sensitive or excessive information.

Tools: **GraphQL, Kong API Gateway, AWS API Gateway, Apigee**

### 2. **Implement Data Access Governance**

Use data access policies and governance controls to ensure that only authorized users can access sensitive data through APIs.

**Implementation**: Implement role-based or attribute-based access controls to manage who can access what data, integrating with identity management solutions to enforce data governance.

Tools: **Okta, Azure Data Lake Storage, Immuta, BigID**

### 3. **Adopt Data Minimization Techniques in API Design**

Design APIs with principles of data minimization, limiting responses to the minimum data necessary to fulfill the request.

**Implementation**: Define API responses with specific fields using OpenAPI specifications, ensuring only essential data is returned in responses, preventing inadvertent exposure of sensitive information.

Tools: **Swagger/OpenAPI, Postman, Insomnia, AWS Lambda**

### 4. **Use Runtime Application Self-Protection (RASP) for Real-Time Monitoring**

Employ RASP solutions to monitor and prevent excessive data exposure by analyzing requests and responses in real-time.

**Implementation**: Use RASP tools to detect unusual data exposure patterns and prevent unauthorized access or excessive data exposure incidents in API responses.

Tools: **Contrast Security, Imperva RASP, Sqreen, Signal Sciences**