# Spring Microservice with OAuth2

This project shows how to integrate OAuth2 authentication with your
[Spring Boot](http://spring.io/projects/spring-boot) microservices.

## How does it work?

The microservice is implemented using plain Spring Boot and
Spring Web:
```java
@RestController
class SecuredController {
    @GetMapping("/")
    public Principal userInfo(Principal principal) {
        return principal;
    }
}
```

OAuth2 integration is done using
[Spring Cloud Security](https://spring.io/projects/spring-cloud-security)
with this single Java class:
```java
@EnableOAuth2Sso
@Configuration
class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.antMatcher("/**")
                .authorizeRequests()
                .antMatchers("/actuator**", "/error**", "/login**").permitAll()
                .antMatchers("/").authenticated();
    }
}
```

Notice the use of the class annotation [@EnableOAuth2Sso](https://docs.spring.io/spring-security-oauth2-boot/docs/current/api/org/springframework/boot/autoconfigure/security/oauth2/client/EnableOAuth2Sso.html):
this is where the magic happens. The Spring Security configuration
sets up protected resources, and this annotation will automatically add
filters to handle OAuth2 authentication.

You just need to provide your OAuth2 parameters in the application
configuration:
```yaml
security:
  oauth2:
    client:
      clientId: 78578397ee0ae9d3d573
      clientSecret: f823cc74a533bd0b4946ff29070193bb77163d05
      accessTokenUri: https://github.com/login/oauth/access_token
      userAuthorizationUri: https://github.com/login/oauth/authorize
      clientAuthenticationScheme: form
    resource:
      userInfoUri: https://api.github.com/user
```

The `clientId` and the `clientSecret` values are retrieved from the
[GitHub developer settings](https://github.com/settings/developers),
where you need to register your app in order to
use OAuth2 authentication:
<img src="https://imgur.com/download/gcKeLaU"/>

## How to use it?

Compile this project using a JDK 8:
```shell
$ ./mvnw clean package
```

Run the microservice on your host:
```shell
$ java -jar target/spring-microservice-oauth2-1.0.0-SNAPSHOT.jar
```

A simple microservice is now available at http://localhost:8080.

As soon as you hit this URL, you'll need to sign in using your
GitHub account:
<img src="https://imgur.com/download/Zcs5nXz"/>

In case you're using 2-Factor authentication, you'll need to enter
your PIN before you can access the microservice:
<img src="https://imgur.com/download/Kpj7j3e"/>

When you're done with authentication, you should be redirected to
the microservice. This page will display your GitHub account
information.

## Contribute

Contributions are always welcome!

Feel free to open issues & send PR.

## License

Copyright &copy; 2018 Pivotal Software, Inc.

This project is licensed under the [Apache Software License version 2.0](https://www.apache.org/licenses/LICENSE-2.0).
