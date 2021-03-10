# spring依赖注入

## signleton 基本写法

```
public class Signleton01 {
    // 私有构造函数，防止别人实例化
    private Signleton01(){}
	// 静态属性，指向一个实例化对象
    private static final Signleton01 INSTANCE = new Signleton01();
	// 公共方法，以便别人获取到实例化对象属性
    public static Signleton01 getINSTANCE() {
        return INSTANCE;
    }
}
```

这三个元素也很好理解，别人想要用我这个类的实例对象就只能通过我提供的`getINSTANCE()`，他想new也new不了第二个对象，自然而然就保证了**该类只有唯一对象**。我们可以做个试验，跑100个线程同时获取该类的实例对象，然后打印出对象的hashCode，看看到底是不是获取的同一个对象：

```
public static void main(String[] args) {
    for (int i = 0; i < 100; i++) {
        new Thread(() -> {
            System.out.println(Signleton01.getINSTANCE().hashCode());
        }).start();
    }
}
```

结果如下：

```
...
834649078
834649078
834649078
834649078
834649078
...
```

### 优缺点[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#优缺点)

优点：写法简单，线程安全

缺点：消耗资源，即使程序从没有用到过该类对象，该类也会初始化一个对象出来

所以为了解决饿汗式的这个缺点， 我们就引出了第二种写法，懒汉式！

## 懒汉式[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#懒汉式)

### 基本写法[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#基本写法)

```
public class Singleton02 {
    // 私有构造函数，防止别人实例化
    private Singleton02() {}
	// 静态属性，指向一个实例化对象（注意，这里没有实例化对象哦）
    private static Singleton02 INSTANCE;
	// 公共方法，以便别人获取到实例化对象属性
    public static Singleton02 getINSTANCE() {
        if (INSTANCE == null) {
            INSTANCE = new Singleton02();
        }
        return INSTANCE;
    }

}
```

懒汉式的和饿汗式最大的区别是什么呢，就是只有在调用`getINSTANCE`的时候，才会创建实例，如果你从来没调用过，那么就不实例化对象。这个就比饿汗式更加节约资源，不过这种写法并不是懒汉式的完善写法，它有一个非常大的问题，就是线程不同步！我们可以按照之前那种方式创建100个线程测试一下结果：

````
...
1851261656
868907500
988762476
1031371881
593800070
...
````

可以看到这线程一同时拿，拿的都不是同一个对象，这完全就破坏了单例模式。因为很多线程在对象没有初始化前就进入到了`if (INSTANCE == null)`判断语句块里，自然而然就会new出不同的对象了。要解决这个线程不安全问题，就得上线程锁！

### synchronized写法

````
public class Singleton02 {

    private Singleton02() {}

    private static Singleton02 INSTANCE;
    
	// 注意，这里静态方法加了synchronized关键字
    public synchronized static Singleton02 getINSTANCE() {
        if (INSTANCE == null) {
            INSTANCE = new Singleton02();
        }
        return INSTANCE;
    }
}
````

当我们在静态方法加上`synchronized`关键字后，就可以保证这个方法在同一时间只会有一个线程能成功调用，也就顺理成章的解决了线程不安全问题。我们还是测试一下：

````
...
1226880356
1226880356
1226880356
1226880356
1226880356
...
````

不管多少个线程，拿到的都是同一个对象，达到了单例的要求！

### 优缺点[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#优缺点-1)

懒汉式连基本的线程安全都不能保证，就不做讨论了，我们这里主要说的事`synchronized`写法

优点：写法简单，节约资源（只有需要该对象的时候才会实例化）

缺点：耗性能

要知道每一次调用`getINSTANCE()`方法时都会上锁，这是非常耗性能的。那么为了解决这个好性能的问题，我们又引申出接下来的一种写法。

## 



## 双重检测[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#双重检测)

每一次调用`getINSTANCE()`方法都会上锁，这是完全没有必要的嘛，因为只有对象还没有实例化的时候我才需要上锁以保证线程安全，对象都实例化了，自然也不用担心后续的调用会new出新的对象。 所以我们这个锁，可以加在`if (INSTANCE == null)`判断语句块里面：

