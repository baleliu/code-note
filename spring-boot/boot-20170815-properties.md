# Spring boot application.properties 属性配置文件

### 配置变量
```
# 这是注释的写法
# 自定义比配置变量
# 文件: application.properties
com.bale.name=baleee
# 引用配置比变量
com.bale.comments=${com.bale.name}的comments
# 禁用命令行修改参数
SpringApplication.setAddCommandLineProperties(false)
```

### class内引用配置变量
```
@Component  //spring 用于注解意义不明确的加载类
public class BlogProperties {

    //使用value注解后去配置变量
    @Value("${com.bale.name}")
    private String name;
    //...
}
```

### 配置文件随机数
```
# 随机字符串
com.bale.value=${random.value}
# 随机int
com.bale.number=${random.int}
# 随机long
com.bale.bignumber=${random.long}
# 10以内的随机数
com.bale.test1=${random.int(10)}
# 10-20的随机数
com.bale.test2=${random.int[10,20]}
```

### 多环境配置
>我们在开发`Spring Boot`应用时，通常同一套程序会被应用和安装到几个不同的环境，比如：开发、测试、生产等。其中每个环境的数据库地址、服务器端口等等配置都会不同，如果在为不同环境打包时都要频繁修改配置文件的话，那必将是个非常繁琐且容易发生错误的事。-- 程序员DD<br>

>在命令行运行时，连续的两个减号--就是对`application.properties`中的属性值进行赋值的标识。所以，`java -jar xxx.jar --server.port=8888`命令，等价于我们在`application.properties`中添加属性`server.port=8888`，该设置在样例工程中可见，读者可通过删除该值或使用命令行来设置该值来验证

spring 配置文件 命名规则 `application-{profile}.properties`
* `application-dev.properties` 开发环境
* `application-test.properties` 测试环境
* `application-prod.properties` 生产环境

1.个具体的配置文件会被加载，需要在`application.properties`文件中通过`spring.profiles.active`属性来设置，其值对应`{profile}`值。
```
<!-- eg -->
<!-- file: application.properties -->
spring.profiles.active=test
```
2.或者通过命令
```
java -jar xxx.jar --spring.profiles.active=test
```
> `注意:` 不同的配置文件可以设置不同的端口号


### 环境变量各种配置参数

[点击这里](ms-20170815-application-properties.md)
