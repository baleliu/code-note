# Spring boot demo

### spring boot logo
```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.6.RELEASE)
```

### 首先创建新的 spring boot demo
```
Idea
--> New Project
    --> Spring Initializr   //error, https://start.spring.io --> http://start.spring.io
        --> Next    //修改你需要的各种参数 如包的结构...
        --> Next
        --> Finish
```

### 工程结构
```
.
├──spring boot demo
│  └──src
│     ├──main
│     │  └──java
│     │     ├──com
│     │     │  ├──hand(包名随意)
│     │     │  │  ├──dto
│     │     │  │  ├──controller
│     │     │  │  └──...(各种)
│     │     │  └──DemoApplication(启动的主类)
│     │     └──resources
│     │        ├──application.properties(配置文件)
│     │        ├──bootstrap.properties(优先application启动的配置文件)
│     │        └──com....mapper.xml(结构同java内)
│     └──test(测试模块)
│        └──...DemoApplicationTest(包结构对应DemoApplication)
└──pom.xml
.
```

### pom.xml
```
<dependency>
    <!--web-->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
### HelloWorldController
```
@RestController
/*
spring4 以后添加 @RestController注解 返回body
等价于@Controller+@ResponseBody
@Controller 返回String 自动匹配url
*/
public class HelloWorldController {
    @RequestMapping("/helloworld")
    public String helloworld() {
        return "HelloWorld";
    }
}
```

### Junit4 Test
```
待补充...
```

### spring boot命令操作

* 在项目根目录下执行`mvn package`将会生成一个可执行的jar包<br>
该命令执行后还会保留一个`XXX.jar.original`的jar包, 包含了项目中单独的部分<br>
生成这个可执行的jar包后，在命令行执行`java -jar xxxx.jar`即可启动项目<br>

* `mvn spring-boot:run` 可以直接使用`tomcat（默认）`启动项目