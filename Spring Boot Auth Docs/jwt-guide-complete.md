# 🧾 Complete JWT (JSON Web Token) Guide for Spring Boot


## 🧭 Basics

### 🔍 What is JWT?

JWT (JSON Web Token) is a compact, URL-safe format used to represent claims between two parties. It's commonly used for **stateless authentication** in modern APIs.

---

### 🧱 JWT Structure

```text
HEADER.PAYLOAD.SIGNATURE
```

| Part       | Description                                  |
|------------|----------------------------------------------|
| Header     | Token type & signing algorithm (e.g., HS256) |
| Payload    | Claims (e.g., sub, exp, role)                |
| Signature  | Ensures token hasn’t been tampered with      |

![JWT Structure](https://github.com/sams52s/Documentation/blob/main/Spring%20Boot%20Auth%20Docs/images/e2f0e688-c1cd-4c04-a297-6f33a4c58288.png)
---

### ✅ Why Use JWT?

- Self-contained tokens
- Stateless authentication
- Supports scalable REST APIs
- Widely supported and secure (with HTTPS)

---

### 🛠 How JWT Works

1. User authenticates (via OAuth2 or login)
2. App generates JWT
3. Client stores JWT and sends it in `Authorization: Bearer` header
4. Server validates the JWT on every request

---

### 🧪 Sample JWT

```json
{
  "sub": "user@example.com",
  "exp": 1712345678,
  "role": "ADMIN",
  "iss": "https://auth.myapp.com"
}
```

---

## 💡 Advanced JWT in Spring Boot

### 🔧 Generating a Token

```java
String token = Jwts.builder()
    .setSubject(user.getEmail())
    .claim("role", user.getRole())
    .setIssuedAt(new Date())
    .setExpiration(new Date(System.currentTimeMillis() + 900_000))
    .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
    .compact();
```

---

### 🔍 Validating Tokens (JwtDecoder)

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth.myapp.com
```

```java
@Bean
public SecurityFilterChain apiSecurity(HttpSecurity http) throws Exception {
    http
      .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
      .oauth2ResourceServer(oauth2 -> oauth2.jwt());
    return http.build();
}
```

---

### 🆚 JWT vs. Opaque Tokens

| Type       | JWT                               | Opaque                              |
|------------|------------------------------------|--------------------------------------|
| Format     | Self-contained JSON               | Random string                        |
| Storage    | On client                         | On server                            |
| Validation | Signature and claims              | Looked up in server/session store    |
| Use Case   | Stateless REST APIs               | Stateful apps, access control        |

---

### 🔒 Best Practices

| ✅ Practice                 | 📌 Reason                                   |
|----------------------------|---------------------------------------------|
| Use HTTPS Everywhere       | Prevent token sniffing                      |
| Use short-lived tokens     | Minimize damage if leaked                   |
| Use refresh tokens         | For long sessions                          |
| Never store passwords/PII  | JWTs are decodable                          |
| Validate claims strictly   | `exp`, `aud`, `iss`, `sub`                  |
| Avoid hardcoded secrets    | Use vaults/env vars                         |
| Consider token revocation  | Maintain blocklist or short expiry          |

---

### 🔐 Securing APIs with JWT

```bash
curl -H "Authorization: Bearer <jwt>" http://localhost:8080/api/user
```

| Situation            | Result                |
|----------------------|-----------------------|
| Missing token        | 401 Unauthorized      |
| Invalid token        | 403 Forbidden         |
| Valid token          | 200 OK                |

---

### 🛡️ Common JWT Claims

| Claim     | Description                           |
|-----------|---------------------------------------|
| `sub`     | Subject (usually user identifier)     |
| `iss`     | Issuer of the token                   |
| `aud`     | Audience - who should accept token    |
| `exp`     | Expiration timestamp (UNIX format)    |
| `iat`     | Issued-at timestamp                   |
| `scope`   | Access scope                          |

---

### 🧠 Token Storage Tips

| Platform | Recommendation                         |
|----------|------------------------------------------|
| Web      | Use `HttpOnly` secure cookies           |
| Mobile   | Use secure storage like Keystore/Keychain |
| Don't    | Store in `localStorage` (XSS risk)      |

---

## 🔐 Advanced Security Tips

- Sign JWTs with **asymmetric keys** (RS256)
- Validate **audience and issuer**
- Monitor for **reuse of expired tokens**
- Use `kid` (Key ID) in headers to support key rotation

---

## 🧪 Auditing & Monitoring

| What to Log        | Why                            |
|--------------------|---------------------------------|
| Token issuance     | Track who gets access and when  |
| Failed validations | Detect tampering or misuse      |
| Expired tokens     | Analyze session patterns        |

---

## 📚 Tools & References

- [JWT.io](https://jwt.io)
- [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)
- [Spring Security Reference](https://docs.spring.io/spring-security/reference/)
- [Spring Authorization Server](https://spring.io/projects/spring-authorization-server)
