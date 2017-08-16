# Spring boot 使用jdbcTemplate访问数据库

[JdbcTemplate API](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html)

### 嵌入式数据库支持

> 嵌入式数据库通常用于开发和测试环境，不推荐用于生产环境。Spring Boot提供自动配置的嵌入式数据库有H2、HSQL、Derby，你不需要提供任何连接配置就能使用。

eg:
```
<dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>hsqldb</artifactId>
    <scope>runtime</scope>
</dependency
```

### mysql 使用jdbcemplate

pom.xml
```
<!--jdbc-->
<dependency>
    
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<!--mysql-->
<dependency>
<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.43</version>
</dependency>
```

application.properties:
```
# eg
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

### 使用 jdbcTemplate

Service层
```
// 如果有更多需求请参考 文章开头API文档
@Service
public UserServiceImpl implements IUserService{

    @Autowired
    private JdbcTemplate JdbcTemplate;

    @Override
    public int insert(User user){
        int  n=jdbcTemplate.update("insert into User(user_name,password) values (?,?)",user.getUserName(),user.getPassword);
        return n;
    }

    @Override
    public int delate(Long id){
        int n= jdbcTemplate.update("delete from User where id=?",id);
        return n;
    }

    //... ...
}

```
