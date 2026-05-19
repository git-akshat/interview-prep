# 📘 Day 15: Spring Security Basics + JWT Authentication  
  
## 1. Why Do We Need Security?  
Security ensures authentication (who you are) and authorization (what you can do).  
  
## 2. Authentication vs Authorization (Very Important)  
**Authentication**  
* Verifies **identity**  
* Example: login with username/password  
  
**Authorization**  
* Verifies **permissions**  
* Example: admin vs user access  
  
## 3. What is Spring Security?  
Spring Security:  
* Powerful security framework  
* Filters every HTTP request  
* Integrates deeply with Spring Boot  
  
By default:  
* All endpoints are **secured**  
* Login form is auto-generated  
  
👉 Interview line:  
Spring Security works using a filter chain before controller execution.  
  
## 4. Session-based vs Token-based Authentication  
**Session-based (Traditional)**  
* Server stores session  
* Stateful  
* Not scalable for APIs  
  
**Token-based (Modern APIs)**  
* Server issues token (JWT)  
* Client sends token on every request  
* Stateless  
  
👉 Interview line:  
REST APIs prefer stateless, token-based authentication.  
  
## 5. What is JWT (JSON Web Token)?  
JWT consists of Header, Payload, and Signature. Header defines algorithm, Payload carries claims, Signature ensures integrity  
  
Structure:  
```
HEADER.PAYLOAD.SIGNATURE

```
  
Contains:  
* User identity  
* Roles  
* Expiry time  
Signed using a secret key.  
  
**JWT Example (Conceptual)**  
```
{
  "sub": "akshat",
  "role": "USER",
  "exp": 1712345678
}

```
  
🔑 Common Standard Claims (interview important)  

| Claim | Meaning           |
| ----- | ----------------- |
| sub   | Subject (user id) |
| iat   | Issued at         |
| exp   | Expiration time   |
| iss   | Issuer            |
| aud   | Audience          |
  
👉 Important:  
* JWT is **not encrypted**  
* It is **signed**  
* Anyone can read it, but cannot modify it  
  
## 6. Spring Security Flow with JWT  
1. User sends login request  
2. Server validates credentials  
3. Server generates JWT  
4. Client stores JWT  
5. Client sends JWT in Authorization header  
6. Server validates JWT for every request  
  
Header format:  
```
Authorization: Bearer <token>

```
  
## 7. Basic Spring Security Configuration  
**Security Config (Minimal)**  
```
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)
            throws Exception {

        http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/auth/**").permitAll()
                .anyRequest().authenticated()
            );

        return http.build();
    }
}

```
Key points:  
* Disable CSRF(Cross-Site Request Forgery) for APIs  
* /auth/** is public  
* All other endpoints require auth  
  
👉 Interview line:  
Security is configured using a filter chain, not interceptors.  
  
## 8. UserDetails & UserDetailsService  
Spring Security needs user info.  
  
**UserDetails**  
Represents authenticated user.  
  
**UserDetailsService**  
Loads user by username.  
  
**Example**  
```
@Service
public class CustomUserDetailsService
        implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {

        return User.withUsername(username)
                .password("{noop}password")
                .roles("USER")
                .build();
    }
}

```
⚠️ {noop} is for demo only.  
  
## 9. Password Encoding   
Never store plain passwords.  
  
Always Use:  
BCryptPasswordEncoder  
```
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}

```
👉 Interview line:  
BCrypt is slow by design to prevent brute-force attacks.  
  
## 10. Login Endpoint (Auth Controller)  
```
@RestController
@RequestMapping("/auth")
public class AuthController {

    @PostMapping("/login")
    public String login() {
        // validate credentials
        // generate JWT
        return "JWT_TOKEN";
    }
}

```
(We’ll implement full JWT logic on Day 16.)  
  
## 🔨 Project Task – Day 15  
**Secure Notes API (Phase 1)**  
1. Add Spring Security dependency  
2. Configure SecurityFilterChain  
3. Create /auth/login endpoint  
4. Protect /notes/** endpoints  
5. Test:  
    * Unauthorized request → 401  
    * Login endpoint accessible  
