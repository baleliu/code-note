# Spring boot 使用mybatis

pom.xml
```
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

        <!--spring boot web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--spring cloud eureka-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.43</version>
        </dependency>

        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.0</version>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Dalston.SR2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

application.properties:
```
server.port=8089
spring.application.name=mybatis-server

# mybatis 配置
spring.datasource.url = jdbc:mysql://localhost:3306/test
spring.datasource.username = root
spring.datasource.password = 123456
spring.datasource.driver-class-name = com.mysql.jdbc.Driver

# 注册到服务中心eureka
eureka.client.service-url.defaultZone=http://localhost:1001/eureka/
```

application.java:
```
// 默认生成
@SpringBootApplication
// 使用DiscoveryClient
@EnableDiscoveryClient
public class MybatisServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisServerApplication.class, args);
    }
}

```

dto/domain/pojos/bean:
```
public class User {

    private Long id;            //id

    private String userName;    //用户名

    private String password;    //密码

    private String comments;    //备注

    // getter and setter ...
}
```

mapper/dao:
```
@Mapper
public interface UserMapper {
    /* 测试用的注解方式访问数据库
    @Select(value = "select * from user")
    @Results({
            @Result(id =true, column = "id" ,property="id"),
            @Result(column = "user_name",property = "userName")
    })*/
    List<User> selectAll();

    User selectById(Long id);

    /**
     * 数据库添加了unique索引, 所以userName查询是一行结果
     * @param user
     * @return
     */
    User selectByNamePwd(User user);

    int insert(User user);

    int delete(int id);

    int update(User user);

}
```

mapper.xml:
```

<!--使用注解方式请跳过-->


<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.hand.mapper.UserMapper">
    <resultMap id="BaseResultMap" type="com.hand.dto.User">
        <id column="id" property="id"/>
        <result column="user_name" property="userName"/>
        <result column="password" property="password"/>
        <result column="comments" property="comments"/>

    </resultMap>

    <select id="selectAll" resultMap="BaseResultMap">
        SELECT * FROM user;
    </select>

    <select id="selectById" resultMap="BaseResultMap">
        SELECT * FROM user
        WHERE id=#{id}
    </select>

    <select id="selectByNamePwd" resultMap="BaseResultMap">
        SELECT * FROM user
        WHERE user_name=#{userName}
        AND password=#{password}
    </select>
    <insert id="insert">
        INSERT INTO user (user_name,password,comments)
        VALUES (#{userName},#{password},#{comments})
    </insert>

    <delete id="delete">
        DELETE FROM user
        WHERE id=#{id}
    </delete>

    <update id="update">
        UPDATE USER
        SET
        <if test="userName!=null">
            user_name=#{userName}
        </if>
        <if test="password!=null">
            password=#{password}
        </if>
        <if test="comments">
            comments=#{comments}
        </if>
        WHERE id=#{id}
    </update>
```

service:
```
@Service
public class UserServiceImpl implements IUserService{

    @Resource
    UserMapper userMapper;

    @Override
    public List<User> queryAllUsers() {

        return userMapper.selectAll();

    }

    @Override
    public User queryById(Long id) {


        return userMapper.selectById(id);
    }

    //... ...
}
```

controller:
```
@RestController
// @RestController == @Controller + @ResponseBody

@RequestMapping(value = "/user")
public class UserController {


    @Autowired
    IUserService userService;

    @RequestMapping(value = "/queryall")
    public List<User> quertAllUsers(){


        return userService.queryAllUsers();
    }

    @RequestMapping(value = "/query")
    public User quertById(Long id){

        return userService.queryById(id);
    }

}
```

junit 4 test:
```
@RunWith(SpringRunner.class)
@SpringBootTest
//rollback 防止污染数据库
@Rollback
public class MybatisServerApplicationTests {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void contextLoads() {

        User user=new User();
        user.setComments("cessdfsdfsdfsdf");
        user.setPassword("123123");
        user.setUserName("testname-2");
        int n=userMapper.insert(user);

        List<User> list=userMapper.selectAll();

        for (User u:list){
            System.out.println(u);
        }


    }

}
```

`总结`:<br>
mybatis整合到这里结束, 和`普通`用起来没什么区别, 但是方便了许多, 不许导各种jar包和复杂的配置文件<br>