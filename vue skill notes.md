# vue skill notes

## 1, Vue 页面跳转方式 与 参数传递 编程导航

https://blog.csdn.net/muguli2008/article/details/102789504

```vue

<template>
 
    <!-- 在Vue中的<router-link>标签中，到页面最终还是转为<a>标签 -->
 
    <router-link to="/about">
        <button>打开关于我们 - 不带参数1</button>
    </router-link>
 
    <router-link :to="{path: '/about'}">
        <button>打开关于我们 - 不带参数2</button>
    </router-link>
</template>
```



```

<template>
    <router-link :to="{path: '/about', query: {id: 1024, name:'mupiao', age: 28 }}">
        <button>打开关于我们 - 带参数1</button>
    </router-link>
 
    <router-link :to="{name: 'About', params: {id: 1024, name:'mupiao', age: 28 }}">
        <button>打开关于我们 - 带参数2</button>
    </router-link>
</template>
```



```
// About页面（关于我们）
 
<template>
      <section>关于我们</section>
</template>
 
 
<script>
export default {
    name: "About",
    data() {
        return {};
    },
    created() {
        // 在Vue实例被创建之后的钩子函数中，接收home页面传过来的参数
 
        // 以query方式接收参数：【query传递数据是通过URL传递的，类似ajax中的get方式】
        console.log(this.$route.query.id);      // 1014
        console.log(this.$route.query.name);    // mupiao
        console.log(this.$route.query.age);     // 28
 
        // 以params方式接收参数：【params方式，类似ajax中的post方式】
        console.log(this.$route.params.id);      // 1014
        console.log(this.$route.params.name);    // mupiao
        console.log(this.$route.params.age);     // 28
    }
}
</script>
```



### **编程式路由跳转： this.$router.push()**

**1、**不带参数跳转

```html
<!-- Home页面（首页） -->

<template>
    <router-link :to="{path: '/about'}">
        <button>打开关于我们</button>
    </router-link>
 
    <button @click="open">打开关于我们</button>
</template>
 
<script>
export default {
    name: "Home",
    data() {
        return {};
    },
    methods: {
        open() {
            this.$router.push('/about');
        }
    },
}
</script>
```

 

**2、**带参数跳转

```javascript
<!-- Home页面（首页） -->



 



<template>



    <router-link :to="{path: '/about'}">



        <button>打开关于我们</button>



    </router-link>



 



    <button @click="open1">打开关于我们 - query方式</button>



    



    <button @click="open2">打开关于我们 - params方式</button>



</template>



 



<script>



export default {



    name: "Home",



    data() {



        return {};



    },



    methods: {



        // query方式



        open1() {  



            this.$router.push({



                path: '/about',



                query: {



                  id: 2048,



                  book: "了不起的Node.js",



                  job: "Web前端"



                }



            });



        },



 



        //⚠️注：如果要传递的参数很长时，请问params方式，因为query方式是通过URL传递的，而URL传参数长度是有限制的哦！！



 



        // params方式



        open2() {



            this.$router.push({



                name: "About", // ⚠️注：这里不能用path路径，只能用name【请对照router.js中的路由规则中的name项，还有就是一般首字母大写】，否则取不到传过去的数据



                params: {



                  id: 2048,



                  book: "了不起的Node.js",



                  job: "Web前端"



                }



            });



        }



    },



}



</script>
```

 

**3、**接收参数

```html

<!-- Home页面（首页） -->
 
<template>
    <router-link :to="{path: '/about'}">
        <button>打开关于我们</button>
    </router-link>
 
    <button @click="open1">打开关于我们 - query方式</button>
    
    <button @click="open2">打开关于我们 - params方式</button>
</template>
 
<script>
export default {
    name: "Home",
    data() {
        return {};
    },
    methods: {
        // query方式
        open1() {  
            this.$router.push({
                path: '/about',
                query: {
                  id: 2048,
                  book: "了不起的Node.js",
                  job: "Web前端"
                }
            });
        },
 
        //⚠️注：如果要传递的参数很长时，请问params方式，因为query方式是通过URL传递的，而URL传参数长度是有限制的哦！！
 
        // params方式
        open2() {
            this.$router.push({
                name: "About", // ⚠️注：这里不能用path路径，只能用name【请对照router.js中的路由规则中的name项，还有就是一般首字母大写】，否则取不到传过去的数据
                params: {
                  id: 2048,
                  book: "了不起的Node.js",
                  job: "Web前端"
                }
            });
        }
    },
}
</script
```

 

 

 

# 真正的页面跳转：

### 一、**HTML超链接跳转：<a href="http://www.xxx.com">跳转链接</a>**

```html
//打开外部链接

<a target="_blank" href="https://www.baidu.com/s?wd=Vue">百度一下 Vue</a>
```

##  

 

### 二、**浏览器BOM中的location.href跳转：**

```javascript
//在当前页面打开URL页面

window.location.href = "https://www.baidu.com/s?wd=Vue";
```

 

 

### **三、浏览器BOM中的window.open()跳转：**

```javascript
//打开一个新的浏览器窗口

window.open("https://www.baidu.com/s?wd=Vue", "_blank", "width=1000, height=500", true);
```

 