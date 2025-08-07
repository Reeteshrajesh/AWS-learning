# AWS API Gateway - Core Concepts (DevOps-Focused)

## Overview

Amazon API Gateway is a fully managed service that allows developers to create, publish, maintain, monitor, and secure APIs at any scale. It acts as a "front door" for applications to access data, business logic, or functionality from your backend services (e.g., Lambda, EC2, HTTP, VPC).

---

## 1. **REST API vs HTTP API vs WebSocket API**

| Feature            | REST API                            | HTTP API                    | WebSocket API                         |
| ------------------ | ----------------------------------- | --------------------------- | ------------------------------------- |
| Best For           | Full-featured API with fine control | Simpler, lower-latency APIs | Real-time bidirectional communication |
| Lambda Integration | ✅ Yes                               | ✅ Yes                       | ✅ Yes                                 |
| AWS Service Proxy  | ✅ Yes                               | ❌ No                        | ❌ No                                  |
| OpenAPI Support    | ✅ Full                              | ✅ Limited                   | ❌                                     |
| Authentication     | ✅ Cognito, IAM, Custom              | ✅ JWT, IAM                  | IAM                                   |
| Cost               | Higher                              | Lower                       | Depends                               |

---

## 2. **Core Components**

### a. **Resources**

* Define the URL structure (e.g., `/users`, `/orders`)
* Each resource can have methods like GET, POST, DELETE, etc.

### b. **Methods**

* HTTP verbs associated with each resource (GET, POST, etc.)
* Can be integrated with:

  * AWS Lambda
  * HTTP backends
  * AWS services (via AWS Service Proxy)

### c. **Stages**

* Different environments (e.g., `dev`, `prod`, `qa`)
* Each stage has its own URL endpoint
* Support for stage variables (e.g., version, Lambda alias)

### d. **Integrations**

* Type of backend connected to the method:

  * **Lambda Integration** (most common for serverless)
  * **HTTP Integration** (external endpoints)
  * **AWS Service Proxy** (e.g., S3, SNS, DynamoDB)

### e. **Mappings**

* Request Mapping:

  * Modify/transform incoming request data before passing to backend
  * Useful for input validation, header rewriting

* Response Mapping:

  * Modify backend response before sending it to the client
  * Customize error messages, format conversions

---

## 3. **Security & Access Control**

### a. **Authorization Types**

* **IAM-based**: Secure internal APIs with AWS IAM roles and policies
* **Cognito User Pools**: Built-in authentication for user-based auth
* **Lambda Authorizers**: Custom logic (JWT, OAuth2, etc.)
* **API Keys**: Throttle/track API usage per client

### b. **Throttling & Quotas**

* Control abuse and ensure fair use
* Configurable per API stage and per API key

### c. **Resource Policies**

* Define who can invoke your APIs (like S3 bucket policies)
* Example: Allow requests from specific IP ranges

---

## 4. **Monitoring & Logging**

* **CloudWatch Metrics**:

  * 4xx, 5xx error count
  * Integration latency
  * Cache hit/miss ratio

* **CloudWatch Logs**:

  * Enable detailed execution logs
  * Useful for debugging integrations

* **X-Ray Tracing**:

  * Visualize API call flow (only for Lambda integrations)

---

## 5. **DevOps Use Cases**

* Secure access to internal Lambda-powered microservices
* Proxy API to on-prem or legacy services via VPC link
* Public APIs for mobile/web apps
* Webhook receiver with transformation logic
* CI/CD deployments per stage using tools like CodePipeline

---

## 6. **Diagram: API Gateway Request Flow**

```
Client ---> API Gateway ---> [Authorization] ---> [Throttling/Quota] ---> [Integration: Lambda/HTTP] ---> Response Mapping ---> Client
```
