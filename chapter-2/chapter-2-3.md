# 使用Thymeleaf模板引擎渲染web视图

Web应用的开发就绕不过渲染Web页面，Spring Boot 提供了很多默认配置的模板引擎；

主要是：`Thymeleaf ` 、 `FreeMarker` 、`Velocity` 、`Groovy` 、`Mustache`；

**Spring Boot建议使用这些模板引擎，避免使用JSP，若一定要使用JSP将无法实现Spring Boot的多种特性**；

后续重点学习几个模块引擎是如何使用的；

### Thymeleaf

[Thymeleaf](https://www.thymeleaf.org)是一个`XML/XHTML/HTML5`模板引擎，可用于`Web`与`非Web`环境中的应用开发。

它是一个开源的Java库，基于Apache License 2.0许可，由`Daniel Fernández`创建，该作者还是Java加密库`Jasypt`的作者。

Thymeleaf提供了一个用于整合`Spring MVC`的可选模块，在应用开发中，你可以使用Thymeleaf来完全代替JSP或其他模板引擎，如`Velocity`、`FreeMarker`等。

`Thymeleaf`的主要目标在于提供一种可被浏览器正确显示的、格式良好的模板创建方式，因此也可以用作`静态建模`。

你可以使用它创建经过验证的XML与HTML模板。`相对于编写逻辑或代码，开发者只需将标签属性添加到模板中即可`。

接下来，这些标签属性就会在DOM（文档对象模型）上执行预先制定好的逻辑。

### Spring Boot 项目资源说明及静态资源访问

在我们开发`Web`应用的时候，需要引用大量的`js`、`css`、`图片`等静态资源。

Spring Boot默认提供静态资源目录位置需置于classpath下，目录名需符合如下规则：

- `/static`
- `/public`
- `/resources`
- `/META-INF/resources`

例如：我们可以在`src/main/resources/`目录下创建`static`，在该位置放置一个`wlj.jpg`图片文件。启动程序后，尝试访问`http://localhost:8080/wlj.jpg`。如能显示图片，配置成功。

### Thymeleaf的配置及使用

**Thymeleaf的配置**

如有需要修改默认配置的时候，只需复制下面要修改的属性到`application.properties`中，并修改成需要的值，如修改模板文件的扩展名，修改默认的模板路径等。

```properties
#thymeleaf start
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.servlet.content-type=text/html
#开发时关闭缓存,不然没法看到实时页面
spring.thymeleaf.cache=false
#页面的存放路径就使用默认配置了
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.check-template-location=true
spring.thymeleaf.suffix=.html
#thymeleaf end

#更多的配置信息，请去官网查看文档哦
```

**Thymeleaf的使用**

在当前项目的`pom.xml`文件内引入`Thymeleaf`模块：

```xml
<dependencies>
        .....
    	.....

        <!--引入Web模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

		<!--引入Thymeleaf模块-->
        <dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>

    </dependencies>
```

在默认的模板路径`src/main/resources/templates`下编写模板文件即可；当然你可以自己改下配置信息，修改下模块的存放路径的；

**控制器**

```java
@Controller
public class HelloController {

    /**
     * 测试 thymeleaf
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

**Tips: 注意是`@Controller` 不是`@RestController`**

这两个的区别是：

```java
@RestController注解相当于 @ResponseBody ＋ @Controller合在一起的作用

使用@RestController注解Controller，则Controller中的方法无法返回jsp或者其他页面的

如果需要返回json，用于提供API，请使用@RestController

如果需要返回Web页面，用于展示页面，请使用@Controller
```

在默认目录`src/main/resources/templates/`下创建`hello.html`静态页面

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello Thymeleaf</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<!--/*@thymesVar id="name" type="java.lang.String"*/-->
<p th:text="'Hello！, ' + ${name} + '!'">Spring Boot</p>
</body>
</html>
```

运行项目，并在浏览器内输入`http://localhost:8080/hello`；

或者你传参，在浏览器内输入`http://localhost:8080/hello?name=thymeleaf`；

**运行效果**

![](https://ws3.sinaimg.cn/large/006tKfTcly1frsazl8fu0j30g907oaa3.jpg)

源代码就不提供了，因为真的很简单，如果还有运行疑问，给我提问题哦，我会耐心解答的；

具体更细节的使用及语法需要查看官方文档进行仔细研究，这里只做入门使用哦；后续专门学习一下`Thymeleaf`,然后写一篇更详尽的使用文档；