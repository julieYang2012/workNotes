

# spring security oauth2 自动刷新续签token (refresh token)

https://blog.csdn.net/m0_37834471/article/details/83213002?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control



# 1.引言

- 前提：了解spring security oauth2的大致流程（对过滤器的内容有一定的了解）
- 主要思路：

1. 首先用过期token访问受拦截资源
2. 认证失败返回401的时候调用异常处理器
3. 通过异常处理器结合refresh_token进行token的刷新
4. 刷新成功则通过请求转发（request.getRequestDispatcher）的方式再次访问受拦截资源

# 2.源码分析核心过滤器OAuth2AuthenticationProcessingFilter

- 此过滤器与我们的token的各种操作息息相关，不清楚的可以参考别人的博客进行了解https://blog.csdn.net/u013815546/article/details/77046453
- 下面是此过滤器的过滤方法，从中可以知道当授权失败抛出异常的时候将会被catch，并且通过authenticationEntryPoint.commence()调用端点异常处理器，这个被调用的异常处理器就是我们要重写的类

```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException,
			ServletException {
 
		final boolean debug = logger.isDebugEnabled();
		final HttpServletRequest request = (HttpServletRequest) req;
		final HttpServletResponse response = (HttpServletResponse) res;
 
		try {
 
			Authentication authentication = tokenExtractor.extract(request);
			
            ...
			
		catch (OAuth2Exception failed) {
			SecurityContextHolder.clearContext();
 
			if (debug) {
				logger.debug("Authentication request failed: " + failed);
			}
			eventPublisher.publishAuthenticationFailure(new BadCredentialsException(failed.getMessage(), failed),
					new PreAuthenticatedAuthenticationToken("access-token", "N/A"));
 
			authenticationEntryPoint.commence(request, response,
					new InsufficientAuthenticationException(failed.getMessage(), failed));
 
			return;
		}
 
		chain.doFilter(request, response);
	}

```

# 3.分析默认端点异常处理器

- 从过滤器源码中我们可以看到此异常处理器是有默认实现类的

```
public class OAuth2AuthenticationProcessingFilter implements Filter, InitializingBean {
 
	private final static Log logger = LogFactory.getLog(OAuth2AuthenticationProcessingFilter.class);
 
	private AuthenticationEntryPoint authenticationEntryPoint = new OAuth2AuthenticationEntryPoint();
 
    ...
 
}
```

- 通过查看此默认处理器，我们可以发现里面主要调用了doHandle的方法 

```
public class OAuth2AuthenticationEntryPoint extends AbstractOAuth2SecurityExceptionHandler implements
		AuthenticationEntryPoint {
 
	...
 
	public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException)
			throws IOException, ServletException {
		doHandle(request, response, authException);
	}
 
    ...
 
}
```

- 我们再次查看doHandle的具体内容可以得出此过滤器的主要功能有3个：

1. 解析异常类型
2. 扩展respone的一些属性和内容
3. respone 刷新缓存直接返回

```
protected final void doHandle(HttpServletRequest request, HttpServletResponse response, Exception authException)
			throws IOException, ServletException {
		try {
			ResponseEntity<?> result = exceptionTranslator.translate(authException);
			result = enhanceResponse(result, authException);
			exceptionRenderer.handleHttpEntityResponse(result, new ServletWebRequest(request, response));
			response.flushBuffer();
		}
		
        ...
	}
```

# 4.重写异常处理器

- 对默认异常处理器的分析，我们可以得出如果是我们需要的异常（401异常）则用我们自定义的方法进行处理，如果是其他异常则让原来的异常处理器处理即可，大致思路如下：

1. 通过exceptionTranslator.translate(authException)解析异常，判断异常类型（status）
2. 如果不是401异常，则直接调用默认异常处理器的处理方法即可
3. 如果是401异常则向授权服务器发起token刷新的请求
4. 如果token刷新成功，则通过request.getRequestDispatcher(request.getRequestURI()).forward(request,response);再次请求资源
5. 如果token刷新失败，要么跳转到登陆页面（web的话也可以通过response.sendirect跳转到登陆页面），要么返回错误信息（json）

```

public class LLGAuthenticationEntryPoint extends OAuth2AuthenticationEntryPoint {
 
    @Autowired
    private OAuth2ClientProperties oAuth2ClientProperties;
    @Autowired
    private BaseOAuth2ProtectedResourceDetails baseOAuth2ProtectedResourceDetails;
    private WebResponseExceptionTranslator<?> exceptionTranslator = new DefaultWebResponseExceptionTranslator();
    @Autowired
    RestTemplate restTemplate;
    
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        try {
            //解析异常，如果是401则处理
            ResponseEntity<?> result = exceptionTranslator.translate(authException);
            if (result.getStatusCode() == HttpStatus.UNAUTHORIZED) {
                MultiValueMap<String, String> formData = new LinkedMultiValueMap<String, String>();
                formData.add("client_id", oAuth2ClientProperties.getClientId());
                formData.add("client_secret", oAuth2ClientProperties.getClientSecret());
                formData.add("grant_type", "refresh_token");
                Cookie[] cookie=request.getCookies();
                for(Cookie coo:cookie){
                    if(coo.getName().equals("refresh_token")){
                        formData.add("refresh_token", coo.getValue());
                    }
                }
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
                Map map = restTemplate.exchange(baseOAuth2ProtectedResourceDetails.getAccessTokenUri(), HttpMethod.POST,
                            new HttpEntity<MultiValueMap<String, String>>(formData, headers), Map.class).getBody();
                //如果刷新异常,则坐进一步处理
                if(map.get("error")!=null){
                    // 返回指定格式的错误信息
                    response.setStatus(401);
                    response.setHeader("Content-Type", "application/json;charset=utf-8");
                    response.getWriter().print("{\"code\":1,\"message\":\""+map.get("error_description")+"\"}");
                    response.getWriter().flush();
                    //如果是网页,跳转到登陆页面
                    //response.sendRedirect("login");
                }else{
                    //如果刷新成功则存储cookie并且跳转到原来需要访问的页面
                    for(Object key:map.keySet()){
                        response.addCookie(new Cookie(key.toString(),map.get(key).toString()));
                    }
                    request.getRequestDispatcher(request.getRequestURI()).forward(request,response);
                }
            }else{
                //如果不是401异常，则以默认的方法继续处理其他异常
                super.commence(request,response,authException);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
 
    }
 
}
```

