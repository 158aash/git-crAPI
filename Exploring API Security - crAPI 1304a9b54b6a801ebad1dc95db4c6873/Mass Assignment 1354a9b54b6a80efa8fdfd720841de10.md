# Mass Assignment

**Mass Assignment** is a security vulnerability in applications and APIs that occurs when an application automatically binds incoming data to model attributes without properly validating or filtering the input. This allows attackers to modify sensitive fields that they are not supposed to access, leading to unauthorized changes in the application's data. Mass Assignment vulnerabilities typically arise when developers expose model attributes directly to user input without restricting which fields can be modified, enabling attackers to manipulate critical information, such as user roles, account statuses, or permissions.

### How Mass Assignment Works:

In applications, when creating or updating an object, user inputs are often mapped to model attributes using frameworks that support mass assignment (e.g., assigning values to multiple fields in one step). However, if all fields are exposed and not filtered, attackers can inject additional parameters into their requests to change fields they shouldn’t have access to. For example, in a user profile update request, an attacker could include fields like `isAdmin` or `accountBalance` and potentially escalate their privileges or alter critical account data.

To exploit Mass Assignment, an attacker typically sends a request with extra parameters, hoping the application will accept and apply those fields to its internal models. For instance, an attacker might include `"role": "admin"` in a profile update request, granting themselves unauthorized admin-level access if the application is vulnerable.

Properly addressing Mass Assignment involves configuring the application to allow only specific attributes to be updated by users (known as “whitelisting” or using "safe attributes"), ensuring that sensitive fields are excluded from automatic assignment.

Key elements of Mass Assignment:

- **Lack of field filtering** in backend mapping, leading to exposed or modifiable properties
- **Sensitive fields**—like roles, permissions, or user status—that can be leveraged for privilege escalation
- **Controls** such as whitelisting permissible fields and enforcing role-based access

**Challenge 8 - Get an item for free**

This shows the user’s **Available Balance** as **$70** and various items available for purchase, each priced at $10. We will attempt to manipulate these values using mass assignment techniques to exploit this balance.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image.png)

The Wheel is purchased at **$10 each and t**he user has options to view **Order Details** or **Return** the item.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%201.png)

This provides specific details about an order for a **wheel**. It includes information such as the **Billed To** email, **Phone**, **Unit Price**, **Quantity**, and **Total**. This detailed view shows that there are fields like `price`, `quantity`, and `status` that could potentially be modified via mass assignment if they are not protected. 

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%202.png)

After purchasing an item (presumably a wheel), the **Available Balance** has decreased from **$70 to $60**

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%203.png)

This screenshot shows a **GET request** to `/workshop/api/shop/orders/54` in BurpSuit, retrieving details about a specific order. The server’s **Response** allows methods such as **GET, POST, PUT, HEAD, and OPTIONS**, which could permit an attacker to perform a PUT request to modify fields.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%204.png)

We will explore the **order status** as **return pending** in the response of the **GET** request. We can modify this request to change and alter the Available Balance.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%205.png)

A **PUT request** to `/workshop/api/shop/orders/54` with a modified **status** field set to `"returned"`.

This shows an actual exploitation of the mass assignment vulnerability. By directly modifying the `status` parameter, the attacker could mark the item as returned, potentially triggering a refund or credit without returning the item. This manipulation highlights the risks of unprotected model attributes that can be mass-assigned.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%206.png)

The attacker successfully manipulated the order status using mass assignment. The application has marked the item as returned, demonstrating how altering the `status` field via mass assignment can achieve unauthorized actions, like falsely marking items as returned to receive refunds.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%207.png)

The **Available Balance** is back to **$70** after exploiting the mass assignment vulnerability to falsely return an item.

By setting the status to "returned," the application has credited the balance back to $70, effectively giving the attacker a refund without returning the item. This shows the financial impact that a mass assignment vulnerability can have, especially if an attacker repeatedly exploits it.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%208.png)

**Challenge 9 - Increase your balance by $1,000 or more**

This shows the user’s available balance as `$60` on a web interface with items for purchase. This balance will  be manipulated through the mass assignment vulnerability.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%209.png)

Two ways to do it, First:

In a typical mass assignment attack, the attacker crafts a custom request  to manipulate attributes they shouldn’t be able to control. Here, by setting the quantity to an abnormally high value and marking the status as `returned`, the attacker aims to exploit the refund process to their advantage.

The response in the screenshot confirms that the request was accepted and processed, showing that the system has allowed an unauthorized change to the order quantity.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2010.png)

The user’s available balance has increased significantly, now showing `$1060`. This reflects the success of the manipulation, where the application has issued a refund based on the manipulated quantity.

The application likely calculates refunds based on the `quantity` field, and due to the lack of proper attribute filtering, the attacker managed to increase their balance significantly.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2011.png)

Second attack:

The balance shows `$1060`, indicating the initial balance of the user in the shopping application.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2012.png)

