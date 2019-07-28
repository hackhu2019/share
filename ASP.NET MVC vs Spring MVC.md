C# 是微软出品对标 Java 的语言，在 web 开发方面，二者都在表现层实现了自己的 MVC 框架。从一个简单的项目开始对比，看看二者有何差异。
# 项目结构
首先来看由 SpringBoot 生成的 MVC 项目模板（勾选了 web、Thymeleaf 、mybatis），controller、model 文件夹与 SpringBoot 项目主文件 XXspringbootApplication 位于同一目录下，view 文件位于应用 Thymeleaf 模板文件夹（templates）下。
![Spring MVC](https://img-blog.csdnimg.cn/2019072721114435.png)
再看官配编辑器 VS 生成的 ASP.NET MVC 基本项目。controller、model 、view 文件夹位于同一级，每一个  controller 有对应的model 、view 子文件夹。
![ ASP.NET MVC](https://img-blog.csdnimg.cn/20190727211937429.png)
结论，相对来说，由 VS 生成的 ASP.NET MVC 基本项目文件目录结构完整，不需要再手动添加文件或者代码，直接可以跑起一个 HelloWorld 网页。而由 SpringBoot 生成的 MVC 项目模板，还需要自己手动添加 controller、model 、view 文件，相对来说更为繁琐。
# Controller 差异
Spring MVC 控制器通过`@Controller` 注解和普通的类区分，没有默认的文件路径，而控制器中的方法对应前端发送的请求，`@RequestMapping(path = "hi",method = RequestMethod.GET)` **注解** 来定义控制器访问路径、对应的请求方式。未在类上添加`@RestController`、方法上添加 `@ResponseBody` 注解，则  `public String hi()` 返回的是视图名称。当返回值为 void 时（例：`public void hi()` ）默认返回与方法名称同名的视图。
```java
@Controller
public class HiController {
    @RequestMapping("hi")
    public String hi(){
        return "index";
    }
}
```
ASP.NET MVC 中所有的控制器都 **继承** 自 Controller 类，默认以 XX+Controller 为控制器名称，默认以控制器前缀（除 Controller 部分）为访问路径，访问对应的方法为「/Home/Index」控制器名称+对应的方法名称。请求方式通过注解方式声明，默认为所有请求方法都响应。在方法的返回值方面，ActionResult 是 ASP.NET MVC 所封装的抽象类，可以返回视图、Json 等多种类型。当方法放回值为 void 时不会有默认返回页面。`return View()` 会返回 VIEW 文件下 Home 文件夹（与控制器名称一致）的 Index.cshtml 视图（文件名与方法名称一致）。

```java
public class HomeController : Controller // 继承
    {
        [HttpGet] // 显式声明，默认为都响应
        public ActionResult Index()
        {
            return View();
        }
```
# Model 差异
在 Spring MVC 中，Model 层和普通的 JavaBean 一样，更多的是定义 view 所需要的数据模型

而在 ASP.NET MVC ，Model 层位于同一目录下，建立 model 文件时，会自动引入 `using System.ComponentModel.DataAnnotations;` 可以让我们使用注解配合 jquery.validate.js 在 view 层对数据进行验证或别名展示，在表现层对数据进行多重验证，确保最终获取或发送数据有效。
```java
public class SetPasswordViewModel
    {
        [Required]
        [StringLength(100, ErrorMessage = "{0} 必须至少包含 {2} 个字符。", MinimumLength = 6)] // 定义字段的取值范围以及出错提示信息
        [DataType(DataType.Password)]
        [Display(Name = "新密码")]
        public string NewPassword { get; set; }

        [DataType(DataType.Password)]
        [Display(Name = "确认新密码")]
        [Compare("NewPassword", ErrorMessage = "新密码和确认密码不匹配。")]
        public string ConfirmPassword { get; set; }
    }
```

# VIEW 差异
在 Spring MVC 中，可以在 VIEW 层使用 EL 表达式，有限的使用 Java 编写代码，通常以${} 表示，内置了 Page	PageScope、Request、RequestScope、Session、SessionScope、Application 等变量。

而  ASP.NET MVC 在视图层提供的功能很强大，Razor 标题语法可以任意的 HTML 插入 C# 语句，只需要在 C# 语句前加上「@」符号即可。同时也可以在 C# 语句块中写入 HTML 语句，在页面加载时会只能感知以区分 C# 语句和 HTML 语句。

```html
<ul>
@for (int i = 0; i < 10; i++) {
<li>@i</li>
}
</ul>
```
同时 ASP.NET MVC 也提供了一系列 HTML 辅助方法，减少了 JavaScript 的工作量，更容易和后台交互。

```java
@using (Html.BeginForm("Login", "Account", new { ReturnUrl = ViewBag.ReturnUrl }, FormMethod.Post, new { @class = "form-horizontal", role = "form" }))
            {
                @Html.AntiForgeryToken()
                <h4>使用本地帐户登录。</h4>
                <hr />
                @Html.ValidationSummary(true, "", new { @class = "text-danger" })
                <div class="form-group">
                    @Html.LabelFor(m => m.Email, new { @class = "col-md-2 control-label" })
                    <div class="col-md-10">
                        @Html.TextBoxFor(m => m.Email, new { @class = "form-control" })
                        @Html.ValidationMessageFor(m => m.Email, "", new { @class = "text-danger" })
                    </div>
                </div>
            }
```

# 总结
C# 之前的闭源和 Java 的开源，再加上发布时间的落后于 Java，导致 C# 和 Java 再编程语言的流行排行差异巨大。

还一部份原因是，C# 的社区远不如 Java 活跃，国内除了 MSDN 官方找不到什么合适的学习资源，而 MSDN 官网的资源不是很适合入门。对于入门的人来说，遇到问题不知道该如何解决，也没有合适的学习资源。

C# 对于细节方面屏蔽的更多，程序员只需要关注自己业务的需要即可，很少需要配置，而 Java 的开发相对繁琐（不使用 SpringBoot 手动配置的部分更多），需要注意更多的细节。

总而言之，言而总之，做为个人项目的开发我会选择 C# 因为可以节省更多的时间来开发自己项目，但是要系统的学习后端开发，我更推荐学习 Java，自己手动实现一些细节会让你对项目的理解更为深刻。