```java
public class Singleton03 {
    private Singleton03() {}

    private static Singleton03 INSTANCE;

    public static Singleton03 getINSTANCE() {
        if (INSTANCE == null) {
            // 只有在对象还没有实例化的时候才上锁
            synchronized (Singleton03.class) {
                INSTANCE = new Singleton03();
            }
        }
        return INSTANCE;
    }
}
```

这样就能节约一些性能，但是这样并没有做到线程安全哦！ 因为很多线程进入到`if (INSTANCE == null)`判断语句后，虽说是因为锁不能同时new对象了，但是如果锁一旦释放，那么其他线程依然会执行到`INSTANCE = new Singleton03()`语句，从而破坏了单例。所以在`synchronized`代码块内还要加一层判断：

```java
public class Singleton03 {
    private Singleton03() {}
	
    // 注意，使用双重检验写法要加上volatile关键字，避免指令重排（有个印象就行，这不是本文的重点）
    private static volatile Singleton03 INSTANCE;

    public static Singleton03 getINSTANCE() {
        if (INSTANCE == null) {
            // 只有在对象还没有实例化的时候才上锁
            synchronized (Singleton03.class) {
                // 额外加一层判断
                if (INSTANCE == null) {
                	INSTANCE = new Singleton03();
                }
            }
        }
        return INSTANCE;
    }
}
```

`synchronized`代码块外面一层判断，里面一层判断，就是有名的双重检测（DCL）了！里面的这一层判断加了之后呢，第一个线程的锁一旦释放也不用担心了，因为此时对象已经实例化，后续的线程也执行不了new语句，从而保证了线程安全！

### 优缺点[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#优缺点-2)

优点：节约资源（只有需要该对象的时候才会实例化）

缺点：写法复杂，耗性能（还是上了锁，还是耗性能）

虽然双重校验比`synchronized`懒汉式写法减少了很多锁性能消耗，但毕竟还是上了锁，所以为了解决这个锁性能消耗问题了，又引申出下一种写法。



## 内部类

话不多说，直接上代码：

```java
public class Singleton04 {
    // 老套路，将构造函数私有化
    private Singleton04() {}
	// 声明一个内部类，内部类里持有实例的引用
    private static class Inner {
        public static final Singleton04 INSTANCE = new Singleton04();
    }
	// 公共方法
    public static Singleton04 getINSTANCE() {
        return Inner.INSTANCE;
    }
}
```

这个写法非常像饿汉式写法，单例三元素还是那三元素，只不过多加了一个内部类，将实例引用放到内部类里而已。为啥要这样写呢？**因为JVM保证了内部类的线程安全，即一个内部类在整个程序中不会被重复加载**，并且如果你没有使用到内部类的话，是不会加载这个内部类的。这就非常巧妙的实现了线程安全以及节约资源的好处！

### 优缺点[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#优缺点-3)

优点：写法简单、节约资源（只有调用了`getINSTANCE()`方法才会加载内部类，才会实例化对象）、线程安全（JVM保证了内部类的线程安全）

缺点：会被序列化或者反射破坏单例



这个缺点可以说是**吹毛求疵**，因为之前所有写法都会被序列化、反射破坏单例。虽然说是吹毛求疵，但咱们搞技术的还是得做到了解全部细节，我来演示一下怎样破坏这个单例

### 通过反射破坏单例[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#通过反射破坏单例)

```java
public static void main(String[] args) throws Exception {
    // 创建100个线程同时访问实例
    for (int i = 0; i < 100; i++) {
        new Thread(() -> {
            System.out.println(Singleton04.getINSTANCE().hashCode());
        }).start();
    }

    // 反射破坏单例
    Class<Singleton04> clazz = Singleton04.class;
    // 拿到无参构造函数并将其设置为可访问，无视private
    Constructor<Singleton04> constructor = clazz.getDeclaredConstructor();
    constructor.setAccessible(true);
    // 创建对象
    Singleton04 singleton04 = constructor.newInstance();
    System.out.println("反射：" + singleton04.hashCode());
}
```

运行结果如下：

```
...
2115147268
2115147268
反射：1078694789
2115147268
2115147268
...
```

如果是通过正常的访问实例方法，是完全可以做到单例的要求，但是如果用反射的形式来创建一个对象，则就破坏了单例，一个程序中就出现了多个不同的实例对象。那么为了解决这个吹毛求疵的问题，聪明的前辈们想到了一个完美的写法！