This screenshot shows a **GET request** to `/workshop/api/shop/products` in BurpSuit, retrieving details about a specific order. The server’s **Response** allows methods such as **GET, POST, HEAD, and OPTIONS**, which could permit an attacker to perform a PUT request to modify fields.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2013.png)

A `POST` request to `/shop/products` with JSON data attempting to set the price of a product (`Wheel`) to `-1000.00`.
By setting a negative price, the attacker aims to trick the application into giving them a balance increase every time they "purchase" this item. If accepted, this manipulation would cause the application to add $1000 to the user's balance with each transaction, as opposed to deducting it.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2014.png)

The balance is still at `$1060`, and the product (`Wheel`) now displays a price of `-$1000.00`.
This confirms that the application accepted the negative price input for the product. The user can now attempt to purchase the product to increase their balance further due to the negative pricing. This highlights a severe validation flaw in the application, allowing arbitrary field modifications.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2015.png)

A purchase confirmation for the `Wheel` priced at `-$1000.00`, and the balance has now increased to `$2060`.

By purchasing the negatively priced item, the user has successfully manipulated the application to increase their balance instead of decreasing it. The balance went from $1060 to $2060, confirming the exploit worked.

![image.png](Mass%20Assignment%201354a9b54b6a80efa8fdfd720841de10/image%2016.png)

There are mitigations for preventing mass assignment vulnerabilities, along with industry-standard tools and techniques as of 2024:

---

### 1. **Attribute Whitelisting and Field Validation**

Limit mass assignment to only allow specific, safe fields to be updated by users. By explicitly specifying which fields are allowed (whitelisting), you prevent attackers from manipulating sensitive attributes.

- **Implementation**: Frameworks like **Rails** (using `strong parameters`), **Spring** (with custom validators), and **Express.js** (middleware validation) support attribute whitelisting natively.
- **Industry Tools**: Use validation libraries like **Joi** (JavaScript), **Cerberus** (Python), and **Validator.js** to set strict field-level validation rules.

**Why It’s Important**: Whitelisting restricts access to sensitive fields, ensuring attackers cannot add unauthorized fields to their requests, which is crucial for protecting sensitive data like roles and account balances.

---

### 2. **Role-Based Access Control (RBAC) and Attribute-Based Access Control (ABAC)**

Implement RBAC to assign users specific roles with limited access to sensitive actions, and use ABAC for more granular access control based on attributes like user location or session.

- **Implementation**: Enforce RBAC with frameworks like **Spring Security** and **Django**. **ABAC** can be implemented with policy engines such as **OPA** (Open Policy Agent).
- **Industry Tools**: **Auth0**, **Okta**, and **Keycloak** provide robust RBAC and ABAC solutions that integrate seamlessly with APIs and web applications.

**Why It’s Important**: Proper access control helps ensure that users can only modify data relevant to their roles and permissions, preventing unauthorized access and manipulation of sensitive fields.

---

### 3. **Use of ORM (Object-Relational Mapping) Security Features**

Leverage ORM frameworks that provide built-in protections against mass assignment vulnerabilities by enforcing validation at the model level and restricting mass updates.

- **Implementation**: Use ORM security features like `ActiveRecord` in **Rails**, `Hibernate` in **Java**, and **SQLAlchemy** in Python.
- **Industry Tools**: Modern ORM tools (e.g., **Sequelize**, **TypeORM**) support attributes like `allowNull`, `unique`, and `defaultValue` for setting up controlled field permissions at the model level.

**Why It’s Important**: ORM security controls prevent unauthorized fields from being modified during database transactions, ensuring that data integrity is maintained across sensitive fields.

---

### 4. **Web Application Firewall (WAF) with Mass Assignment Rules**

Deploy a WAF to detect and block requests containing unauthorized parameters that may exploit mass assignment vulnerabilities.

- **Implementation**: Configure WAFs to monitor for unusual request payloads and enforce input filtering rules.
- **Industry Tools**: **Cloudflare WAF**, **AWS WAF**, and **Imperva WAF** support custom rule sets for blocking requests with suspicious parameters and are commonly used to prevent injection and mass assignment attacks.

**Why It’s Important**: A WAF provides an additional layer of security by blocking potential mass assignment exploitation attempts, especially when configured with rules to monitor for unexpected fields in requests.

---

### 5. **Comprehensive API Security Gateway**

Use an API gateway to centrally enforce security controls like payload validation, token verification, and rate limiting to mitigate mass assignment attacks at the API level.

- **Implementation**: API gateways can enforce request validation, field-based access control, and authentication before requests reach the backend.
- **Industry Tools**: **Apigee**, **Kong Gateway**, and **AWS API Gateway** provide security features that can prevent unauthorized field manipulation in API requests.

**Why It’s Important**: An API gateway enforces security policies at the API layer, minimizing the risk of mass assignment attacks by validating request structures and attributes.