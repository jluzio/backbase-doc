# Backbase

## external-jwt

### ExternalJwtConsumer
```java
package com.backbase.buildingblocks.jwt.external;

public interface ExternalJwtConsumer {
  ExternalJwt parseToken(String token) throws ExternalJwtException, JsonWebTokenException;
}
```

### ExternalJwtMapper
```java
package com.backbase.buildingblocks.jwt.external;

public interface ExternalJwtMapper {

  Optional<ExternalJwtClaimsSet> claimSet(Authentication authentication, HttpServletRequest request);

  void updateAuthorities(Set<String> authorities, Authentication authentication);

  Optional<String> getUserName(Authentication authentication);
}
```

### ExternalJwtProducer
```java
package com.backbase.buildingblocks.jwt.external;

public interface ExternalJwtProducer {

  ExternalJwt createToken(Authentication authentication) throws ExternalJwtException, JsonWebTokenException;

  ExternalJwt createToken(Authentication authentication, ExternalJwtClaimsSet claimsSet) throws ExternalJwtException, JsonWebTokenException;

  ExternalJwt createToken(
    Authentication authentication, HttpServletRequest request, ExternalJwtClaimsSet claimsSet
  ) throws ExternalJwtException, JsonWebTokenException;
}

```

### ExternalJwtTenantMapper
```java
package com.backbase.buildingblocks.jwt.external;

public interface ExternalJwtTenantMapper {

  Optional<String> tenantId(Authentication authentication, HttpServletRequest request);
}
```

## authentication-core

### AuthenticationHandler
```java
package com.backbase.buildingblocks.authentication.core;

public interface AuthenticationHandler {

  void onSuccessfulLogin(HttpServletRequest request, HttpServletResponse response, Authentication authentication);

  void onSuccessfulLogout(HttpServletRequest request, HttpServletResponse response, Authentication authentication);
}
```

### RefreshHandler
```java
package com.backbase.buildingblocks.authentication.core;

public interface RefreshHandler {
  void onSuccessfulRefresh(HttpServletRequest request, HttpServletResponse response, Authentication authentication);
}
```

### Examples
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableDefaultAuthEndpoint(exclude = LOGIN)
public class Application extends SpringBootServletInitializer implements WebApplicationInitializer {

    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}

@Controller @Slf4j @RequiredArgsConstructor
public class LoginController {
    private static final String USERNAME_FIELD = "username";
    private static final String EMAIL_FIELD = "email";
    private static final String PASSWORD_FIELD = "password";
    private static final String LOGIN_TEMPLATE = "login.ftl";
    private static final String SUCCESS_TEMPLATE = "success.ftl";
    private static final String DEFAULT_LOGIN_PATH = "/login";
    private final AuthenticationManager authenticationManager;
    private final AuthenticationHandler authenticationHandler;
    private final ExternalJwtProducerProperties tokenProperties;
    private final ExternalJwtProducer tokenProducer;

    @ResponseBody
    @GetMapping(value = DEFAULT_LOGIN_PATH, produces = TEXT_HTML_VALUE)
    public String getLoginPage(HttpServletRequest request) {
        return generateHTMLTemplate(request, null, LOGIN_TEMPLATE);
    }

    @ResponseBody
    @PostMapping(value = DEFAULT_LOGIN_PATH, produces = TEXT_HTML_VALUE, consumes = APPLICATION_FORM_URLENCODED_VALUE)
    public ResponseEntity<String> doLogin(@RequestParam(value = USERNAME_FIELD) String username,
                                          @RequestParam(value = EMAIL_FIELD) String email,
                                          @RequestParam(value = PASSWORD_FIELD) String password,
                                          HttpServletRequest request,
                                          HttpServletResponse response) {

        AbstractAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
        try {
            Authentication authentication = authenticationManager.authenticate(authRequest);
            if (authentication != null && authentication.isAuthenticated() && !(authentication instanceof AnonymousAuthenticationToken)) {
                this.appendCookieToken(request, response, authentication);
                this.authenticationHandler.onSuccessfulLogin(request, response, authentication);
                log.debug("User '{}' authenticated", username);
            }
            log.info("User '{}' authenticated with email '{}'", username, email);
            return new ResponseEntity<>(generateHTMLTemplate(request, null, SUCCESS_TEMPLATE), HttpStatus.OK);
        } catch (AuthenticationException ex) {
            log.error("Unauthorized user '{}'", username);
            return new ResponseEntity<>(generateHTMLTemplate(request, ex.getMessage(), LOGIN_TEMPLATE), HttpStatus.UNAUTHORIZED);
        }
    }

