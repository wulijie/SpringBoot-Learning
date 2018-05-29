# Hello World

这章节分析SpringBoot项目结构及如何输出`Hello World` 到 Web页面；

### 项目结构解析

![](https://ws3.sinaimg.cn/large/006tNc79ly1frr6o3epftj30wr0cdt94.jpg)

如上图所示，**Spring Boot**的基础结构共三个文件（具体路径根据用户生成项目时填写的Group所有差异）:

* `src/main/java/` 下的程序入口 `DemoApplication.java`
* `src/main/resources`下的配置文件：`application.properties`
* `src/test/`下的测试入口：`DemoApplicationTests.java`

生成的 `DemoApplication.java` 和 `DemoApplicationTests.java` 类都可以直接运行来启动当前创建的项目，由于目前该项目未配合任何数据访问或Web模块，程序会在加载完Spring之后结束运行。

### 引入Web模块

当前项目的`pom.xml`内容如下，仅引入了两个模块：

* `spring-boot-starter`：核心模块，包括自动配置支持、日志和YAML
* `spring-boot-starter-test`：测试模块，包括 `JUnit`、`Hamcrest`、`Mockito`

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

引入Web模块，需添加`spring-boot-starter-web`模块：

```xml
<dependencies>
        .....
    	.....

        <!--引入Web模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>
```

### 编写HelloWorld服务

创建`package` 命名为 `org.wlj.demo.web` (根据实际情况修改)；

创建 `HelloController` 类，内容如下：

```java
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }
}
```

启动主程序，打开浏览器访问 `http://localhost:8080/hello`，可以看到页面输出`Hello World`

**启动方式如下图：**

![](https://ws2.sinaimg.cn/large/006tNc79ly1frr77y2629j30ph0a33yt.jpg)

### 编写单元测试用例



打开的`src/test/`下的测试入口`DemoApplicationTests.java`类。下面编写一个简单的单元测试来模拟http请求，具体如下：

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = MockServletContext.class)
@WebAppConfiguration
public class DemoApplicationTests {

    private MockMvc mvc;

    @Before
    public void setUp() throws Exception {
        mvc = MockMvcBuilders.standaloneSetup(new HelloController()).build();
    }

    @Test
    public void getHello() throws Exception {
    	mvc.perform(MockMvcRequestBuilders.get("/hello")
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string(equalTo("Hello World")));
    }
}
```

使用 `MockServletContext` 来构建一个空的 `WebApplicationContext` ，这样我们创建的`HelloController` 就可以在 `@Before` 函数中创建并传递到 `MockMvcBuilders.standaloneSetup（）` 函数中。

**注意** 引入下面内容，让`status`、`content`、`equalTo` 函数可用：

```java
import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
```

单元测试，我后面会着重一篇进行研究，这里只是入门学习一下哦；

至此已完成目标，通过`Maven`构建了一个空白`Spring Boot`项目，再通过引入web模块实现了一个简单的请求处理。