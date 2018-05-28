# Intelij idea软件的下载及使用

### Intelij idea 软件下载

下载地址：[mac版](https://www.jetbrains.com/idea/download/#section=mac) windows和mac版配置流程一样的，我这里就以mac版举例了

如下图：下载企业版功能会全一些，但是需要破解，后面我说下怎么破解

![](https://ws3.sinaimg.cn/large/006tNc79ly1fqq2xelef3j307v06ft8k.jpg)

### Intelij idea软件破解

网上搜了很多破解方案，确实成功的少一些，这里我找了一个博客，我试过了是ok的。

这个[博客](http://idea.lanyus.com)的作者还提供了自己搭建局域网破解方案，想试试的朋友可以试试的。

下面讲下破解步骤：

1、找到hosts文件，增加`0.0.0.0 account.jetbrains.com`在host文件内。（不会替换的朋友百度下吧，很简单的，mac电脑的朋友使用mac访达的`文件前往`功能输入`/etc/hosts`就能定位到了，其他系统自行百度吧）

2、打开Intelij idea软件，在欢迎页面右下角，点开`Configure`后再点击`Manage license`

**如图：**

![](https://ws2.sinaimg.cn/large/006tNc79ly1fqq32g7bm0j30m60fx0t4.jpg)

3、点击[博客](http://idea.lanyus.com)中的获取验证码按钮会得到ActivationCode，复制到上一步的图中位置点击ok即可。

![](https://ws4.sinaimg.cn/large/006tNc79ly1fqq32tjg1kj30il08dgm2.jpg)

建议有兴趣的朋友去按照博客作者的方案，搭建属于自己的[Intelij授权服务器](http://blog.lanyus.com/archives/174.html)。

### Intelij idea 软件导入Maven结构的工程

1、点击 `import Project` 选择项目根目录

![](https://ws1.sinaimg.cn/large/006tNc79ly1fqq2wdgvk1j30c707ha9x.jpg)

2、点击`Maven`后，然后一路`Next`按下去就ok了

![](https://ws1.sinaimg.cn/large/006tNc79ly1fqq2w00pzaj30o6073mx1.jpg)