# 5.将处理器设置到过滤器上

- 由于spring security遵循适配器的设计模式，所以我们可以直接从配置类上配置此处理器

```
@EnableResourceServer
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
public abstract class ResServerConfig extends ResourceServerConfigurerAdapter {
 
    ...
 
    @Override
    public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
        super.configure(resources);
       
        resources.authenticationEntryPoint(new LLGAuthenticationEntryPoint());
      
    }
```

# 6.实战

### 6.1向授权服务器获取token

- 首先编写登陆控制器，通过restTemplate向授权服务器获取token并且存入cookie

```
PostMapping(value = "/login")
    public ResponseEntity<OAuth2AccessToken> login(@RequestBody @Valid LoginDTO loginDTO, BindingResult bindingResult, HttpServletResponse response) throws Exception {
        if (bindingResult.hasErrors()) {
            throw new Exception("登录信息格式错误");
        } else {
            //Http Basic 验证
            String clientAndSecret = oAuth2ClientProperties.getClientId() + ":" + oAuth2ClientProperties.getClientSecret();
            //这里需要注意为 Basic 而非 Bearer
            clientAndSecret = "Basic " + Base64.getEncoder().encodeToString(clientAndSecret.getBytes());
            HttpHeaders httpHeaders = new HttpHeaders();
            httpHeaders.set("Authorization", clientAndSecret);
            //授权请求信息
            MultiValueMap<String, String> map = new LinkedMultiValueMap<>();
            map.put("username", Collections.singletonList(loginDTO.getUsername()));
            map.put("password", Collections.singletonList(loginDTO.getPassword()));
            map.put("grant_type", Collections.singletonList(oAuth2ProtectedResourceDetails.getGrantType()));
            map.put("scope", oAuth2ProtectedResourceDetails.getScope());
            //HttpEntity
            HttpEntity httpEntity = new HttpEntity(map, httpHeaders);
            //获取 Token
            ResponseEntity<OAuth2AccessToken> body = restTemplate.exchange(oAuth2ProtectedResourceDetails.getAccessTokenUri(), HttpMethod.POST, httpEntity, OAuth2AccessToken.class);
            OAuth2AccessToken oAuth2AccessToken = body.getBody();
            response.addCookie(new Cookie("access_token", oAuth2AccessToken.getValue()));
            response.addCookie(new Cookie("refresh_token", oAuth2AccessToken.getRefreshToken().getValue()));
            return body;
        }
    }
```

- 之后我在这里通过idea的 HTTP Client 工具模拟请求获取token
- **获取access_token请求（/oauth/token）** 
  请求所需参数：client_id、client_secret、grant_type、username、password

![img](https://img-blog.csdn.net/2018102014413687?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODM0NDcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/2018102014422773?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODM0NDcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 6.2模拟失效token访问资源服务器

- 使用失效的token访问资源的时候，可以发现断点直接到达异常处理器，由此看出token确实是失效的并且进入了异常处理器进行处理，最终通过refresh_token获取到最新的token再次成功访问获取资源
- **刷新token请求（/oauth/token）** 
  请求所需参数：grant_type、refresh_token、client_id、client_secret 
  其中grant_type为固定值：grant_type=refresh_token

![img](https://img-blog.csdn.net/20181020144950324?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODM0NDcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20181020145027802?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODM0NDcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20181020145132518?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODM0NDcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 7.总结

本次由于对spring security oauth2了解不深入，导致在寻找异常抛出解决方法的时候折腾了一下，整体的思路并不复杂，只是用到了最普通的请求转发，但是需要对过滤器链有一定了解，打断点慢慢看是不错的选择。









# Re：从零开始的Spring Security Oauth2（一）

https://blog.csdn.net/u013815546/article/details/76898524

# Spring Security Oauth2 认证（获取token/刷新token）流程（password模式）

https://blog.csdn.net/bluuusea/article/details/80284458



# 史上最简单的Spring Security教程

https://blog.csdn.net/liuminglei1987/article/list/2



spring security fundamental

https://github.com/bharaththippireddy



在请求到达/oauth/token之前经过了ClientCredentialsTokenEndpointFilter这个过滤器，关键方法如下