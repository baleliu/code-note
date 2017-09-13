# Apache Groovy 语法

### 备注
4种<br>
``` comments
//单行备注
/*
这里是
多行备注
*/
/**
* 这里是Doc备注
* @author : bale
*/
---------------------------华丽分割线---------------------------
#!/usr/bin/env groovy
println "Hello from the shebang line"
//# 必须是第一个字符
```

### 关键字
def,assert....

### 标识符
范围<br>
和其他语言差不多, 没什么太多说的.<br>
所有关键字都可以当作标识符被点出来eg:foo.as
* 'a' to 'z' (lowercase ascii letter)
* 'A' to 'Z' (uppercase ascii letter)
* '\u00C0' to '\u00D6'
* '\u00D8' to '\u00F6'
* '\u00F8' to '\u00FF'
* '\u0100' to '\uFFFE'

### 引用类型

``` qoutedIdentify
def map = [:]

map."an identifier with a space and double quotes" = "ALLOWED"
map.'with-dash-signs-and-single-quotes' = "ALLOWED"

assert map."an identifier with a space and double quotes" == "ALLOWED"
assert map.'with-dash-signs-and-single-quotes' == "ALLOWED"
```

### String 字符串
``` string
'' 单引号扩起来为 java的String
"" 双引号扩起来为 groovy的Gstring
```

### for循环
``` for
//1
for (i=0;i<10;i+++){};
//2
for (int i=0;i<10;i++){};
//3(0..10包含10,0..<10不包含10)
for (i in 0..10){};

```