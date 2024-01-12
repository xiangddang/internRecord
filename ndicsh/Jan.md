工作交接：Gitee企业账号（后端repo - server，前端repo - web，阿里云服务器账号），mysql数据库账号

近期任务：
1. code review后端springboot
2. mysql数据库了解和重构设计
3. hugeGraph的数据导入和配置
4. 产品和技术文档的归纳撰写
5. 页面数据api的设计和实现

后端框架重构任务：
1. 加强数据的安全性：WAF, Feign, (SQL injection待定) 
2. 数据库缓存：redis
3. 微服务： kubernetes，代码重构
4. 数据库重构：mysql，table重新调整

目前需要解决的问题：
1. 专利库点击专利标题、预览， 免费（专利库）和收费（专利库）的区别
2. 报告预览、下载
3. 跳转支付页面（未来）
4. 政策专区，pdf、历史存档
5. 企业用户自己上传联系人信息

Springboot后端代码结构：
- Model: 与数据相关的代码，如数据模型、数据库操作、业务逻辑
- View: 包括用户界面和页面布局的代码，本部分应不存在
- Controller: 处理用户请求和调度逻辑的代码，帮助理解应用程序如何响应用户输入，并将其传递给适当的model和view部分


## 2024-01-10
#### ndicsh-web/src/main/java/com.ndicsh.web/api
tag: 分类暂不明确
@Validated: spring框架的注解，用于启用参数验证validation功能，确保传递给控制器方法的参数经过验证
@Api(tags = "通用")：swaggger的注解，用于标记接口的分类，以便在文档中对api进行分类
@RestControlller：spring框架的注解，表示这个类是一个RESTful风格的控制器，它处理http请求并返回json格式的响应
@RequestMapping：spring框架的注解，用于将请求映射到控制器类的方法上
@Resource是java ee规范的一部分，通常用于进行基于名称的依赖注入，使用该注解时，可以指定要注入的依赖项的名称，它会根据名称来查找并注入相应的对象
@Autowired是spring框架的一部分，用于进行基于类型的依赖注入。根据依赖项的类型来查找并注入相应的对象。使用该注解时，spring会根据类型来自动查找要注入的依赖项。如果有多个匹配项，通常会抛出异常，除非使用@Qualifier注解指定要注入的bean的名称

Bean: 在软件开发中bean时一个通用的术语，通常指的是一个被管理的对象或一个可重用的组建。具体来说，在java中，bean通常指的是一个被spring容器管理的对象，它具有以下特点。可重用性，独立性，被容器管理，依赖注入，生命周期管理，配置灵活。
在spring框架中，bean是应用程序的核心组件，他们通常代表了应用程序中的各种对象，包括服务、数据访问对象（dao）、控制器、模型等等。通过将这些对象配置为spring bean，可以实现依赖注入、aop（面向切面编程）、事务管理等高级功能，从而简化应用程序的开发和维护。bean是spring中的一个关键概念，用于实现松散耦合和可维护的应用程序。

@SneakyThrows：是Lombok（一个Java库，用于减少样板代码的编写）提供的一个注解，它用于简化在方法中抛出受检异常（checked exception）的操作。通常情况下，Java要求在方法签名中声明可能会抛出受检异常的方法，或者在方法体中使用 try-catch 块来捕获这些异常，这会导致代码变得冗长。

@Data: 是一个注解，通常用于java类上，它是lombok（一个java库）提供的注解之一。@Data注解的作用是自动生成getter，setter方法，也不需要实现toString(), equals()和hashCode()方法。从而简化了java类的开发过程。使用@Data之后，lombok会在编译时自动生成他们，减少了冗余的代码，提高了代码的可读性和维护性。attention:要是用lombok注解，需要在项目中引用lombok依赖，并确保开发环境支持lombok。

过滤器和拦截器的区别：过滤器 - 取你所想取，拦截器 - 拒你所想拒

## 2024-01-11

使用阿里云进行redis的配置，配置多次出错原因为ECS和RDS MySQL里面的白名单没有配置好（目前是添加了公网和私网的IP地址）
同事已配置好WAF和Feign
接下来的任务是完成目前系统功能的梳理和文档的撰写

## 2024-01-12

redis已在测试环境中配置好，目前根据阿里云的测试用例来看，redis可能配置成功，但是缓存效果不明显，需要进一步测试。后续可能根据情况配置到生产环境中。


redis如何在springboot中使用：
1. 在pom.xml中添加redis依赖
2. 配置redis连接信息： 在application.properties或applicaiton.yml中添加redis连接信息
```shell
spring.redis.host=localhost
spring.redis.port=6379
```

3. 启用缓存： 在springboot应用的主类上添加@EnableCaching注解
```Java
@SpringBootApplication
@EnableCaching
public class YourApplication {
    public static void main(String[] args) {
        SpringApplication.run(YourApplication.class, args);
    }
}
```

4. 使用缓存：在服务类中，使用@Cacheable注解来标记哪些需要缓存的方法
```Java
@Service
public class EnterpriseService {

    @Cacheable(value = "enterprises", key = "#industry + #region + #name")
    public List<Enterprise> findEnterprises(String industry, String region, String name) {
        // 数据库查询逻辑
    }

    @Cacheable(value = "enterpriseDetails", key = "#id")
    public Enterprise findEnterpriseDetails(String id) {
        // 数据库查询逻辑
    }
}
```

5. 清除缓存：在服务类中，使用@CacheEvict注解来标记哪些需要清除缓存的方法。根据条件设置清除条件。