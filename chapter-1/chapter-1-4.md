# 工程配置文件详解

我们在Spring Boot使用过程中，最直观的感受就是没有了原来自己整合Spring应用时繁多的XML配置内容，替代它的是在`pom.xml`中引入模块化的`Starter POMs`，其中各个模块都有自己的默认配置，所以如果不是特殊应用场景，就只需要在 `application.properties` 中完成一些属性配置就能开启各模块的应用。

关于`application.properties`的使用，主要用来配置数据库连接、日志相关配置等。(这个后面实战项目的时候会提及具体使用)，本文主要是讲解在`application.properties`配置中的其他特性和使用方法。

### 配置类型及说明

在Spring Boot 2.0中对配置属性加载的时候会除了像1.x版本时候那样**移除特殊字符**外，还会将配置均以**全小写**的方式进行匹配和加载。所以，下面的4种配置方式都是等价的：

#### 简单类型

**properties格式：**

```properties
spring.jpa.databaseplatform=mysql
spring.jpa.database-platform=mysql
spring.jpa.databasePlatform=mysql
spring.JPA.database_platform=mysql
```

**yaml格式：**

```yaml
spring:
  jpa:
    databaseplatform: mysql
    database-platform: mysql
    databasePlatform: mysql
    database_platform: mysql
```

**Tips：推荐使用全小写配合-分隔符的方式来配置，比如：spring.jpa.database-platform=mysql**

#### List 类型

在properties文件中使用`[]`来定位列表类型，比如：

```properties
spring.my-example.url[0]=http://example.com
spring.my-example.url[1]=http://spring.io
```

也支持使用**逗号**分割的配置方式，上面与下面的配置是等价的：

```properties
spring.my-example.url=http://example.com,http://spring.io
```

而在 **yaml** 文件中使用可以使用如下配置：

```yaml
spring:
  my-example:
    url:
      - http://example.com
      - http://spring.io
```

也支持逗号分隔的方式：

```yaml
spring:
  my-example:
    url: http://example.com, http://spring.io
```

**Tips：在Spring Boot 2.0中对于List类型的配置必须是连续的**；

所以如下配置是不允许的（不然会抛异常`UnboundConfigurationPropertiesException`）：

```properties
foo[0]=a
foo[2]=b
```

**在Spring Boot 1.x中上述配置是可以的，foo[1]由于没有配置，它的值会是null**

#### Map 类型

**properties格式：**

```properties
spring.my-example.foo=bar
spring.my-example.hello=world
```

**yaml格式：**

```yaml
spring:
  my-example:
    foo: bar
    hello: world
```

**Tips：如果Map类型的key包含非字母数字和-的字符，需要用[]括起来，比如：**

```properties
spring:
  my-example:
    '[foo.baz]': bar
```

### 配置文件绑定及属性的读取

#### 简单类型

**properties属性**

```properties
org.demo.wlj.name=wlj
org.demo.wlj.title=Spring-Boot
```

然后通过`@Value("${属性名}")`注解来加载对应的配置属性，具体如下：

```java
@Component
public class DemoProperties {
    @Value("${org.demo.wlj.name}")
    private String name;
    @Value("${org.demo.wlj.title}")
    private String title;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }
}
```

按照惯例，通过单元测试来验证BlogProperties中的属性是否已经根据配置文件加载了。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {
    @Autowired
    private DemoProperties demoProperties;


    @Test
    public void getHello() throws Exception {
        Assert.assertEquals(demoProperties.getName(), "wlj");
        Assert.assertEquals(demoProperties.getTitle(), "Spring-Boot");
    }
}
```

是不是不会执行单元测试？贴心的我给你贴个图：

在你想要执行的Test文件上点击`右键` => `Run DemoApplicationTests` 查看测试用例是否都通过就可以了。

![](https://ws4.sinaimg.cn/large/006tKfTcly1frs1amvr04j30oq0ctdgg.jpg)

#### List 类型

**properties属性**

```properties
spring.my-example.url[0]=http://example.com
spring.my-example.url[1]=http://spring.io
```

获取这个配置的代码：

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);
        // 绑定List配置
        Binder binder = Binder.get(context.getEnvironment());
        List<String> post = binder
                .bind("spring.my-example.url", Bindable.listOf(String.class))
                .get();
        System.out.println(post);
    }
}
```



### 参数间的引用

在`application.properties`中的各个参数之间也可以直接引用来使用，就像下面的设置：

```properties
org.demo.wlj.name=wlj
org.demo.wlj.title=Spring-Boot
org.demo.wlj.desc=${org.demo.wlj.name} 正在努力的学习《${org.demo.wlj.title}》
```

`org.demo.wlj.desc`参数上文中定义的`name`和`title`属性，最后该属性的最后值就是 `wlj 正在努力的学习 Spring-Boot`；

### 使用随机数

在一些情况下，有些参数我们需要希望它不是一个固定的值，比如密钥、服务端口等。Spring Boot的属性配置文件中可以通过`${random}`来产生int值、long值或者string字符串，来支持属性的随机值。

```properties
# 随机字符串
org.demo.wlj.value=${random.value}
# 随机int
org.demo.wlj.number=${random.int}
# 随机long
org.demo.wlj.bignumber=${random.long}
# 10以内的随机数
org.demo.wlj.test1=${random.int(10)}
# 10-20的随机数
org.demo.wlj.test2=${random.int[10,20]}
```

### 通过命令行设置属性

在命令行终端输入：`java -jar xxx.jar --server.port=8888` ；

这个命令等价于：`application.properties`中添加属性`server.port=8888`

通过命令行来修改属性值固然提供了不错的便利性，但是通过命令行就能更改应用运行的参数，那岂不是很不安全？是的，所以Spring Boot也贴心的提供了屏蔽命令行访问属性的设置，只需要这句设置就能屏蔽：`SpringApplication.setAddCommandLineProperties(false)`。

### 多环境配置

开发过程中，我们经常会遇到开发、测试、生产环境来回切换的问题，所以需要熟悉多环境配置；

在Spring Boot中多环境配置文件名需要满足`application-{profile}.properties`的格式，其中`{profile}`对应你的环境标识，比如：

- `application-dev.properties`：开发环境
- `application-test.properties`：测试环境
- `application-prod.properties`：生产环境

但是哪个文件被加载，就需要在 `application.properties` 文件中通过设置 `spring.profiles.active`属性来设置，其值对应`{profile}`值；

比如：`spring.profiles.active=test`就会加载`application-test.properties`配置文件内容;