## 枚举[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#枚举)

```java
// 注意，这里是枚举
public enum Singleton05 {
    // 实例
    INSTANCE;
	// 公共方法
    public static Singleton05 getINSTANCE() {
        return INSTANCE;
    }
}
```

哎嘿，不是说所有单例都是那三元素吗，这里怎么只有两个元素呀！这是因为**枚举就没有构造方法，自然而然就做到了私有化构造函数的效果，而且比私有化构造函数效果更好！**因为都没有构造函数了，连序列化和反射都破坏不了这种写法的单例！！

眼见为实，我们做个试验：

```java
public static void main(String[] args) throws Exception {
    // 创建100个线程同时访问实例
    for (int i = 0; i < 100; i++) {
        new Thread(() -> {
            System.out.println(Singleton05.getINSTANCE().hashCode());
        }).start();
    }

    // 反射破坏单例
    Class<Singleton05> clazz = Singleton05.class;
    // 拿到无参构造函数并将其设置为可访问，无视private
    Constructor<Singleton05> constructor = clazz.getDeclaredConstructor();
    constructor.setAccessible(true);
    // 创建对象
    Singleton05 singleton05 = constructor.newInstance();
    System.out.println("反射：" + singleton05.hashCode());
}
```

运行结果如下：

```
...
422057313
422057313
422057313
422057313

Exception in thread "main" java.lang.NoSuchMethodException: Singleton05.<init>()
	at java.lang.Class.getConstructor0(Class.java:3082)
	at java.lang.Class.getDeclaredConstructor(Class.java:2178)
```

当运行到反射那一块代码的时候，程序直接报错，原因就是我之前所说的一样，枚举没有构造方法，你自然就无法通过反射来创建对象了！

### 优缺点[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#优缺点-4)

此方法乃是最完美的方法，真是佩服想出这种写法的前辈！

# 总结[#](https://www.cnblogs.com/RudeCrab/p/14301082.html#总结)

五个写法全部介绍完毕，每个写法都有其特点，根据自己的需求来写就好了！每种写法理解其特点后，写出来也就非常轻松。就像我一开始说的一样，理解这五种写法也不是吊书袋，每一种写法都有其背后的思考，有些写法思路真的让人叹服，至少我了解到内部类和枚举写法的时候我心里就是：我靠！这都能想出来，太牛逼了吧......

好的代码就是艺术作品，希望我们都能码出好的艺术出来！









# 前言[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#前言)

设计模式是我们编程道路上绕不开的一环，用好了设计模式能够让代码拥有良好的维护性、可读性以及扩展性，它仿佛就是“优雅”的代名词，各个框架和库也都能见到它的身影。

正是因为它有种种好处，所以很多人在开发时总想将某个设计模式用到项目中来，然而往往会用得比较别扭。其中一部分原因是业务需求并不太符合所用的设计模式，还有一部分原因就是在Web项目中我们对象都是交由Spring框架的Ioc容器来管理，很多设计模式无法直接套用。那么在真正的项目开发中，我们就需要对设计模式做一个灵活的变通，让其能够和框架结合，在实际开发中发挥出真正的优势。

当项目引入IoC容器后，我们一般是通过依赖注入来使用各个对象，将设计模式和框架结合的关键点就在于此！本文会讲解如何通过依赖注入来完成以下三种设计模式：

- 单例模式
- 责任链模式
- 策略模式

由浅入深，让你在了解几个设计模式的同时掌握依赖注入的一些妙用。

