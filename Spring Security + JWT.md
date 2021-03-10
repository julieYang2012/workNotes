# Spring Security + JWT



## Authentication work flow

> ![image-20210105092901625](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210105092901625.png)

in `Servlet` filters chanis，Spring Security add`FilterChainProxy`. `FilterChainProxy`：

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response,
                     FilterChain chain) throws IOException, ServletException {
    ...
    
    // get Spring Security filters
    List<Filter> filters = getFilters(request);
    // Spring Security 
    VirtualFilterChain vfc = new VirtualFilterChain(fwRequest, chain, filters);
    vfc.doFilter(request, response);
    
    ...
}

```

the filters：

![seucirty默认过滤器链.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d60a4f0509094ce99c412e5f79fa8900~tplv-k3u1fbpfcp-zoom-1.image)

`UsernamePasswordAuthenticationFilter`  for Authentication

`FilterSecurityInterceptor ` for Authorization.



```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

```



```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
}

```

>  **💡`SecurityContextHolder`** manager it and you can use it everywhere：

```java
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

```

the path：`SecurityContextHolder`👉`SecurityContext`👉`Authentication`。



📝`Authentication`：save the user info

📝`SeucirtyContext`：for get `Authentication`

📝`SecurityContextHolder`：for get`SecurityContext`

the relationship：

![securitycontextholder](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d0732a48f3c4480386682f3ecce4e0b1~tplv-k3u1fbpfcp-zoom-1.image)

`Authentication`：

📝`Principal`：befor authentication is user name, after is user object.

📝`Credentials`：usually is password

📝`Authorities`：permissions



sql the user info from database👉check password👉true save it to SecurityContext👉SecurityContext contains authentication means user login sucess

Spring Security：

```java
Authentication authentication = new UsernamePasswordAuthenticationToken(userName, password, permissions);
SecurityContextHolder.getContext().setAuthentication(authentication);

```

the path ：`Authentication`👉`SecurityContext`👉`SecurityContextHolder`。



```java
// check user
if (!userService.login(userName, password)) {
    return "User or Password not match";
}
//
Authentication authentication = new UsernamePasswordAuthenticationToken(userName, password, permissions);
SecurityContextHolder.getContext().setAuthentication(authentication);

```



Spring security using the **💡`AuthenticationManager`**  for Authentication

```
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    @Override
    protected AuthenticationManager authenticationManager() throws Exception {
        return super.authenticationManager();
    }
}


```



```
@RestController
@RequestMapping("/API")
public class LoginController {
    @Autowired
    private AuthenticationManager authenticationManager;

    @PostMapping("/login")
    public String login(@RequestBody LoginParam param) {
        //create token
        Authentication token = new UsernamePasswordAuthenticationToken(param.getUsername(), param.getPassword());
        // AuthenticationManagerverify the token
        Authentication authentication = authenticationManager.authenticate(token);
        // save it to context
        SecurityContextHolder.getContext().setAuthentication(authentication);
        return "success";
    }
}

```





Session `HttpSessionSecurityContextRepository.SPRING_SECURITY_CONTEXT_KEY`   you can get `Authentication`：

```java
Authentication = (Authentication)session.getAttribute(HttpSessionSecurityContextRepository.SPRING_SECURITY_CONTEXT_KEY)

```

：

```java
@GetMapping("/logout")
public String logout() {
    SecurityContextHolder.clearContext();
    return "logout";
}

```



## JWT



![Image for post](https://miro.medium.com/max/1334/0*tP3PZ13_RxFy478P.)

disable the `session`：

```java
// 
http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

```

> 

create `token`：

```java
@Autowired
private UserService userService;

@PostMapping("/login")
public UserVO login(@RequestBody @Validated LoginParam user) {
    // call userService
    return userService.login(user);
}

```



```java
public UserVO login(LoginParam param) {
    //
    UserEntity user = userMapper.selectByUsername(param.getUsername());
    // 
    if (user == null || !passwordEncoder.matches(param.getPassword(), user.getPassword())) {
        throw new ApiException("Error");
    }

    // 
    UserVO userVO = new UserVO();
    userVO.setId(user.getId())
        .setUsername(user.getUsername())
        // 
        .setToken(jwtManager.generate(user.getUsername()));
    return userVO;
}

```



add filter：

```java
@Component
public class LoginFilter extends OncePerRequestFilter {
    @Autowired
    private JwtManager jwtManager;
    @Autowired
    private UserServiceImpl userService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws ServletException, IOException {
       
        Claims claims = jwtManager.parse(request.getHeader("Authorization"));
        if (claims != null) {
          
            String username = claims.getSubject();
       
            UserDetails user = userService.loadUserByUsername(username);
           
            Authentication authentication = new UsernamePasswordAuthenticationToken(user, user.getPassword(), user.getAuthorities());
          
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }
        chain.doFilter(request, response);
    }
}

```

replace the filter in securityConfig ：

```java

http.addFilterBefore(loginFilter, UsernamePasswordAuthenticationFilter.class);

```



login :

![image-20210105122804422](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210105122804422.png)





get user list

![image-20210105122940800](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20210105122940800.png)