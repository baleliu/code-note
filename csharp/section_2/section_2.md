# 第二章 C# 核心

### 2.1 C# 环境变量配置
添加环境变量pah
1. 查找对应文件夹在不在C:\Windows\Microsoft.NET\Framework\v4.0.30319
2. 将路径复制到path 里.

### 2.2.1 各种语言的开始! HelloWorld
边界 Frist.cs文件
```first
using System;

namespace Work{
    public class MyFirstClass{
        static void Main(){
            Console.WriteLine("hello world");
            Console.ReadLine();
            return;
        }
    }
}
```
编译文件<br>
```
文件路径下.
执行:csc First.cs
结果:生成 First.exe
```
运行文件<br>
结果:控制台输出 helloworld<br>

> `注意1`:c#里并没有输入和输出的关键字(类似java), 完全以.net类(System命名空间包含了.net最常用的类)
> `注意2`:所有c#代码如同java一样, 要写在`类`(`class`)中, 这点与c/c++很不同.

### 2.2.2 方法的定义的语法

```method
[modifiers] return_type methodName([parameters...]){
    //...method content...
}
```
### 2.3.1 变量的定义
```var
data_type identifier;
//eg:
int i;
double d;
int j=10;
int a=1,b=2,c=3;
bool flag=true;
//...
```
### 2.3.2 变量的初始化
```var_init
//1
public static int Main(){
    int a;
    //这里会报错, 原因:方法中的变量没初始化就使用!
    //类中的变量,如果没有初始化, 则为0
    Console.WriteLine(a);
    return 0;
}
//--------------------超级华丽的分割线---------------------//
//2
public static void Main(){

    //引用型变量初始化, 可以使用 new 关键字
    Something something=new Something();

    return;
}
```
### 2.3.3 类型推断关键字:var  
```
//var
int i=0;
//虽然j并没有声明是int型变量, 但是在j的作用域内,通过j初始化值 0 的判断, 就会认定j为一个int
var j=0;
//--------------------超级华丽的分割线---------------------//
//type
static void Main(String args[]){
    var name="bale";
    var num=10;

    Type nameType= name.GetType();
    Type numType=num.GetType();
    Console.WriteLine("name type is "+nameType.ToString());
    Console.WriteLine("num type is "+numType.ToString());
}

//输出结果
//name type is System.String
//num type is System.Int32
//--------------------超级华丽的分割线---------------------//
//c# 中一切皆对象, 也就是说这里不是输出int, 而是int32, 如果用java做对比, 那就是没有基本类型int,只有 包装类 Integer
```
* 变量必须初始化
* 初始化器不能为空
* 初始化器必须放在表达式中
* 不能把初始化器设置为一个对象, 除非初始化器中创建了一个对象
* 初来乍到对这些名词不熟悉,但是猜的八九不离十了, 以后再说

### 2.3.4 变量的作用域
```var_field_1
int a=1;
int a=2;
//相同作用域,中声明的变量不能重复

```
```var_field_2
//代码1
for(int i=0;i<10;i++){//...}
for(int i=10;i>0;i--){//...}
//这段代码可以通过编译, 每个for中的i的作用域就是for循环的{...}中
//--------------------超级华丽的分割线---------------------//

//代码2
using System;

namespace Work{
    class Test01{
        public static void Main(String args[]){//大括号main
            String s="sdf";
            for(int i=0;i<10;i++){//大括号for
                //这里会报错
                String s="bale";
                Console.WriteLine(s);
            }
        }
    }
}

//上面的String s 的作用域为 大括号main
//下面的String s 的作用域为 大括号for

//作用域为包含关系, 所以不能重复命名
```

