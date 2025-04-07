# 🔐 Complete Guide to OAuth 2.0 in Spring Boot


## 🧭 Basics

### 🚀 What is OAuth 2.0?

OAuth 2.0 is an authorization framework that lets applications access user data **without needing their password**. It allows **delegated access** via **secure tokens**.

### 🔐 Why Use OAuth?
> OAuth2 is a protocol that allows applications to delegate authentication to trusted providers like GitHub, Google, or Facebook. It enables users to log in without the app ever handling their passwords.

- ✅ Delegated Access (on behalf of a user)
- ✅ Enhanced Security (no password sharing)
- ✅ Single Sign-On (SSO)
- ✅ Trusted 3rd-party authentication

---
### 🔐 OAuth2 Login Flow:

> The user clicks "Login with GitHub" and is redirected to GitHub's authorization endpoint. \
> GitHub asks the user to grant permissions and returns an authorization code.\
> Spring Boot exchanges this code for an access token.\
> The token is stored in the session and used for subsequent authenticated requests\


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
### 🧩 Authorization Code Grant (with PKCE)
The Authorization Code grant is a “three-legged” OAuth flow used by confidential or public clients to obtain an authorization code via the user’s browser, then exchange that code for tokens. It is the core flow for user login via OAuth 2.0 and is often enhanced with PKCE (Proof Key for Code Exchange) for security.
![OAUTH Flow](https://github.com/sams52s/Documentation/blob/main/Spring%20Boot%20Auth%20Docs/images/34811da5-6c97-489a-947c-f6f8a3c91fd2.png)

### ⚙️ Refresh Token Grant

A Refresh Token is a long-lived token issued by the Authorization Server alongside the access token, which allows the client to obtain new access tokens after the old one expires without involving the user again. The refresh token grant (defined in OAuth 2.0 Section 6) is not exactly a distinct initial flow, but an extension of other flows (Authorization Code, Resource Owner Password) – it cannot be used on its own without a prior user authentication. The process is: when an access token expires (or is about to expire), the client sends a request to the token endpoint with grant_type=refresh_token along with the refresh token and its client credentials. If valid, the Authorization Server responds with a new access token (and possibly a new refresh token) (Diagrams And Movies Of All The OAuth 2.0 Flows). 

![OAUTH Refresh Token](https://github.com/sams52s/Documentation/blob/main/Spring%20Boot%20Auth%20Docs/images/fbcde635-dfe6-4247-bd89-08c5ca55bb31.png)

Using a Refresh Token: The client makes a POST request to the Authorization Server’s token endpoint with grant_type=refresh_token and the previously obtained refresh token. The server validates the refresh token (and client auth) and returns a new access token (often issuing a new refresh token as well, rotating the old one). The client can then use the new access token to continue calling protected APIs.
In Spring Boot, if you’re using Spring Security’s OAuth2 client support, the refresh token handling can be configured via the OAuth2AuthorizedClientService which will automatically use the refresh token to renew the access token when needed. If implementing an Authorization Server, ensure refresh_token is included in the allowed grant types for clients that need it (and configure token store if using JDBC or JWT for storing refresh tokens). For example, in the earlier Authorization Server config, we allowed "refresh_token" for the Authorization Code client. This means after the user login exchange, the token response will include a refresh_token. The client can later perform:
POST /oauth/token 
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&
refresh_token=<refresh_token>&
client_id=demo-client&client_secret=demo-secret

and get a new access_token in response. Refresh tokens typically have a longer lifetime (or no fixed expiry) but can be revoked or invalidated by the Authorization Server.

### 🕰 OAuth 2.0 History (Quick Look)

- OAuth 1.0 (complex, cryptographic)
- OAuth 2.0 (token-based, simpler)
- OAuth 2.1 (current draft with improved security defaults)

![OAUTH Refresh Token](https://github.com/sams52s/Documentation/blob/main/Spring%20Boot%20Auth%20Docs/images/A_flowchart_in_the_image_illustrates_the_authentic.png)

---
## 💡 Token Structure and Validation
OAuth 2.0 uses tokens to represent granted authority. The two main token types are access tokens (typically a short-lived token used to access APIs) and refresh tokens (long-lived token to get new access tokens). Access tokens can be opaque (no internal structure, known only by the Authorization Server) or structured. A common format for structured tokens is JWT (JSON Web Token). OAuth 2.0 itself doesn’t mandate JWT, but when using OpenID Connect or certain resource server setups, JWTs are prevalent.
JWT Structure: A JSON Web Token is composed of three parts: a header, a payload, and a signature, each base64url-encoded and separated by dots. For example, a JWT looks like <header>.<payload>.<signature>.

![JWT Structure](https://github.com/sams52s/Documentation/blob/main/Spring%20Boot%20Auth%20Docs/images/e2f0e688-c1cd-4c04-a297-6f33a4c58288.png)

Structure of a JWT (JSON Web Token): The header (purple) typically contains metadata like the signing algorithm and token type, e.g. {"alg": "RS256", "typ": "JWT"}. The payload (green) contains the claims – statements about an identity or other data (e.g. user ID, scopes, issuer, expiration time). Common JWT payload claims in OAuth include iss (issuer), sub (subject, usually user ID), aud (audience, the intended recipient like the resource server), exp (expiry timestamp), iat (issued-at time), and possibly scope or authorities. The signature (blue) is generated by taking the Base64URL-encoded header and payload, and signing them with a secret or private key. In the example diagram above, the signature is HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret) for an HMAC-based token (with a secret key known to the server). For RSA or EC algorithms, the signature is created with the issuer’s private key and can be verified with the corresponding public key (JSON Web Tokens - jwt.io).
Token Validation: When a Resource Server receives an access token on a request (e.g., in the Authorization: Bearer <token> header), it must validate the token before granting access to the resource. There are two general strategies: local validation or introspection. For JWTs, local validation is common – the Resource Server can decode the token, verify the signature (using the Authorization Server’s public key), and check claims like expiration and audience. This is efficient (no network call) and is how Spring Boot’s Resource Server support works by default for JWT. In Spring Security, you would configure the Resource Server with the issuer’s JWKS (JSON Web Key Set) URI or a public key. For example, in application.yml:
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://your-auth-server.com/oauth2/default

With this, Spring Security will fetch the issuer’s public keys and automatically validate incoming JWTs (verifying signature and iss, aud, exp, etc.). If the token is valid, the request is authenticated with the JWT’s claims. If using opaque tokens, the Resource Server can use introspection: it makes a call to the Authorization Server’s introspection endpoint (/oauth/check_token or RFC 7662 endpoint) with the token to ask if it’s active and get its associated claims. Spring Security supports this via oauth2ResourceServer().opaqueToken() configuration where you set the introspection URI and credentials.
Spring Boot Resource Server Example: A basic configuration to secure APIs might look like:
@Bean
SecurityFilterChain apiSecurity(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(authz -> authz
            .anyRequest().authenticated()
        )
        .oauth2ResourceServer(oauth2 -> oauth2.jwt()); // enable JWT validation

    return http.build();
}

This, along with the issuer or JWK set URI property, is enough for Spring Boot to start validating JWTs on incoming requests. The Resource Server will automatically reject requests with invalid or expired tokens (returning 401 Unauthorized).
Token Content: It’s important to design the token’s content (claims or scopes) according to what the Resource Server needs. For instance, include scopes or authorities claims to represent what the token can do. When using JWT access tokens, avoid putting sensitive personal data in the token – as it can be decoded by any party in possession of it (it’s signed, not encrypted by default). If including user info, consider using reference tokens (opaque) or encrypting the JWT (JWT can be encrypted as JWE, though not as common in OAuth).
Revocation and Expiry: OAuth 2.0 access tokens are usually short-lived (e.g. 5 minutes to 1 hour) to limit exposure. If a JWT is issued, it cannot be revoked easily unless you maintain a blocklist, so short lifetimes mitigate risk. For long-lived sessions, use refresh tokens and rotate them. The framework also provides a token revocation endpoint (RFC 7009) which, if implemented, allows clients to actively revoke tokens (though with JWTs, revocation lists need to be consulted by resource servers, or one might use short expiration and frequent refresh instead).


### 🎯 Typical OAuth 2.0 Use Cases

- Login via Google, GitHub, Facebook
- Single Sign-On (SSO)
- Accessing third-party APIs (e.g., Google Calendar)
- Mobile & SPAs
- Securing microservices

---

## 💡 Spring Boot OAuth 2.0 Setup (Advanced)

In Spring Boot, you can implement this flow either by using Spring Security’s built-in support for OAuth2 login or by setting up your own Authorization Server. For example, using Spring Security OAuth2 Client, you would configure the client in application.yml (see OAuth Client Management section) and use the spring-security-oauth2-client starter to handle the redirect and token exchange automatically.

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
|Do|Don’t|
|------------------------------------|------------------------------------------|
|Use HTTPS for all OAuth endpoints and token transport.|Don’t transmit tokens or client secrets over HTTP or via insecure channels.|
|Use Authorization Code flow with PKCE for user logi1】.|Don’t use Implicit or Password grants (they are deprecate1】.|
|Validate redirect URIs strictly against allowed li1】.|Don’t use open redirectors or wildcards that can be manipulat2】.|
|Use state parameter (and/or PKCE) to protect against CS9】.|Don’t ignore CSRF; an attacker could hijack OAuth responses if not protected.|
|Keep access token scope narrow (least privilege).|Don’t request or grant more scope than necessary (avoid over-scoping tokens).|
|Make access tokens short-lived; use refresh tokens with rotation.|Don’t issue very long-lived access tokens, or reusable long-lived refresh tokens without rotation.|
|Securely store client secrets (for confidential clients).|Don’t embed client secrets in SPA or mobile app code (public clients can’t safely have secrets).|
|Utilize Spring Security’s frameworks to handle protocols (they implement best practices for you).|Don’t hand-roll your own OAuth logic if possible (to avoid missing subtle security steps).|
|Monitor and log token usage and anomalies (to detect abuse).|Don’t log sensitive token data in plaintext (or at all, if possible). Use monitoring wisely.|
|Stay updated on OAuth 2.1 and security BCPs for guidance.|Don’t assume default example configs are always secure – review and apply latest recommendations.|

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