本文所有代码都放在[Github](https://github.com/RudeCrab/rude-java)上，克隆下来即可运行查看效果。

# 实战[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#实战)

## 单例模式[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#单例模式)

单例应该是很多人接触的第一个设计模式，相比其他设计模式来说单例的概念非常简单，即**在一个进程中，某个类从始至终只有一个实例对象**。不过概念就算再简单，还是需要一点编码才能实现，R 之前的文章 [回字有四种写法，那你知道单例有五种写法吗](https://www.cnblogs.com/RudeCrab/p/14301082.html) 就有详细的讲解，这里对该设计模式就不过多介绍了，咱们直接来看看在实际开发中如何运用该模式。

交由Spring IoC容器管理的对象称之为Bean，每个Bean都有其作用域（scope），这个作用域可以理解为Spring控制Bean生命周期的方式。创建和销毁是生命周期中必不可少的节点，单例模式的重点自然是对象的创建。而Spring创建对象的过程对于我们来说是无感知的，即我们只需配置好Bean，然后通过依赖注入就可以使用对象了：

```java
@Service //和@Component功能一样，将该类声明为Bean交由容器管理
public class UserServiceImpl implements UserService{
}

@Controller
public class UserController {
    @Autowired // 依赖注入
    private UserService userService;
}
```

那这个对象的创建我们该如何控制呢？

其实，Bean默认的作用范围就是单例的，我们无需手写单例。要想验证Bean是否为单例很简单，我们在程序各个地方获取Bean后打印其`hashCode`就可以看是否为同一个对象了，比如两个不同的类中都注入了`UserService`：

```java
@Controller
public class UserController {
    @Autowired
    private UserService userService;
    
    public void test() {
        System.out.println(userService.hashCode());
    }
}

@Controller
public class OtherController {
    @Autowired
    private UserService userService;
    
    public void test() {
        System.out.println(userService.hashCode());
    }
}
```

打印结果会是两个相同的`hashCode`。

为什么Spring默认会用单例的形式来实例化Bean呢？这自然是因为单例可以节约资源，有很多类是没必要实例化多个对象的。

如果我们就是想每次获取Bean时都创建一个对象呢？我们可以在声明Bean的时候加上`@Scope`注解来配置其作用域：

```java
@Service
@Scope("prototype")
public class UserServiceImpl implements UserService{
}
```

这样当你每次获取Bean时都会创建一个实例。

Bean的作用域有以下几种，我们可以根据需求配置，大多数情况下我们用默认的单例就好了：

| 名称        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| singleton   | 默认作用范围。每个IoC容器只创建一个对象实例。                |
| prototype   | 被定义为多个对象实例。                                       |
| request     | 限定在HTTP请求的生命周期内。每个HTTP客户端请求都有自己的对象实例。 |
| session     | 限定在HttpSession的生命周期内。                              |
| application | 限定在ServletContext的生命周期内。                           |
| websocket   | 限定在WebSocket的生命周期内。                                |

这里要额外注意一点，Bean的单例并不能完全算传统意义上的单例，因为其作用域只能保证在IoC容器内保证只有一个对象实例，但是不能保证一个进程内只有一个对象实例。也就是说，如果你不通过Spring提供的方式获取Bean，而是自己创建了一个对象，此时程序就会有多个对象存在了：

```java
public void test() {
    // 自己new了一个对象
    System.out.println(new UserServiceImpl().hashCode());
}
```

这就是需要变通的地方，Spring可以说在我们日常开发中覆盖了每一个角落，只要自己不故意绕开Spring，那么保证IoC容器内的单例基本就等同于保证了整个程序内的单例。

## 责任链模式[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#责任链模式)

概念比较简单的单例讲解完后，咱们再来看看责任链模式。

### 模式讲解[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#模式讲解)

该模式并不复杂：一个请求可以被多个对象处理，这些对象连接成一条链并且沿着这条链传递请求，直到有对象处理它为止。该模式的好处是让请求者和接受者解耦，可以动态增删处理逻辑，让处理对象的职责拥有了非常高的灵活性。我们开发中常用的过滤器Filter和拦截器Interceptor就是运用了责任链模式。

光看介绍只会让人云里雾里，我们直接来看下该模式如何运用。

就拿工作中的请假审批来说，当我们发起一个请假申请的时候，一般会有多个审批者，每个审批者都代表着一个责任节点，都有自己的审批逻辑。我们假设有以下审批者：

> 组长Leader：只能审批不超过三天的请假；
> 经理Manger：只能审批不超过七天的请假；
> 老板Boss：能够审批任意天数。

咱们先定义一个请假审批的对象：

```java
public class Request {
    /**
     * 请求人姓名
     */
    private String name;
    /**
     * 请假天数。为了演示就简单按整天来算，不弄什么小时了
     */
    private Integer day;

    public Request(String name, Integer day) {
        this.name = name;
        this.day = day;
    }
    
    // 省略get、set方法
}
```

按照传统的写法是接受者收到这个对象后通过条件判断来进行相应的处理：

```java
public class Handler {
    public void process(Request request) {
        System.out.println("---");

        // Leader审批
        if (request.getDay() <= 3) {
            System.out.println(String.format("Leader已审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));
            return;
        }
        System.out.println(String.format("Leader无法审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));

        // Manger审批
        if (request.getDay() <= 7) {
            System.out.println(String.format("Manger已审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));
            return;
        }
        System.out.println(String.format("Manger无法审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));

        // Boss审批
        System.out.println(String.format("Boss已审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));

        System.out.println("---");
    }
}
```

在客户端模拟审批流程：

```java
public class App {
    public static void main( String[] args ) {
        Handler handler = new Handler();
        handler.process(new Request("张三", 2));
        handler.process(new Request("李四", 5));
        handler.process(new Request("王五", 14));
    }
}
```

打印结果如下：

```bash
---
Leader已审批【张三】的【2】天请假申请
---
Leader无法审批【李四】的【5】天请假申请
Manger已审批【李四】的【5】天请假申请
---
Leader无法审批【王五】的【14】天请假申请
Manger无法审批【王五】的【14】天请假申请
Boss已审批【王五】的【14】天请假申请
---
```

不难看出Handler类中的代码充满了坏味道！每个责任节点间的耦合度非常高，如果要增删某个节点，就要改动这一大段代码，很不灵活。而且这里演示的审批逻辑还只是打印一句话而已，在真实业务中处理逻辑可比这复杂多了，如果要改动起来简直就是灾难。

这时候我们的责任链模式就派上用场了！我们将每个责任节点封装成独立的对象，然后将这些对象组合起来变成一个链条，并通过统一入口挨个处理。

首先，我们要抽象出责任节点的接口，所有节点都实现该接口：

```java
public interface Handler {
    /**
     * 返回值为true，则代表放行，交由下一个节点处理
     * 返回值为false，则代表不放行
     */
    boolean process(Request request);
}
```

以Leader节点为例，实现该接口：

```java
public class LeaderHandler implements Handler{
    @Override
    public boolean process(Request request) {
        if (request.getDay() <= 3) {
            System.out.println(String.format("Leader已审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));
            // 处理完毕，不放行
            return false;
        }
        System.out.println(String.format("Leader无法审批【%s】的【%d】天请假申请", request.getName(), request.getDay()));
        // 放行
        return true;
    }
}
```

然后定义一个专门用来处理这些Handler的链条类：

```java
public class HandlerChain {
    // 存放所有Handler
    private List<Handler> handlers = new LinkedList<>();

    // 给外部提供一个增加Handler的入口
    public void addHandler(Handler handler) {
        this.handlers.add(handler);
    }

    public void process(Request request) {
        // 依次调用Handler
        for (Handler handler : handlers) {
            // 如果返回为false，中止调用
            if (!handler.process(request)) {
                break;
            }
        }
    }
    
}
```

现在我们来看下使用责任链是怎样执行审批流程的：

```java
public class App {
    public static void main( String[] args ) {
        // 构建责任链
        HandlerChain chain = new HandlerChain();
        chain.addHandler(new LeaderHandler());
        chain.addHandler(new ManagerHandler());
        chain.addHandler(new BossHandler());
        // 执行多个流程
        chain.process(new Request("张三", 2));
        chain.process(new Request("李四", 5));
        chain.process(new Request("王五", 14));
    }
}
```

打印结果和前面一致。

这样带来的好处是显而易见的，我们可以非常方便地增删责任节点，修改某个责任节点的逻辑也不会影响到其他的节点，每个节点只需关注自己的逻辑。并且责任链是按照固定顺序执行节点，按照自己想要的顺序添加各个对象即可方便地排列顺序。

此外责任链有很多变体，比如像Servlet的Filter执行下一个节点时还需要持有链条的引用：

```java
public class MyFilter implements Filter {
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
        if (...) {
            // 通过链条引用来放行
            chain.doFilter(req, resp);
        } else {
            // 如果没有调用chain的方法则代表中止往下传递
            ...
        }
    }
}
```

各责任链除了传递的方式不同，整体的链路逻辑也可以有所不同。

我们刚才演示的是将请求交由某一个节点进行处理，只要有一个处理了，后续就不用处理了。有些责任链目的不是找到某一个节点来处理，而是每个节点都做一些事，相当于一个流水线。

比如像刚才的审批流程，我们可以将逻辑改为一个请假申请需要每一个审批人都同意才算申请通过，Leader同意了后转给Manger审批，Manger同意了后转给Boss审批，只有Boss最终同意了才生效。

形式有多种，其核心概念是将请求对象链式传递，不脱离这一点就都可以算作责任链模式，无需太死守定义。

### 配合框架[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#配合框架)

责任链模式中，我们都是自己创建责任节点对象，然后将其添加到责任链条中。在实际开发中这样就会有一个问题，如果我们的责任节点里依赖注入了其它的Bean，那么手动创建对象的话则代表该对象就没有交由Spring管理，那些属性也就不会被依赖注入：

```java
public class LeaderHandler implements Handler{
    @Autowired // 手动创建LeaderHandler则该属性不会被注入
    private UserService userService;
}
```

此时我们就必须将各个节点对象也交由Spring来管理，然后通过Spring来获取这些对象实例，再将这些对象实例放置到责任链中。其实这种方式大部分人都接触过，Spring MVC的拦截器Interceptor就是这样使用的：

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 获取Bean，添加到责任链中（注意哦，这里是调用的方法来获取对象，而不是new出对象）
        registry.addInterceptor(loginInterceptor());
        registry.addInterceptor(authInterceptor());
    }
    
    // 通过@Bean注解将自定义拦截器交由Spring管理
    @Bean
    public LoginInterceptor loginInterceptor() {return new LoginInterceptor();}
    @Bean
    public AuthInterceptor authInterceptor() {return new AuthInterceptor();}
}
```

`InterceptorRegistry`就相当于链条类了，该对象由Spring MVC传递给我们，好让我们添加拦截器，后续Spring MVC会自行调用责任链，我们无需操心。

别人框架定义的责任链会由框架调用，那我们自定义的责任链该如何调用呢？这里有一个更为简便的方式，那就是**将Bean依赖注入到集合中**！

我们日常开发时都是使用依赖注入获取单个Bean，这是因为我们声明的接口或者父类通常只需一个实现类就可以搞定业务需求了。而刚才我们自定义的Handler接口下会有多个实现类，此时我们就可以一次性注入多个Bean！咱们现在就来改造一下之前的代码。

首先，将每个Handler实现类加上`@Service`注解，将其声明为Bean：

```java
@Service
public class LeaderHandler implements Handler{
    ...
}

