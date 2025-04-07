# 🔐 Complete Guide to OAuth 2.0 in Spring Boot

This guide covers both **beginner** and **advanced** topics to help you understand and implement OAuth 2.0 securely and effectively in Spring Boot applications.

---

## 🧭 Beginner-Level Basics

### 🚀 What is OAuth 2.0?

OAuth 2.0 is an authorization framework that lets applications access user data **without needing their password**. It allows **delegated access** via **secure tokens**.

### 🔐 Why Use OAuth?

- ✅ Delegated Access (on behalf of a user)
- ✅ Enhanced Security (no password sharing)
- ✅ Single Sign-On (SSO)
- ✅ Trusted 3rd-party authentication

---

### 🧑‍🤝‍🧑 Key OAuth 2.0 Terminology

| Term                | Description                                      |
|---------------------|--------------------------------------------------|
| **Resource Owner**  | The end user who owns the data                   |
| **Client**          | The app requesting access                        |
| **Authorization Server** | Verifies identity, issues tokens        |
| **Resource Server** | Hosts protected resources, validates tokens      |
| **Access Token**    | Token used to access APIs                        |
| **Refresh Token**   | Token to get a new access token without login    |

---

### 📚 OAuth 2.0 Grant Types Overview

| Grant Type                  | Use Case                             |
|-----------------------------|--------------------------------------|
| Authorization Code (PKCE)  | ✅ Recommended for most apps          |
| Client Credentials         | For server-to-server communication   |
| Resource Owner Password    | Deprecated (avoid use)               |
| Refresh Token              | To renew tokens without re-login     |

---

### 🕰 OAuth 2.0 History (Quick Look)

- OAuth 1.0 (complex, cryptographic)
- OAuth 2.0 (token-based, simpler)
- OAuth 2.1 (current draft with improved security defaults)

---

### 🎯 Typical OAuth 2.0 Use Cases

- Login via Google, GitHub, Facebook
- Single Sign-On (SSO)
- Accessing third-party APIs (e.g., Google Calendar)
- Mobile & SPAs
- Securing microservices

---

## 💡 Spring Boot OAuth 2.0 Setup (Advanced)

### 🧩 Required Dependencies

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

---

### ⚙️ Configuration Example (application.yml)

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          github:
            client-id: YOUR_CLIENT_ID
            client-secret: YOUR_CLIENT_SECRET
            scope: read:user
        provider:
          github:
            authorization-uri: https://github.com/login/oauth/authorize
            token-uri: https://github.com/login/oauth/access_token
            user-info-uri: https://api.github.com/user
```

---

### 🔄 JWT vs. Opaque Tokens

| Token Type | Description                        | Use When                          |
|------------|------------------------------------|-----------------------------------|
| JWT        | Self-contained token with claims   | ✅ Stateless, REST APIs           |
| Opaque     | Random token stored on server      | Better for session/state control |

---

### 🔐 Authorization Server vs Resource Server

| Server Type         | Role                                    |
|---------------------|------------------------------------------|
| Authorization Server| Authenticates users, issues tokens       |
| Resource Server     | Hosts protected endpoints, validates JWT |

Spring Boot examples:
- Auth Server: Spring Authorization Server, Keycloak, Okta
- Resource Server: Spring Security with JWT decoder

---

### 🔍 Token Structure (JWT)

```json
{
  "sub": "user@example.com",
  "scope": "read write",
  "exp": 1712345678,
  "iss": "https://auth.example.com"
}
```

### ✅ Validating JWT in Spring Boot

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
      .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
      .oauth2ResourceServer(oauth2 -> oauth2.jwt());
    return http.build();
}
```

---

### 🧑‍💼 OAuth Client Management

- **Client ID / Secret** from the provider
- Register Redirect URIs
- 🔓 Public Clients = no secret (e.g., SPA, mobile)
- 🔐 Confidential Clients = must keep secret

---

### 🧭 Scopes and Roles

| Concept | Purpose                                     |
|--------|---------------------------------------------|
| Scopes | Limit what access the token grants          |
| Roles  | Internal application authorization (RBAC)   |

**Example:** `SCOPE_read` → mapped to Spring Security authority.

---

## ✅ Security Best Practices

| Best Practice                      | Why It Matters                           |
|------------------------------------|------------------------------------------|
| Always use HTTPS                   | Prevent token interception               |
| Use PKCE + Authorization Code      | Most secure for client-side apps         |
| Use refresh tokens securely        | Avoid re-login, improve UX               |
| Keep tokens short-lived            | Reduces risk from leaks                  |
| Don’t log sensitive token data     | Avoid accidental exposure                |
| Never hardcode secrets             | Use secure vaults/env variables          |

---

## ❌ Handling Errors (Simplified)

- Customize login with `oauth2Login().loginPage(...)`
- Handle unauthorized with `AuthenticationEntryPoint`
- Customize error messages via `OAuth2ErrorHandler`

---

## 🛡️ Auditing & Logging

| Event                  | What to Log (Avoid Sensitive Data)        |
|------------------------|-------------------------------------------|
| Login Success/Failure  | Timestamp, IP, User ID                    |
| Token Issuance         | Token type, expiration, user info (no JWT body!) |

---

## 📈 Monitoring & Alerts

- Token usage & failures
- Spring Actuator + Prometheus + Grafana
- Alert on suspicious patterns

---

## ⚖️ Compliance (Simplified)

- Ask for user **consent**
- Don’t store **PII** in JWTs
- Document how data is used
- Brief mention of GDPR, HIPAA

---

## 📚 Tools & Resources

- [Spring OAuth2 Docs](https://spring.io/guides/tutorials/spring-boot-oauth2/)
- [OAuth 2.1 Draft](https://oauth.net/2.1/)
- [Spring Authorization Server](https://spring.io/projects/spring-authorization-server)
- [JWT.io](https://jwt.io)
- [RFC 6749 - OAuth 2.0 Spec](https://tools.ietf.org/html/rfc6749)
