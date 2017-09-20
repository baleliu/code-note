# annotation 注解

更细节详情请参考最新java 官方 api
[java 官方 api](http://www.oracle.com/technetwork/java/api-141528.html)
<br>
注解从java 1.5版本以后可以正常使用annotation<br>
<br>
手动implement interface Annotation并不能定义annotation类型.<br>
<br>
需要使用 @interface<br>

### 自定义annotation语法
```
@Target(...)
@meta-annotation
...
public @interface AnnotationName{
    //eg: String value() default "";
    ...
}

```

eg:
```
@Target(ElementType.LOCAL_VARIABLE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestMessage {
    String value() default "bale";
}
```

### meta-annotation 元注解

存在于java.lang.annotation包中<br>
元注解就是"用来注解'注解'的注解"<br>
用来注解自定注解<br>
<br>
> 下文中文"注解"为动词"注解",annotation 为名词"注解",避免混淆.

* @Target
* @Retention
* @Documented
* @Inherited

#### @Target

target的作用是注解annotation的修饰的对象范围.<br>
语法:<br>
```
//param是作用范围的参数
@Target(ElementType.param)
```

ElementType参数有:
* ANNOTATION_TYPE -- 注解
* TYPE 类/接口/枚举
* PARAMETER -- 参数
* PACKAGE -- 包
* METHOD -- 方法
* LOCAL_VARIABLE -- 局部变量
* FIELD -- 域
* CONSTRUCTOR -- 构造方法

#### @Retention
retention的作用是定义annotation存在的时间段

语法:
```
@Retention(RetentionPoicy.param)
```
RetentionPoicy参数有:
* SOURCE -- 在源文件中有效
* CLASS -- 在.class文件中有效
* RUNTIME -- 在运行时有效

#### @Document
document的作用,是让该注解在生成java doc文档 时被不被忽略<br>
document为标记注解,没参数<br>

#### @Inherited
近当该annotation注解class是,inherited的作用是让其子类继承该注解.<br>
extends 才起作用,implement的实现并不继承.<br>

eg:
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Document
@Inherited
public @interface TestMessage {

    String sex() default "m";

    String name() default "bale";

    int age() default 18;
}
```

### 注意事项
* annotation内的参数只能使用public 或者 default(默认不写为public,这点有点类似interface)
* annotation内的参数类型只能是8种基本数据类型和String,Enum,Class,Annotation以及其类型数组
* annotation内的参数为value时, 使用时可以省略参数名, 因此仅有一个参数的注解直接使用value就好了.

### annotation解析器
注解并不是定义了就可以直接使用了, 因为既然有自定义注解, <br>那么注解的含义也就多种多样, java不可能自带这种"无敌"的注解解析器, <br>因此需要自己编写相应的注解解析器.<br>

#### 思路上个问题
* annotation是写在代码中的, 所以解析器需要在提前执行才能达到想要的效果.所以在普通的java代码中, 将解析器在main方法中最先执行.
* 但是有时候, 复杂的框架中, 很难做到, 第一时间执行注解解析器(目前我是这样的, 随着技术的进步, 可能会改变想法), 因此目前想到的做法是定义一个动态的注解解析器, 当使用该注解的时候调用.动态获得调用注解的类的注解信息(目前估计效率会很低).
* 我觉得以上两个想法都不是很好,等技术进步在改进

#### 代码上的问题
* 用反射获得代码上的annotation,并取得annotation的值,但是反射并不能获得局部变量的值, 所以目前解析不了局部变量注解内的值. 局部变量, 当使用的时候才会生成, 使用结束之后就会别JC在一段时间内回收.因此,反射似乎并不能得到想要的结果, 需要在源码级别获得该局部变量annotation的值.

#### 普通类型的annotation解析器
Field/Method类型解析器<br>
使用java反射<br>
代码如下:<br>
```
//annotation类
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestMessageF {
    String value() default "";
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestMessageM {
    String value() default "";
}
//-----------------------------------------------------------//
//测试使用annotation的类
public class Test {
    @TestMessageF("testF")
    public static String test="";

    @TestMessageM("testM")
    public static void test(){
        String test="";
    }
}
//-----------------------------------------------------------//
    //测试方法
    //这里只是利用java的反射机制,获取该class内的field和method上的注解内容.
    //CLASS的annotation用法一样
    //boolean flag=xxxtest.class.isAnnotationPresent(xxannotation.class);
    @Test
    public void testAnnotation() throws NoSuchMethodException, IllegalAccessException {

        //获取变量
        Field[] fields= com.bale.liquiscript.test.Test.class.getDeclaredFields();
        //遍历查找输出
        for (Field f:fields){
            if (f.isAnnotationPresent(TestMessageF.class)){
                System.out.println("field annotation--------------");
                TestMessage testMessage= f.getAnnotation(TestMessageF.class);
                System.out.println(testMessage.value());
                //简单的模仿一下spring的@autowired 并且不需要set/get方法,这里是静态变量很容易做到
                //f.setAccessible(true);//如果设置不成功可以加上
                f.set(String.class,testMessage.value());
                System.out.println("test:"com.bale.liquiscript.test.Test.test);
            }
        }

        //获取方法
        Method[] methods=com.bale.liquiscript.test.Test.class.getDeclaredMethods();
        //遍历查找输出
        for (Method m:methods){
            if (m.isAnnotationPresent(TestMessageM.class)){
                System.out.println("method annotation---------------");
                TestMessage testMessage= m.getAnnotation(TestMessageM.class);
                System.out.println(testMessage.value());
            }
        }
    }
```
测试结果:
```
field annotation--------------
testF
test:testF
method annotation---------------
testM
```

#### "相对"灵活的annotation解析器

ssm的配置文件有一条<br>
```
<context:component-scan base-package="com.bale.controllers"/>
```
这里我们不指定注解的扫描路径, 当调用的时候临时指定, 或者自动匹配.<br>
代码如下:<br>
``` 
//测试用的annotation
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestMessageM {
    String value() default "";
}

//-----------------------------------------------------------//
//annotation解析器

public static void annotationUtils(String classPath) throws Exception{
    //如果不指定className默认在当前当前调用annotation解析器的class内查找注解.
    //如果指定className 则去指定class内查找注解并进行解析.
    //参数可以是包名,类名数组等, 根据情况编写, 我这里仅写了一个.

    String className=classPath;

    if(className==null||"".equals(className)){
        //通过当前线程获取调用调用当前annotation解析器的className
        StackTraceElement[] stacks = Thread.currentThread().getStackTrace();
        className = stacks[2].getClassName();
    }
    Class clazz = Class.forName(className);
    Method[] methods= clazz.getDeclaredMethods();
    for (Method m: methods){
        for (Method m:methods){
            if (m.isAnnotationPresent(TestMessageM.class)){
                //处理方式自定义
                System.out.println("method annotation---------------");
                TestMessage testMessage= m.getAnnotation(TestMessageM.class);
                System.out.println(testMessage.value());
            }
        }
    }
}
```

### 注解的例子

研究一波spring @Results和与他相关的 annotation<br>
#### @Target
```
@Documented
@Retention(RetentionPolicy.RUNTIME)
//这里的注解好像自己注解了自己, ANNOTATION_TYPE为注解annotation自己的类型
//java.lang.annotation源码
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    //枚举类型数组
    ElementType[] value();
}
//这里可以看出,Target里的参数可以是多个.
//eg:@Target({ElementType.METHOD,ElementType.FIELD})
//这是ElementType代码,作为参考, java 1.8加入两种新的type类型
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,
    /** Field declaration (includes enum constants) */
    FIELD,
    /** Method declaration */
    METHOD,
    /** Formal parameter declaration */
    PARAMETER,
    /** Constructor declaration */
    CONSTRUCTOR,
    /** Local variable declaration */
    LOCAL_VARIABLE,
    /** Annotation type declaration */
    ANNOTATION_TYPE,
    /** Package declaration */
    PACKAGE,
    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,
    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```
#### @Results
```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Results {
    String id() default "";

    //使用注解类型的参数,而且还是注解数组, 默认值为空数组
    Result[] value() default {};
}
```
#### @Result
```
@Documented
@Retention(RetentionPolicy.RUNTIME)
//这里的target是空数组, 经过测试, 该annotation只能写在annotation内.
//@Target({})这个类型代表, annotation中的annotation
@Target({})
public @interface Result {
    //...
    JdbcType jdbcType() default JdbcType.UNDEFINED;
    //使用class类型的参数
    Class<?> javaType() default void.class;
    //使用annotation类型的参数
    One one() default @One;
    //使用annotation类型的参数
    Many many() default @Many;
}
```

### package annotation解析器
...

### LOCAL_VARIABLE annotation解析器
...