@Service
public class ManagerHandler implements Handler{
    ...
}

@Service
public class BossHandler implements Handler{
    ...
}
```

然后我们来改造一下我们的链条类，将其也声明为一个Bean，然后直接在成员变量上加上`@Autowired`注解。既然都通过依赖注入来实现了，那么就无需手动再新增责任节点，所以我们将之前的添加节点的方法给去除：

```java
@Service
public class HandlerChain {
    @Autowired
    private List<Handler> handlers;

    public void process(Request request) {
        // 依次调用Handler
        for (Handler handler : handlers) {
            // 如果返回为false,中止调用
            if (!handler.process(request)) {
                break;
            }
        }
    }

}
```

没错，依赖注入非常强大，不止能够注入单个对象，还可以注入多个！这样一来就非常方便了，我们只需实现Handler接口，将实现类声明为Bean，就会自动被注入到责任链中，我们甚至都不用手动添加。要想执行责任链也特别简单，只需获取HandlerChain然后调用即可：

```java
@Controller
public class UserController {
    @Autowired
    private HandlerChain chain;

    public void process() {
        chain.process(new Request("张三", 2));
        chain.process(new Request("李四", 5));
        chain.process(new Request("王五", 14));
    }
    
}
```

执行效果如下：

```bash
---
Boss已审批【张三】的【2】天请假申请
---
Boss已审批【李四】的【5】天请假申请
---
Boss已审批【王五】的【14】天请假申请
```

咦，全部都是Boss审批了，为啥前面两个节点没有生效呢？因为我们还没有配置Bean注入到集合中的顺序，我们需要加上`@Order`注解来控制Bean的装配顺序，数字越小越靠前：

```java
@Order(1)
@Service
public class LeaderHandler implements Handler{
    ...
}

