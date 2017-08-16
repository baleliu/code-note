# Spring boot 使用Redis数据库

[Redis官网](http://redis.io/)
[Redis中文社区](http://www.redis.cn/)
[Spring-data-redis Reference](http://docs.spring.io/spring-data/redis/docs/1.6.2.RELEASE/reference/html/)

> `简介`: Spring boot 对很多NoSql 数据库都有很好的支持, 提供了自动化配置支持
 
包括:
* Redis
* MongoDB
* Elasticsearch
* Solr
* Cassandra

### 操作redis 数据库

pom.xml:

```
<!--redis-->
<!--之前没有写version maven一直不认识-->
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-redis</artifactId>
        <version>1.4.7.RELEASE</version>
</dependency>
```
application.properties
```
# REDIS (RedisProperties)
# Redis数据库索引（默认为0）
spring.redis.database=0
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=0
```
dto:
```
//需要继承序列化接口
public class User implements Serializable{

    private static final long serialVersionUID = 2718477049627961545L;
    private Long id;
    private String name;
    private Integer age;

    //getter and setter ...
}
```
utils:
RedisSerializerConverter.java<br>

`注意:`<br>
`Spring boot` 不支持RedisTemplate<K,V> 直接使用<br>

``` RedisSerializerConverter
// redis 序列换转换类
public class RedisSerializerConverter implements RedisSerializer<Object>{

    //创建转化器
    private Converter<Object,byte[]> converter= new SerializingConverter();
    private Converter<byte[],Object> deConverter = new DeserializingConverter();

    public static final byte[] EMPTY_ARRAY = new byte[0];
    public static final Object EMPTY_OBJECT= null;

    @Override
    public byte[] serialize(Object o) throws SerializationException {

        if (o==null){
            return EMPTY_ARRAY;
        }

        try{
            return converter.convert(o);
        }catch (Exception e){
            return EMPTY_ARRAY;
        }

    }

    @Override
    public Object deserialize(byte[] bytes) throws SerializationException {

        if (isEmpty(bytes)){
            return EMPTY_OBJECT;
        }

        try{
            return deConverter.convert(bytes);
        }catch (Exception e){
            return EMPTY_OBJECT;
        }
    }

    //判断转换的是否为空
    public boolean isEmpty(byte[] array){
        return array==null||array.length==0;
    }
}
```

utils:
RedisObject.java

``` RedisObject
//配置redis
@Configuration
public class RedisObject {

    @Bean       //这个必须有
    public JedisConnectionFactory jedisConnectionFactory(){
        return new JedisConnectionFactory();
    }

    @Bean
    public RedisTemplate<String ,User> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String,User> template= new RedisTemplate<String,User>();
        template.setConnectionFactory(jedisConnectionFactory());
        template.setKeySerializer(new StringRedisSerializer());
        //设置刚才写的redis序列化转换器
        template.setValueSerializer(new RedisSerializerConverter());
        return template;
    }
}
```

junit 4 test:
```
    @Autowired
    // StringRedisTemplate == RedisTemplate<String,String>
    private StringRedisTemplate stringRedisTemplate;

    @Autowired
    private RedisTemplate<String, User> redisTemplate;

    @Test
    public void contextLoads() {

        /*stringRedisTemplate.opsForValue().set("aaa", "111");
        Assert.assertEquals("111", stringRedisTemplate.opsForValue().get("aaa"));
        System.out.println(stringRedisTemplate.opsForValue().get("aaa"));*/
        User user = new User("bale", 40);

        redisTemplate.opsForValue().set(user.getName(), user);

        Assert.assertEquals(40, redisTemplate.opsForValue().get("bale").getAge().longValue());

        System.out.println(redisTemplate.opsForValue().get("bale"));
    }
```