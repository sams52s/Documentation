# 🧾 JWT (JSON Web Token) Guide for Spring Boot

## 🔍 What is JWT?
JWT (JSON Web Token) is a compact, URL-safe token format used to represent claims between two parties.

### 🧱 Token Structure:
```text
HEADER.PAYLOAD.SIGNATURE
```

| Part       | Description                                                                 |
|------------|-----------------------------------------------------------------------------|
| Header     | Token type & signing algorithm (e.g. HS256, RS256)                         |
| Payload    | Claims like `sub` (subject), `exp` (expiration), `role`, etc.              |
| Signature  | Ensures the token hasn’t been tampered with (signed using a secret/key)    |

---

## ⚙️ How JWT Works in Spring Boot

### 📌 Token Generation Example
```java
String token = Jwts.builder()
    .setSubject(user.getEmail())
    .claim("role", user.getRole())
    .setIssuedAt(new Date())
    .setExpiration(new Date(System.currentTimeMillis() + 900_000))
    .signWith(SignatureAlgorithm.HS512, SECRET_KEY)
    .compact();
```

### 📌 Token Validation Flow
1. Extract JWT from `Authorization` header
2. Decode and verify the signature
3. Check expiration and required claims

---

## 🔐 Spring Security JWT Integration

### ✅ Basic Configuration
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://your-auth-server.com/oauth2/default
```

### ✅ Security Filter Chain Setup
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

## 🔐 Security Best Practices

| ✅ Practice                  | 🔍 Why it matters                                             |
|-----------------------------|---------------------------------------------------------------|
| Short-lived Access Tokens   | Reduces damage if token leaks (15–30 min recommended)         |
| Use Refresh Tokens          | Extend sessions safely without re-login                       |
| Never Hardcode Secrets      | Use Vault or cloud secret managers                            |
| Validate All Claims         | Especially `exp`, `aud`, `iss`, and `sub`                     |
| Use HTTPS Everywhere        | Prevent token sniffing                                        |
| Don’t Store Sensitive Data  | JWTs can be decoded — don’t include passwords or PII          |
| Implement Token Revocation  | Use a blocklist or very short expiration if using JWTs        |

---

## 🧪 Testing JWT-Protected APIs

### Example:
```http
GET /api/user
Authorization: Bearer <your_jwt_token>
```

| Case                   | Expected Result        |
|------------------------|------------------------|
| No token               | 401 Unauthorized       |
| Valid token            | 200 OK                 |
| Expired/Tampered token | 401 or 403             |

---

## 🔗 Tools & Resources
- [JWT.io](https://jwt.io) — debugger and learning tool
- [Spring Security Reference](https://docs.spring.io/spring-security/reference/)
- [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519) — official JWT spec


