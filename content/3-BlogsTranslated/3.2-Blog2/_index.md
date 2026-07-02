---
title: "Blog 2"
date: 2026-06-16
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Building a secure B2C application with Amazon Cognito and Amazon Verified Permissions

In modern B2C applications, user management is not only about authentication but also about controlling access to specific resources.

Typically, systems must handle two core problems:

- **Authentication:** Who is the user?
- **Authorization:** What is the user allowed to do?

AWS proposes combining **Amazon Cognito** and **Amazon Verified Permissions** to separate these two concerns, making the system clearer and more scalable.

---

## Problems with the traditional approach

In many applications, authorization logic is often written directly in the code:

- Difficult to scale when the number of roles increases
- Hard to maintain as rules become more complex
- Authorization logic becomes scattered across the application

As the system grows, this approach becomes less efficient and harder to manage.

---

## AWS proposed solution

AWS introduces a decoupled architecture:

- **Amazon Cognito:** handles user authentication and issues JWT tokens
- **Amazon Verified Permissions (AVP):** evaluates access control policies based on authorization rules

Instead of embedding authorization logic in code, policies are defined using the **Cedar language**, and AVP evaluates them at runtime.

---

## System workflow

**(Insert image here: Architecture Diagram)**  
*Figure 1: Cognito and Verified Permissions flow*

The basic flow is as follows:

1. User signs in via Amazon Cognito  
2. Cognito issues a JWT token  
3. Application sends a request with the token  
4. Backend calls Amazon Verified Permissions  
5. AVP evaluates the policy  
6. Returns an Allow / Deny decision  

---

## Supported authorization models

This solution supports multiple common authorization patterns:

- **Resource-based access:** users can only access their own resources  
- **Role-based access (RBAC):** permissions are assigned based on roles  
- **Hierarchical access control:** permissions are inherited across organizational levels  
- **Explicit deny:** deny rules always take priority  
- **Admin override:** administrators can override standard access rules  

---

## Key benefits

This architecture provides several important benefits:

- Clear separation of authentication and authorization  
- Reduced authorization logic inside application code  
- Easier policy updates without redeploying the system  
- Improved auditability and centralized access control  
- Suitable for B2C and SaaS applications with large user bases  

---

## AWS services overview

### Amazon Cognito
- Pricing is based on **Monthly Active Users (MAU)**  
- Costs scale with the number of active users  

### Amazon Verified Permissions
- Pricing is based on the number of policy evaluation requests  
- Higher request volume leads to higher cost  

- Small systems: low cost  
- Large systems: requires optimization of AVP calls to control cost  

---

## Testing and evaluation

### 1. Comparison with code-based authorization

| Criteria | Code-based | Cognito + AVP |
|----------|------------|---------------|
| Maintainability | Low | High |
| Scalability | Limited | High |
| Auditability | Poor | Strong |
| Rule updates | Requires redeployment | No redeployment needed |

---

### 2. Comparison with IAM

- IAM is more suitable for system-to-system access control  
- AVP is designed for application-level (B2C) authorization  

---

### 3. Testing approach

This solution can be tested in multiple ways:

- Unit testing for Cedar policies  
- Role-based testing (Admin/User/Guest)  
- Integration testing between Cognito → AVP → backend  
- Scenario-based testing for real-world use cases  

---

## Conclusion

Combining **Amazon Cognito** and **Amazon Verified Permissions** enables a modern and scalable B2C security architecture.

> It clearly separates authentication and authorization, making the system easier to maintain and extend.

However, cost optimization should be considered when the system has a high volume of authorization requests.

---

## References

https://aws.amazon.com/blogs/security/building-secure-b2c-applications-with-fine-grained-access-control-using-amazon-cognito-and-amazon-verified-permissions/