@Order(2)
@Service
public class ManagerHandler implements Handler{
    ...
}

@Order(3)
@Service
public class BossHandler implements Handler{
    ...
}
```

这样我们自定义的责任链模式就完美融入到Spring中了！

## 策略模式[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#策略模式)

乘热打铁，我们现在再来讲解一个新的模式！

### 模式讲解[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#模式讲解-1)

我们开发中经常碰到这样的需求：需要根据不同的情况执行不同的操作。比如我们购物最常见的邮费，不同的地区、不同的商品邮费都会不同。假设现在需求是这样的：

> 包邮地区：没有超过10KG的货物免邮，10KG以上8元；
>
> 邻近地区：没有超过10KG的货物8元，10KG以上16元；
>
> 偏远地区：没有超过10KG的货物16元，10KG以上15KG以下24元， 15KG以上32元。

那我们计算邮费的方法大概是这样的：

```java
// 为了方便演示，重量和金额就简单设置为整型
public long calPostage(String zone, int weight) {
    // 包邮地区
    if ("freeZone".equals(zone)) {
        if (weight <= 10) {
            return 0;
        } else {
            return 8;
        }
    }

    // 近距离地区
    if ("nearZone".equals(zone)) {
        if (weight <= 10) {
            return 8;
        } else {
            return 16;
        }
    }

    // 偏远地区
    if ("farZone".equals(zone)) {
        if (weight <= 10) {
            return 16;
        } else if (weight <= 15) {
            return 24;
        } else {
            return 32;
        }
    }
	
    return 0;
}
```

这么点邮费规则就写了如此长的代码，要是规则稍微再复杂点简直就更长了。而且如果规则有变，就要对这一大块代码缝缝补补，久而久之代码就会变得非常难以维护。

我们首先想到的优化方式是将每一块的计算封装成方法：

```java
public long calPostage(String zone, int weight) {
    // 包邮地区
    if ("freeZone".equals(zone)) {
        return calFreeZonePostage(weight);
    }

    // 近距离地区
    if ("nearZone".equals(zone)) {
        return calNearZonePostage(weight);
    }

    // 偏远地区
    if ("farZone".equals(zone)) {
        return calFarZonePostage(weight);
    }
	
    return 0;
}
```

这样确实不错，大部分情况下也能满足需求，可依然不够灵活。

因为这些规则都是写死在我们方法内的，如果调用者想使用自己的规则，或者经常修改规则呢？总不能动不动就修改我们写好的代码吧。要知道邮费计算只是订单价格计算的一个小环节，我们固然可以写好几种规则定式来提供服务，但也得允许别人自定义规则。此时，我们更应该将邮费计算操作高度抽象成一个接口，有不同的计算规则就实现不同的类。不同规则代表不同策略，这种方式就是我们的策略模式！我们来看下具体写法：

首先，封装一个邮费计算接口：

```java
public interface PostageStrategy {
    long calPostage(int weight);
}
```

然后，我们将那几个地区规则封装成不同的实现类，拿包邮地区示例：

```java
public class FreeZonePostageStrategy implements PostageStrategy{
    @Override
    public long calPostage(int weight) {
        if (weight <= 10) {
            return 0;
        } else {
            return 8;
        }
    }
}
```

最后，要应用策略的话我们还需要一个专门类：

```java
public class PostageContext {
    // 持有某个策略
    private PostageStrategy postageStrategy = new FreeZonePostageStrategy();
    // 允许调用方设置新的策略
    public void setPostageStrategy(PostageStrategy postageStrategy) {
        this.postageStrategy = postageStrategy;
    }
    // 供调用方执行策略
    public long calPostage(int weight) {
        return postageStrategy.calPostage(weight);
    }
}
```

这样，调用方既可以使用我们已有的策略，也可以非常方便地修改或自定义策略：

```java
public long calPrice(User user, int weight) {
    PostageContext postageContext = new PostageContext();
    // 自定义策略
    if ("RudeCrab".equals(user.getName())) {
        // VIP客户，20KG以下一律包邮，20KG以上只收5元
        postageContext.setPostageStrategy(w -> w <= 20 ? 0 : 5);
        return postageContext.calPostage(weight);
    }
    // 包邮地区策略
    if ("freeZone".equals(user.getZone())) {
        postageContext.setPostageStrategy(new FreeZonePostageStrategy());
        return postageContext.calPostage(weight);
    }
    // 邻近地区策略
    if ("nearZone".equals(user.getZone())) {
        postageContext.setPostageStrategy(new NearZonePostageStrategy());
        return postageContext.calPostage(weight);
    }
    
    ...
    
    return 0;
}
```

可以看到，简单的逻辑直接使用Lambda表达式就完成了自定义策略，若逻辑复杂的话也可以直接新建一个实现类来完成。

这就是策略模式的魅力所在，允许调用方使用不同的策略来得到不同的结果，以达到最大的灵活性！

尽管好处很多，但策略模式缺点也很明显：

- 可能会造成策略类过多的情况，有多少规则就有多少类
- 策略模式只是将逻辑分发到不同实现类中，调用方的`if、else`一个都没减少。
- 调用方需要知道所有策略类才能使用现有的逻辑。

大部分缺点可以配合工厂模式或者反射来解决，但这样又增加了系统的复杂性。那有没有既能弥补缺点又不复杂的方案呢，当然是有的，这就是我接下来要讲解的内容。在策略模式配合Spring框架的同时，也能弥补模式本身的缺点！

### 配合框架[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#配合框架-1)

经过责任链模式咱们就可以发现，其实所谓的配合框架就是将我们的对象交给Spring来管理，然后通过Spring调用Bean即可。策略模式中，咱们每个策略类都是手动实例化的，那咱们要做的第一步毫无疑问就是将这些策略类声明为Bean：

```java
@Service("freeZone") // 注解中的值代表Bean的名称，这里为什么要这样做，等下我会讲解
public class FreeZonePostageStrategy implements PostageStrategy{
	...
}

