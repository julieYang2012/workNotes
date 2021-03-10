# WebClient 

https://www.hangge.com/blog/cache/detail_2643.html

## 1 Parameters methods

```
Mono<String> mono = webClient
        .get() // GET 
        .uri("/{1}/{2}", "posts", "1")  // url
        .retrieve() // get response body
        .bodyToMono(String.class); //response data type

```

```
String type = "posts";
int id = 1;
 
Mono<String> mono = webClient
        .get() // GET 
        .uri("/{type}/{id}", type, id)  
        .retrieve() 
        .bodyToMono(String.class); 
        System.out.println(mono.block());
```

```
Map<String,Object> map = new HashMap<>();
map.put("type", "posts");
map.put("id", 1);
 
Mono<String> mono = webClient
        .get() // GET 
        .uri("/{type}/{id}", map)   
        .retrieve() 
        .bodyToMono(String.class); 
```



### 2 subscribe  method and block method

```
@RestController
public class HelloController {
 
    // create WebClient 
    private WebClient webClient = WebClient.builder()
            .baseUrl("http://jsonplaceholder.typicode.com")
            .build();
 
    @GetMapping("/test")
    public void test() {
        System.out.println("--- begin ---");
 
        Mono<String> mono = webClient
                .get() // GET 
                .uri("/posts/1")  
                .retrieve() 
                .bodyToMono(String.class);
 
        // asymchonize method
        mono.subscribe(result -> {
            System.out.println(result);
        });
         // System.out.println(mono.block());
 
        System.out.println("--- end ---");
        return;
    }
}
```

## 3 exhange method get the whole response include the header, cookie 



````
@RestController
public class HelloController {
 
    //  WebClient 
    private WebClient webClient = WebClient.builder()
            .baseUrl("http://jsonplaceholder.typicode.com")
            .build();
 
    @GetMapping("/test")
    public void test() {
        Mono<ClientResponse> mono = webClient
                .get() // GET 
                .uri("/posts/1")  // 
                .exchange();
 
        //get the whole response body
        ClientResponse response = mono.block();
 
        HttpStatus statusCode = response.statusCode();
        int statusCodeValue = response.rawStatusCode(); 
        Headers headers = response.headers(); 
 
        // get the body
        Mono<String> resultMono = response.bodyToMono(String.class);
        String body = resultMono.block();
 
        // print the results
        System.out.println("statusCode：" + statusCode);
        System.out.println("statusCodeValue：" + statusCodeValue);
        System.out.println("headers：" + headers.asHttpHeaders());
        System.out.println("body：" + body);
        return;
    }
}
````

## 4 response result 

1) convert to object 

```
@RestController
public class HelloController {
 
   
    private WebClient webClient = WebClient.builder()
            .baseUrl("http://jsonplaceholder.typicode.com")
            .build();
 
    @GetMapping("/test")
    public void test() {
        Mono<PostBean> mono = webClient
                .get() // GET 
                .uri("/posts/1")  
                .retrieve() 
                .bodyToMono(PostBean.class); 
        System.out.println(mono.block());
        return;
    }
}
```

```
public class PostBean {
    private int userId;
    private int id;
    private String title;
    private String body;
}
```

2) convert to set (bodyToFlux)

```
@RestController
public class HelloController {
 
    
    private WebClient webClient = WebClient.builder()
            .baseUrl("http://jsonplaceholder.typicode.com")
            .build();
 
    @GetMapping("/test")
    public void test() {
        Flux<PostBean> flux = webClient
                .get() // GET 
                .uri("/posts")  
                .retrieve() 
                .bodyToFlux(PostBean.class); 
        List<PostBean> posts = flux.collectList().block();
        System.out.println("number is：" + posts.size());
        return;
    }
}

```

