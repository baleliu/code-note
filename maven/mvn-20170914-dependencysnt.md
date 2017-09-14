# maven 带dependencyManagement 和 裸dependencies区别

maven项目有有人可能不是很清楚dependencies和带dependencyManagement到底干嘛的, 我就是其中之一, 好了不知道就研究一波--><br>

### 测试1
```
-->首先创建一个空的maven项目 bale(下文成为parent项目)
    -->在项目上创建子模块 sub1(下文称为son项目)
        -->编写pom(以freemarker为例子)
```
parent项目pom文件<br>
son项目pom文件不写
```bale_pom
    <groupId>bale</groupId>
    <artifactId>bale</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>sub1</module>
    </modules>

    <dependencies>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.26-incubating</version>
        </dependency>
    </dependencies>
```
查看项目依赖<br>
parent项目依赖:<br>
```bale
D:\idea-workspace\bale>mvn dependency:list
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building bale 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:list (default-cli) @ bale ---
...省略下载一堆文件...
[INFO]
[INFO] The following files have been resolved:
[INFO]    org.freemarker:freemarker:jar:2.3.26-incubating:compile
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 28.255 s
[INFO] Finished at: 2017-09-14T19:08:20+08:00
[INFO] Final Memory: 16M/263M
[INFO] ------------------------------------------------------------------------

```
son项目依赖:<br>

```sub1_1
D:\idea-workspace\bale\sub1>mvn dependency:list
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building sub-1 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:list (default-cli) @ sub-1 ---
[INFO]
[INFO] The following files have been resolved:
[INFO]    org.freemarker:freemarker:jar:2.3.26-incubating:compile
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.298 s
[INFO] Finished at: 2017-09-14T19:09:50+08:00
[INFO] Final Memory: 12M/245M
[INFO] ------------------------------------------------------------------------

```
结果: son完美继承parent的pom文件中`dependencies`下的内容, 版本号都有了

### 测试2

parent项目pom文件修改:<br>
套上一层dependencyManagement
```
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.freemarker</groupId>
                <artifactId>freemarker</artifactId>
                <version>2.3.26-incubating</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

son项目依赖情况:<br>
```sub1_2
D:\idea-workspace\bale\sub1>mvn dependency:list
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building sub-1 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:list (default-cli) @ sub-1 ---
[INFO]
[INFO] The following files have been resolved:
[INFO]    none
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.885 s
[INFO] Finished at: 2017-09-14T19:10:47+08:00
[INFO] Final Memory: 12M/245M
[INFO] ------------------------------------------------------------------------

```
修改son项目pom文件:<br>
注意: 不加version
```sub_3

    <dependencies>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
        </dependency>
    </dependencies>

D:\idea-workspace\bale\sub1>mvn dependency:list
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building sub-1 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:list (default-cli) @ sub-1 ---
[INFO]
[INFO] The following files have been resolved:
[INFO]    org.freemarker:freemarker:jar:2.3.26-incubating:compile
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.862 s
[INFO] Finished at: 2017-09-14T19:16:56+08:00
[INFO] Final Memory: 12M/245M
[INFO] ------------------------------------------------------------------------

```

结果: 加上`dependencyManagement`之后, son项目并不会自动继承parent项目的pom文件<br>
但是, 子项目可以选择继承, son项目中, 写上`groupId`和`artifactId`,`version`省略,通过结果可以看出, son项目继承了parent项目的版本号<br>

### spring-cloud中的dependencyManagement

这是springcloud里pom的一段代码, 这个东西是干嘛的呢, 可能有人看教程里写了, 就搬过来了. 我当时就是, 研究一波-->
```cloud_pom
<!--spring cloud dependencies-->
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
[查看maven中央仓库内容点这里](http://search.maven.org)

找到`spring-cloud-dependencies`的pom文件代码<br>
有点长我把不想看的去掉了...<br>
可以看到,这里又引用一大堆`xxx-dependencies`<br>
随便找一个查eg:`spring-cloud-commons-dependencies`<br>

```spring_cloud_dependencies_pom
    ....
    <dependencyManagement>
        <dependencies>
            <!-- bom dependencies at the bottom so they can be overridden above -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-commons-dependencies</artifactId>
                <version>${spring-cloud-commons.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-netflix-dependencies</artifactId>
                <version>${spring-cloud-netflix.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            ...
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-consul-dependencies</artifactId>
                <version>${spring-cloud-consul.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            ....
    </dependencyManagement>
    ...

```
终于找到我想看的了...
```spring_cloud_commons_dependencies_pom
...
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-context</artifactId>
                <version>${project.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter</artifactId>
                <version>${project.version}</version>
            </dependency>
...
```
找到最后, 已经有了结论, spring-cloud的子项目里,很多没有版本号, 只写groupId和artifactId并不写版本号的原因就在这里.
