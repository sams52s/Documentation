# 🔐 Java Spring Boot Authentication Security

Welcome, fellow developer! This guide is your go-to reference for securing Java Spring Boot applications with modern authentication and authorization techniques. We'll explore everything from the basics of Spring Security to integrating OAuth 2.0 and handling stateless JWTs.

> 📚 This is the main documentation file. For deep dives, check out:
>
> 👉 [JWT Guide](./jwt-guide.md)
> 👉 [OAuth 2.0 Guide](./oauth2-guide.md)

---

## 🚦 Authentication & Authorization in Spring Boot

**Authentication** verifies *who you are*. **Authorization** checks *what you’re allowed to do*.
Spring Boot, powered by **Spring Security**, makes implementing these concepts flexible and secure.

### 🔸 Basic Authentication
- Uses HTTP Basic headers (username/password encoded in Base64)
- Quick to implement, but not suitable for modern apps
- Best used in internal tools or testing environments

```java
http
  .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
  .httpBasic();
```

---

## 🛡️ Spring Security Core

**Spring Security** is a filter-based framework that intercepts HTTP requests through a customizable filter chain.

### 🔧 Key Filters
- `SecurityContextPersistenceFilter`
- `UsernamePasswordAuthenticationFilter`
- `CsrfFilter`
- `FilterSecurityInterceptor`

### 🧩 Sample Config
```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    return http
        .csrf(AbstractHttpConfigurer::disable)
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        )
        .formLogin(Customizer.withDefaults())
        .build();
}
```

---

## 🧾 JWT (JSON Web Token)

JWTs are self-contained tokens used for stateless authentication. Ideal for microservices and REST APIs.

👉 Jump to full [JWT Guide](./jwt-guide.md)

---

## 🚪 OAuth 2.0

OAuth 2.0 lets you offload authentication to trusted providers like Google, GitHub, etc.

👉 Jump to full [OAuth 2.0 Guide](./oauth2-guide.md)

---

## 🏗️ Architecture Comparison

| Architecture      | Security Tools                          |
|------------------|------------------------------------------|
| Web App          | Spring Security + OAuth2                 |
| REST API         | OAuth2 + JWT                             |
| Microservices    | OAuth2 Client Credentials Flow + JWT     |

### 🧠 Security Design Tips
- Use JWTs for REST APIs
- Centralize auth with OAuth2 for microservices
- Use Spring Authorization Server for full control

---

## 🧪 Full Authentication Flow Summary

1. Secure `/api/**` endpoints
2. Set up OAuth2 login (e.g., GitHub)
3. On success, generate and return a JWT
4. Use JWT in `Authorization` header for protected endpoints

```http
GET /api/user
Authorization: Bearer <jwt_token>
```

---

## ✅ Best Practices Checklist

| Practice                     | Description                                                  |
|-----------------------------|--------------------------------------------------------------|
| ✅ Enforce HTTPS            | Prevent MITM attacks, secure token exchange                  |
| ✅ Store Secrets Securely   | Use AWS Secrets Manager, Vault, or env vars                  |
| ✅ Use Short-lived Tokens   | Expire access tokens quickly                                 |
| ✅ Implement Refresh Tokens | Maintain sessions securely without long-lived tokens         |
| ✅ Validate All Claims      | Check issuer, audience, expiration on each request           |
| ✅ Use Spring Security Test | Automate security testing                                     |
| ✅ Monitor with Actuator    | Get real-time insights into your app's security               |

---

## 🧵 Linked Resources

- [JWT Guide](./jwt-guide.md)
- [OAuth 2.0 Guide](./oauth2-guide.md)
- [Spring Security Docs](https://spring.io/projects/spring-security)
- [JWT.io](https://jwt.io)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)


