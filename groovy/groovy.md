# Apache Groovy语言

[Groovy官网](http://www.groovy-lang.org/)

### 概念

定义: Groovy是一种基于JVM（Java虚拟机）的敏捷开发语言，它结合了Python、Ruby和Smalltalk的许多强大的特性，Groovy 代码能够与 Java 代码很好地结合，也能用于扩展现有代码。由于其运行在 JVM 上的特性，Groovy 可以使用其他 Java 语言编写的库。-- 百度百科<br>

个人理解: groovy和java一样, 都是跑在JVM上的语言, 并且 groovy包含Python,java等语言一大堆强大特性,并且可以使用java代码,也就是说,groovy就是"java升级版", 并且是一门动态语言. 并且所有一切皆对象.

### 环境条件
Groovy 2.4 requires Java 6+ with full support up to Java 8. There are currently some known issues for some aspects when using Java 9 snapshots. The groovy-nio module requires Java 7+. Using Groovy’s invokeDynamic features require Java 7+ but we recommend Java 8. -- 官网2017-09-09<br>
groovy 2.4 要求java6+,并且充分支持java8. 
当前问题: 当使用java 9 抢先版的时候,`groovy-nio`要求java 7+,使用groovy 动态调用功能要求java 7+, 但是我们推荐使用java 8<br>

### maven pom.xml
``` pom
<!--Just the core of groovy without the modules (see below).-->
<!--groovy核心框架-->
<groupId>org.codehaus.groovy</groupId>
<artifactId>groovy</artifactId>
<version>2.4.12</version>

<!--"$module" stands for the different optional groovy modules "ant", "bsf", "console", "docgenerator", "groovydoc", "groovysh", "jmx", "json", "jsr223", "servlet", "sql", "swing", "test", "testng" and "xml". Example: <artifactId>groovy-sql</artifactId>-->
<!--groovy功能模块-->
<groupId>org.codehaus.groovy</groupId> 
<artifactId>groovy-$module</artifactId> 
<version>2.4.12</version>

<!--The core plus all the modules. Optional dependencies are marked as optional. You may need to include some of the optional dependencies to use some features of Groovy, e.g. AntBuilder, GroovyMBeans, etc.-->
<!--groovy全部功能模块-->
<groupId>org.codehaus.groovy</groupId> 
<artifactId>groovy-all</artifactId> 
<version>2.4.12</version>

<!--To use the InvokeDynamic version of the jars just append ':indy' for Gradle or <classifier>indy</classifier> for Maven.-->
<!--如果使用动态调版本需要导入 indy 的jar包,或者maven项目在pom.xml中添加 classifier属性-->
```

### 区别于Java

1> Default imports 自动导入的包.<br>

* java.io.*
* java.lang.*
* java.math.BigDecimal
* java.math.BigInteger
* java.net.*
* java.util.*
* groovy.lang.*
* groovy.util.*

2> Multi-methods 重载方法的调用<br>

java 根据参数类型调用方法
groovy 根据运行时参数类型调用方法<br>

``` code
//这段是公用代码
//方法1
int method(String arg) {
    return 1;
}
//方法2
int method(Object arg) {
    return 2;
}
Object o = "asdf";
//结果位置
int result = method(o);
```

* java结果为2 , 因为o被定义为Object类型, 所以调用方法2
* groovy结果为1, 因为当groovy调用method时, 参数为"asdf"的字符串, 所以根据字符串匹配, 调用方法1

3> 数组定义方法<br>
```
//java
int[] arr={1,2};
//groovy
int[] arr=[1,2];
```

4> 修饰符<br>
groovy不需要加入public修饰符, 默认自带public
``` modify
//默认public作用域
class A{
    String a;
}

class B{
    //package作用域
    @PackageScope String b;
}
```