@Service("nearZone")
public class NearZonePostageStrategy implements PostageStrategy{
	...
}

@Service("farZone")
public class FarZonePostageStrategy implements PostageStrategy{
	...
}
```

然后我们就要通过Spring获取这些Bean。有人可能会自然联想到，我们还是将这些实现类都注入到一个集合中，然后遍历使用。这确实可以，不过太麻烦了。依赖注入可是非常强大的，不仅能将Bean注入到集合中，**还能将其注入到Map中**！

来看具体用法：

```java
@Controller
public class OrderController {
    @Autowired
    private Map<String, PostageStrategy> map;

    public void calPrice(User user, int weight) {
        map.get(user.getZone()).calPostage(weight);
    }
}
```

大声告诉我，清不清爽！简不简洁！优不优雅！

依赖注入能够将Bean注入到Map中，其中Key为Bean的名称，Value为Bean对象，这也就是我前面要在`@Service`注解上设置值的原因，只有这样才能将让调用方直接通过Map的`get`方法获取到Bean，继而使用该Bean对象。

我们之前的`PostageContext`类可以不要了，什么时候想调用某策略，直接在调用处注入Map即可。

通过这种方式，我们不仅让策略模式完全融入到Spring框架中，还完美解决了`if、else`过多等问题！我们要想新增策略，只需新建一个实现类并将其声明成Bean就行了，原有调用方无需改动一行代码即可生效。

> 小贴士：如果一个接口或者父类有多个实现类，但我又只想依赖注入单个对象，可以使用`@Qualifier("Bean的名称")`注解来获取指定的Bean。

# 总结[#](https://www.cnblogs.com/RudeCrab/p/14306279.html#总结)

本文介绍了三种设计模式，以及各设计模式在Spring框架下是如何运用的！这三种设计模式对应的依赖注入方式如下：

- 单例模式：依赖注入单个对象
- 责任链模式：依赖注入集合
- 策略模式：依赖注入Map

将设计模式和Spring框架配合的关键点就在于，**如何将模式中的对象交由Spring管理**。这是本文的核心，这一点思考清楚了，各个设计模式才能灵活使用。

[
  ](https://img2020.cnblogs.com/blog/1496775/202101/1496775-20210108125800650-1747993399.png)