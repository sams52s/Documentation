## 🔐 What is OAuth 2.0?

OAuth 2.0 is a framework that lets applications access resources on behalf of a user without needing their password.

### 🧑‍🤝‍🧑 Key Roles:

- **Resource Owner** – the user
- **Client** – your app
- **Authorization Server** – issues tokens (e.g., Google)
- **Resource Server** – your API (validates tokens)



---

## 🧭 OAuth 2.0 Grant Types

### 🔄 1. Authorization Code (with PKCE)

Best for user logins from browsers and mobile apps.

```text
Client → Authorization Server (code) → Access Token → Resource Server
```



### 🤝 2. Client Credentials

Used for server-to-server API calls (no user involved).

### 🔁 3. Refresh Token

Allows a client to renew an expired access token without logging in again.



---

## 📥 Sample Configuration in Spring Boot

### ✅ application.yml for GitHub Login

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          github:
            client-id: YOUR_ID
            client-secret: YOUR_SECRET
            scope: read:user
        provider:
          github:
            authorization-uri: https://github.com/login/oauth/authorize
            token-uri: https://github.com/login/oauth/access_token
            user-info-uri: https://api.github.com/user
```

### ✅ Security Setup

```java
http
  .authorizeRequests(auth -> auth.anyRequest().authenticated())
  .oauth2Login();
```

---

## 🛡️ Best Practices for OAuth 2.0

| ✅ Practice                         | 🛡️ Why it matters                    |
| ---------------------------------- | ------------------------------------- |
| Always use HTTPS                   | Prevents token hijacking              |
| Use PKCE for public clients        | Protects against interception attacks |
| Strict redirect URI validation     | Prevents token/code leakage           |
| Use `state` param                  | Protects against CSRF attacks         |
| Keep token scope narrow            | Least privilege principle             |
| Enable short access token TTL      | Tokens should expire in minutes       |
| Use refresh tokens (with rotation) | Keeps sessions alive securely         |

---

## 🧪 Testing OAuth Integration

Use [Postman](https://postman.com) or [ReqBin](https://reqbin.com) to simulate authorization flows. Great for debugging token exchanges and calling secured APIs.

---

## ⚙️ Authorization vs Resource Server

| Role                 | Responsibility                           | Spring Boot Tooling                    |
| -------------------- | ---------------------------------------- | -------------------------------------- |
| Authorization Server | Issues tokens after authenticating users | Spring Authorization Server            |
| Resource Server      | Validates tokens, serves protected data  | spring-security-oauth2-resource-server |



---

## 📚 References & Tools

- [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749) – OAuth 2.0 spec
- [Spring Security OAuth2 Docs](https://spring.io/guides/tutorials/spring-boot-oauth2/)
- [OAuth 2.1 Draft](https://oauth.net/2.1/)
- [Okta Blog: Secure OAuth](https://developer.okta.com/blog/2019/10/21/what-the-heck-is-oauth)
- [JWT.io](https://jwt.io)