```var_field_3

using System;
namespace Work{
    class Test02{
        static int x=1;
        public static void Main(String[] args){
            int x=2;

            //这里会输出2
            //乍一看 两个x之间的作用域有冲突, 但是, 这里有个特殊情况
            //上面的x是静态的类变量,c#中也叫"字段"
            //下面方法中的x为方法中的变量,叫做局部变量
            //这两个变量并不是一个"区"的, 所以可以同时存在
            //这里有一个"就近原则",所以输出2并不是1
            Console.WriteLine(x);

            //这里会输出1
            //className.fieldName 是使用类变量(字段)的方式
            Console.WriteLine(Test02.x);

            //如果上面的x没有static关键字, 那么, 这种变量叫做"实例变量"
            //也就是说, 想使用这种变量, 必须new 出一个实例来, 通过实例才能使用,
            //instanceName.fieldName 是使用实例变量的方式

            //这里来说一下"类变量"和"实例变量"的区别
            //类变量:是在某个类初始化的是就被初始化的变量,并且类变量,被所有类的实例所"公用","公共设施要爱惜,坏了别人就不能好好用了", 通过类名和实例名都可以调用
            //实例变量:那就是new 出来一个实例才能用的变量, 是这个实例的"私人物品","每人一个,人人都有".

            return;
        }
    }
}
```

### 2.3.5 常量const
常量顾名思义, 作为常数的量

```const
const int a=100;
//这就是一个常量, 用const关键字修饰
//和java中 static final int a=10; 一个意思
```

常量的特点:
* 声明常量必须初始化,切之后不能修改
* 常量的数值必须能在编译的时候就能用, 因此, 不能用一个变量给常量初始化.如果要这么做,请使用只读字段(第三章)
* 常量总是静态的(可以作为第一条的依据),但是在声明的时候不可以用static修饰

常量的好处:
* "`易阅读`":假如, 程序中用到了3.1415926535897932的时候, 定义一个常量PI=..., 后面的代码都用PI, 代码好看又容易理解
* "`易修改`":并且, 如果PI写错了只要,修改代码最上面的常量定义, 就可以修改所有代码中的PI的值, 如果, 每个地方都写3.1415926...然后错了好几处, 修改起来算是一个很酸爽的活..
* "`禁修改`":如果某个数字不能被修改, 那就定义成常量, 代码某处如果修改这个量, 会报错, 不至于你自己都不知道.

### 2.4 8种预定义数据类型

8种预定义整型

名称|CTS类型|说明|范围
-|-|-|-
sbyte|System.SByte|有符号字节型|-128~127
short|System.Int16|短整型|-3w+~3w+
int|System.Int32|整型|-2^31~2^31-1
long|System.Int64|长整型|-2^63~2^63-1
byte|System.Byte|无符号字节型|0~255
ushort|System.UIn16|无符号短整型|0~6w+
uint|System.UInt32|无符号整型|0~2^32-1
ulong|System.UInt64|无符号长整型|0~2^64-1

```
int a=1;
uint aa=2u;
long x=13212L;
ulong xx=23123UL;
//使用大写字母显示声明类型(小写不好看,可用)

```

2种浮点数类型
* float--System.Single
* double--System.Double
* decimal--System.Decimal(高精度浮点数)
```
float f= 1.2F;
decimal d=1.2M;
```

布尔类型
* bool--System.Boolean
```
bool bl1=true;
bool bl2=false;
//这里不能用0代表false,1代表true. 不能!
```

字符类型
* char--System.Char
```
//c# 和 java都是使用 Unicode编码
char c='a';
```

上面的都叫做值类型<br>
下面介绍引用类型<br>

* string--System.String
* object--System.Object

```string
//字符串类型
string str1="13123";
string str2="asdasd";
//字符串拼接
string str3=str1+str2;
//注意转义字符的使用
string path="c:\\a\\b\\c.txt";
//等价于下面这句话
string path=@"c:\a\b\c.txt";
//@代表原样,也就是说, 字符串中有换行, 也会原样保留.
//eg:
string xx="sdf
fgh
"
//输出xx 中间就会换行
```

```object
//一切类的基类==任何类的父类.
//很多基础的类方法都是在object实现的Equals(),ToString()...
```