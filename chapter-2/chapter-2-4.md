# 使用Freemarker模板引擎渲染web视图

### FreeMarker

介绍说明及优势

* [FreeMarker](http://freemarker.foofun.cn)是一个模板引擎，一个基于模板生成文本输出的通用工具，用纯Java编写


* [FreeMarker](http://freemarker.foofun.cn)不是一个Web应用框架，而适合作为Web应用框架一个组件
* [FreeMarker](http://freemarker.foofun.cn)与容器无关，因为它并不知道HTTP或Servlet；FreeMarker同样可以应用于非Web应用程序环境
* [FreeMarker](http://freemarker.foofun.cn)对jsp标签支持良好，性能非常不错
* [FreeMarker](http://freemarker.foofun.cn)内置大量常用功能，使用非常方便
* [FreeMarker](http://freemarker.foofun.cn)被设计用来生成HTML Web页面，特别是基于MVC模式的应用程序。

###FreeMarker 的使用

 在项目的`pom.xml`文件中引入`FreeMarker`的依赖：

```xml
<dependencies>
        ……
    	……
		
    	<!--引入Web模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--引入 freemarker-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>


    </dependencies>
```

在`application.properties` 填写配置文件

```properties
#freemarker start
spring.freemarker.allow-request-override=false
spring.freemarker.enabled=true
spring.freemarker.content-type=text/html
spring.freemarker.charset=UTF-8
#开发时关闭缓存,不然没法看到实时页面
spring.freemarker.cache=false
spring.freemarker.suffix=.ftl
#页面的存放路径就使用默认配置了
spring.freemarker.prefix=classpath:/templates/
spring.freemarker.check-template-location=true
spring.freemarker.expose-request-attributes=false
spring.freemarker.expose-session-attributes=false
spring.freemarker.expose-spring-macro-helpers=false
#freemarker end
```



在`src/main/resource/`创建一个`templates`文件夹，后缀为`*.ftl`，里面新建一个`hello.ftl`的文件，内容如下:

```xml
<!DOCTYPE html>
<html>
<head>
    <title>Hello freemarker</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<h1>Hello,${name}</h1>
</body>
</html>
```

创建`HelloController`控制器：

```java
@Controller
public class HelloController {
    /**
     * 测试 freemarker
     *
     * @return
     */
    @RequestMapping(value = "/hello", method = RequestMethod.GET)
    public String thymeleaf(HttpServletRequest request,
                            //@RequestParam 设置请求参数 非必须，默认值为 wlj
                            @RequestParam(value = "name",
                                    required = false,
                                    defaultValue = "wlj") String name) {
        // 加入一个属性，用来在模板中读取
        request.setAttribute("name", name);
        // return模板文件的名称，对应src/main/resources/templates/hello.html
        return "hello";
    }
}
```

