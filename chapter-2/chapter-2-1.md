# 构建一个较为复杂的 RESTful API 

### Spring Boot 注解说明

- `@Controller`：修饰class，用来创建处理http请求的对象 
- `@RestController`：Spring4之后加入的注解，原来在`@Controller`中返回json需要`@ResponseBody`来配合，如果直接用`@RestController`替代`@Controller`就不需要再配置`@ResponseBody`，默认返回json格式。 
- `@RequestMapping`：配置url映射

接下来我们使用Spring MVC来实现一组对User对象操作的RESTful API，配合注释详细说明在Spring MVC中如何

映射HTTP请求、如何传参、如何编写单元测试。

**RESTful API具体设计如下：**

| 请求类型 | URL         | 功能说明           |
| :------- | :---------- | :----------------- |
| GET      | /user/users | 查询用户列表       |
| POST     | /user       | 创建一个用户       |
| GET      | /user/id    | 根据id查询一个用户 |
| PUT      | /user/id    | 根据id更新一个用户 |
| DELETE   | /user/id    | 根据id删除一个用户 |

**创建User实体**

```java
public class User {
    private Long id;
    private String name;
    private Integer age;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

**创建User的操作接口Controller**

```java
@RestController
@RequestMapping(value = "/user")     // 通过这里配置使下面的映射都在/user下
public class UserController {
    // 创建线程安全的Map
    static Map<Long, User> users = Collections.synchronizedMap(new HashMap());

    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public List<User> getUserList() {
        // 处理"/user/users"的GET请求，用来获取用户列表
        // 还可以通过@RequestParam从页面中传递参数来进行查询条件或者翻页信息的传递
        List<User> r = new ArrayList(users.values());
        return r;
    }

    @RequestMapping(value = "/", method = RequestMethod.POST)
    public String postUser(@ModelAttribute User user) {
        // 处理"/user/"的POST请求，用来创建User
        // 除了@ModelAttribute绑定参数之外，还可以通过@RequestParam从页面中传递参数
        users.put(user.getId(), user);
        return "success";
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public User getUser(@PathVariable Long id) {
        // 处理"/user/{id}"的GET请求，用来获取url中id值的User信息
        // url中的id可通过@PathVariable绑定到函数的参数中
        return users.get(id);
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.PUT)
    public String putUser(@PathVariable Long id, @ModelAttribute User user) {
        // 处理"/user/{id}"的PUT请求，用来更新User信息
        User u = users.get(id);
        u.setName(user.getName());
        u.setAge(user.getAge());
        users.put(id, u);
        return "success";
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
    public String deleteUser(@PathVariable Long id) {
        // 处理"/user/{id}"的DELETE请求，用来删除User
        users.remove(id);
        return "success";
    }
}
```

下面针对该Controller编写测试用例验证正确性，具体如下。当然也可以通过浏览器插件等进行请求提交验证。

```java
package org.wlj.demo;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.mock.web.MockServletContext;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.RequestBuilder;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.wlj.demo.web.UserController;

import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = MockServletContext.class)
@WebAppConfiguration
public class DemoApplicationTests {


    private MockMvc mvc;

    @Before
    public void setUp() throws Exception {
        mvc = MockMvcBuilders.standaloneSetup(new UserController()).build();
    }


    @Test
    public void testUserController() throws Exception {
        // 测试UserController
        RequestBuilder request = null;

        // 1、get查一下user列表，应该为空
        request = get("/user/users");
        mvc.perform(request)
                .andExpect(status().isOk())
                .andExpect(content()
                        .string(equalTo("[]")));

        // 2、post提交一个user
        request = post("/user/")
                .param("id", "1")
                .param("name", "wlj")
                .param("age", "20");
        mvc.perform(request)
                .andExpect(content()
                        .string(equalTo("success")));

        // 3、get获取user列表，应该有刚才插入的数据
        request = get("/user/users");
        mvc.perform(request)
                .andExpect(status().isOk())
                .andExpect(content()
                        .string(equalTo(
                                "[{\"id\":1,\"name\":\"wlj\",\"age\":20}]")));

        // 4、put修改id为1的user
        request = put("/user/1")
                .param("name", "wlj")
                .param("age", "30");
        mvc.perform(request)
                .andExpect(content().string(equalTo("success")));

        // 5、get一个id为1的user
        request = get("/user/1");
        mvc.perform(request)
                .andExpect(content()
                        .string(equalTo("{\"id\":1,\"name\":\"wlj\",\"age\":30}")));

        // 6、del删除id为1的user
        request = delete("/user/1");
        mvc.perform(request)
                .andExpect(content().string(equalTo("success")));

        // 7、get查一下user列表，应该为空
        request = get("/user/users");
        mvc.perform(request)
                .andExpect(status().isOk())
                .andExpect(content()
                        .string(equalTo("[]")));
    }
}

```

**运行结果：**

![](https://ws3.sinaimg.cn/large/006tKfTcly1frs7mi1ivrj30ln08gwem.jpg)

到此为止，我们通过引入Web模块（[不会的同学，请看这里](https://github.com/wulijie/SpringBoot-Learning/blob/master/chapter-1/chapter-1-3.md)）就可以轻松利用Spring MVC的功能，以非常简洁的代码完成了对User对象的RESTful API的创建以及单元测试的编写。当然接口提供的数据肯定不是在内存里存储了，后续学习数据库的接入与使用。