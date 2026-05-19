# 📘 Day 16: JWT Implementation + Role-Based Authorization  
  
## 1. What We Fix Today  
* Actually **generate JWT**  
* **Validate JWT on every request**  
* Add **roles (ADMIN / USER)**  
* Restrict endpoints based on roles  
👉 Interview line:  
Authentication identifies the user; authorization decides access.  
  
## 2. JWT Utility Class (Token Creation & Validation)  
This class:  
* Generates JWT  
* Validates JWT  
* Extracts username & roles  
```
@Component
public class JwtUtil {

    private final String SECRET = "my-secret-key";

    public String generateToken(String username, String role) {
        return Jwts.builder()
                .setSubject(username)
                .claim("role", role)
                .setIssuedAt(new Date())
                .setExpiration(
                    new Date(System.currentTimeMillis() + 1000 * 60 * 60)
                )
                .signWith(SignatureAlgorithm.HS256, SECRET)
                .compact();
    }

    public String extractUsername(String token) {
        return Jwts.parser()
                .setSigningKey(SECRET)
                .parseClaimsJws(token)
                .getBody()
                .getSubject();
    }

    public String extractRole(String token) {
        return (String) Jwts.parser()
                .setSigningKey(SECRET)
                .parseClaimsJws(token)
                .getBody()
                .get("role");
    }
}

```
👉 Interview tip: JWT expiry is **mandatory** in real systems.  
  
## 3. Authentication Endpoint (Login API)  
```
@RestController
@RequestMapping("/auth")
public class AuthController {

    private final JwtUtil jwtUtil;

    public AuthController(JwtUtil jwtUtil) {
        this.jwtUtil = jwtUtil;
    }

    @PostMapping("/login")
    public String login(@RequestBody LoginRequest request) {

        // demo validation
        if ("user".equals(request.getUsername())
                && "password".equals(request.getPassword())) {

            return jwtUtil.generateToken(
                    request.getUsername(), "USER");
        }

        throw new RuntimeException("Invalid credentials");
    }
}

```
  
## 4. JWT Filter (Most Important Part)  
This filter:  
* Runs **before controller**  
* Extracts JWT from header  
* Validates token  
* Sets authentication in SecurityContext  
```
@Component
public class JwtFilter extends OncePerRequestFilter {

    private final JwtUtil jwtUtil;

    public JwtFilter(JwtUtil jwtUtil) {
        this.jwtUtil = jwtUtil;
    }

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        String authHeader =
                request.getHeader("Authorization");

        if (authHeader != null &&
            authHeader.startsWith("Bearer ")) {

            String token = authHeader.substring(7);
            String username = jwtUtil.extractUsername(token);
            String role = jwtUtil.extractRole(token);

            UsernamePasswordAuthenticationToken auth =
                    new UsernamePasswordAuthenticationToken(
                            username,
                            null,
                            List.of(new SimpleGrantedAuthority(
                                    "ROLE_" + role)));

            SecurityContextHolder.getContext()
                    .setAuthentication(auth);
        }

        filterChain.doFilter(request, response);
    }
}

```
👉 Interview line:  
JWT validation happens in a filter, not in the controller.  
  
## 5. Register JWT Filter in Security Config  
```
@Bean
public SecurityFilterChain filterChain(HttpSecurity http)
        throws Exception {

    http
        .csrf(csrf -> csrf.disable())
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/auth/**").permitAll()
            .anyRequest().authenticated()
        )
        .addFilterBefore(jwtFilter,
                UsernamePasswordAuthenticationFilter.class);

    return http.build();
}

```
  
## 6. Role-Based Authorization  
**Method-Level Security**  
```
@EnableMethodSecurity

```
  
**Restrict Endpoint by Role**  
```
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/notes/{id}")
public void deleteNote(@PathVariable Long id) {
    service.delete(id);
}

```
Meaning:  
* Only users with ROLE_ADMIN can delete  
  
## 7. ROLE_ Prefix (Classic Interview Trap)  
Spring Security:  
* Internally expects roles as ROLE_X  
So:  
* hasRole("ADMIN")  
* Authority must be ROLE_ADMIN  
👉 Interview line:  
Spring automatically prefixes roles with ROLE_.  
  
## 8. Authentication vs Authorization Flow (Clear)  
1. Client logs in → gets JWT  
2. Client sends JWT with request  
3. Filter validates token  
4. SecurityContext is populated  
5. Authorization checks role  
6. Controller executes  
  
## 🔨 Project Task – Day 16  
**Secure Notes API (Phase 2)**  
1. Implement JWT utility  
2. Add JWT filter  
3. Generate token on login  
4. Protect APIs using JWT  
5. Add roles:  
    * USER → read notes  
    * ADMIN → delete notes  
6. Test using Postman:  
    * No token → 401  
    * Invalid token → 401  
    * USER deleting → 403  
