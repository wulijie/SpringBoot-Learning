# 使用Intellij中的Spring Initializr来快速构建Spring Boot/Cloud工程

使用嵌入的Intellij中的Spring Initializr工具，它同[Web](http://start.spring.io)提供的创建功能是一样的（同样你也可以使用这个网站创建），可以帮助我们快速的构建出一个基础的Spring Boot/Cloud工程。

选择`create New Project` => `Spring Initializr` => 使用default的由[Web](http://start.spring.io)提供的功能就可以；（Spring Boot 2.0 是需要java 1.8版本的）

![](https://ws2.sinaimg.cn/large/006tKfTcly1frqwrfbzfij30od0ai74f.jpg)

 点击 `Next` 等待片刻后，我们可以看到如下图所示的工程信息窗口，在这里我们可以编辑我们想要创建的工程信息。其中 `Type` => 可以改变我们要构建的工程类型，比如：Maven、Gradle；`Language` => 可以选择：Java、Groovy、Kotlin。`Group` => 一般填写公司名或者一些公益组织名等；`Artifact` => 写这个项目的名字；
![](https://ws1.sinaimg.cn/large/006tNc79ly1frr0cs68j7j30rd0gf74f.jpg)

点击 `Next` 进入选择SpringBoot版本和依赖管理的窗口，在这里值得我们关注的是，它不仅包含了`Spring Boot Starter POMs`  的各种依赖，还包含了`Spring Boot Cloud` 的各种依赖；我选用

![](https://ws2.sinaimg.cn/large/006tNc79ly1frr0i4nxvwj30rk0j4wer.jpg)

点击 `Next` 进入最后关于工程物理存储的一些细节，点击 `Finish` 就可以完成工程的构建了；
![](https://ws2.sinaimg.cn/large/006tNc79ly1frr0knotd4j30rk0j4dfy.jpg)

Intellij中的 `Spring Initializr` 虽然还是基于官方Web实现，但是通过工具来进行调用并直接将结果构建到我们的本地文件系统中，让整个构建流程变得更加顺畅，还没有体验过此功能的Spring Boot/Cloud爱好者们不妨可以尝试一下这种不同的构建方式。