    private void appendCookieToken(HttpServletRequest request, HttpServletResponse response, Authentication authentication) {
        log.debug("Append external jwt for auth[{}]", authentication);
        try {
            ExternalJwtCookie externalJwtCookie = ExternalJwtCookie.create()
                    .withProducer(this.tokenProducer)
                    .withAuthentication(authentication)
                    .withCookieName(this.tokenProperties.getCookie().getName())
                    .withRequest(request)
                    .build();
            response.addCookie(externalJwtCookie);
        } catch (JsonWebTokenException | ExternalJwtException var5) {
            log.error("Can't append external jwt token to http response", var5);
        }
    }

    private String generateHTMLTemplate(HttpServletRequest request, String errorMessage, String templateName) {
        final String loginPageUri = AuthEndpoints.getRequestPath(request);

        Configuration cfg = new Configuration(Configuration.VERSION_2_3_29);
        Writer out = new StringWriter();
        try {
            Resource resource = new ClassPathResource("statics");
            cfg.setDirectoryForTemplateLoading(resource.getFile());
            cfg.setDefaultEncoding("UTF-8");
            Template template = cfg.getTemplate(templateName);
            template.process(inputTemplateInitialization(errorMessage, loginPageUri), out);
        } catch (IOException | TemplateException e) {
            log.error("Error generating the HTML template", e);
        }

        return out.toString();
    }

    private Map<String, Object> inputTemplateInitialization(String errorMessage, String loginPageUri) {
        Map<String, Object> input = new HashMap<>();
        input.put("USERNAME_FIELD", USERNAME_FIELD);
        input.put("PASSWORD_FIELD", PASSWORD_FIELD);
        input.put("EMAIL_FIELD", EMAIL_FIELD);
        input.put("LOGIN_PAGE_URL", loginPageUri);
        input.put("ERROR_MESSAGE", errorMessage);
        return input;
    }
}

// external token extension
@Component @Slf4j
public class CustomExternalJwtMapper implements ExternalJwtMapper {
    public Optional<ExternalJwtClaimsSet> claimSet(Authentication authentication, HttpServletRequest request) {
        log.info("Adding extra claim to the token");
        String country = request.getLocale().getCountry();
        return Optional.of(new ExternalJwtClaimsSet(Collections.singletonMap("usr_country", country)));
    }
}

// in-memory auth
@Configuration @EnableWebSecurity
public class SecurityConfiguration extends AuthSecurityConfiguration {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("user").password("{noop}user").roles("USER", "group_user(USER)")
                .and()
                .withUser("admin").password("{noop}admin").roles("ADMIN", "ACTUATOR", "group_admin(ADMIN)")
                .and()
                .withUser("manager").password("{noop}manager").roles("MANAGER", "group_manager(MANAGER)")
                .and()
                .withUser("john").password("{noop}john").roles("USER", "group_beta(USER)")
        ;
    }
}
```

```java
// Token Converter extension (internal token extension)
@Component
public class CustomTokenEnhancer implements InternalTokenEnhancer {

    @Override
    public OAuth2AccessToken enhance(OAuth2AccessToken accessToken, OAuth2Authentication authentication) {
        accessToken.getAdditionalInformation().putAll(getCustomClaims());
        return accessToken;
    }

    private Map<String, Object> getCustomClaims() {
        Map<String, Object> claims = new HashMap<>();
        claims.put("twToken", "58f0755d-24fb-4ab3-a420-b7a10e31226c");
        return claims;
    }
}

```

# Running auth service
```bash
mvn spring-boot:run -Dspring-boot.run.jvmArguments="-DSIG_SECRET_KEY=JWTSecretKeyDontUseInProduction! -DEXTERNAL_SIG_SECRET_KEY=JWTSecretKeyDontUseInProduction! -DEXTERNAL_ENC_SECRET_KEY=JWTEncKeyDontUseInProduction666!"

```

# Spring

## LDAP
### UserDetailsContextMapper
```java
package org.springframework.security.ldap.userdetails;

public interface UserDetailsContextMapper {

  UserDetails mapUserFromContext(
    DirContextOperations ctx,
    String usernam,
    Collection<? extends GrantedAuthority> authorities);

  void mapUserToContext(UserDetails user, DirContextAdapter ctx);
}
```

### Beans

```java
@Bean
public LdapTemplate ldapTemplate(LdapContextSource ldapContextSource) {
  return new LdapTemplate(ldapContextSource);
}

public LdapContextSource ldapContextSource(
  String url, String base, String userDn, String password) {
  LdapContextSource contextSource = new DefaultSpringSecurityContextSource(url);
  if (userDn != null) {
    contextSource.setUserDn(userDn);
    contextSource.setPassword(password);
  }
  contextSource.setBase(base);
  return contextSource;
}

# Search
org.springframework.ldap.core.LdapTemplate#search(org.springframework.ldap.query.LdapQuery, org.springframework.ldap.core.AttributesMapper<T>)
```
