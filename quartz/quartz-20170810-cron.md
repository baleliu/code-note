# CRON表达式

### 作用范围和意义
> 主要用于 quartz  quartz是什么? <br>
> 目前觉得 有点类似正则表达式, 用一串符号表达一种 特定`规则`

### cron 表达式
```
//cron 表达式主要分为 6 - 7 个值域, 每个域代表一个意思, 用空格间隔
//---6
seconds minutes hours dayOfMonth month dayOfWeek
//---7
seconds minutes hours dayOfMonth month dayOfWeek year
```
#### 每个域出现的范围
> `seconds` ',' '-' '*' '/' (0-59的整数)<br>
> `minutes` == `seconds` (0-59)<br>
> `hours` == `seconds` (0-23)<br>
> `dayOfMonth` == `seconds` + 'L' 'W' 'C' '?' (0-31实际情况而定)<br>
> `month` == `seconds` (1-12)(JUN-DEC)<br>
> `dayOfWeek` == `dayOfMonth` - 'W' + '#' (1-7)(SUN-SAT)<br>
> `year` == `seconds` (1970-2099) <br>

#### 符号代表的意义
> `*` 同正则表达式, 任意匹配, seconds 使用 '*' 代表的意思 : 每秒钟<br>
> `,` enum 枚举, 1,2,3 <br>
> `?` 只存在 `day-`中使用, 适应匹配, 如果dayOfMonthdya 为 20 , 则 dayOfWeek 写 ? 代表, 20日那天的星期 <br>
> `-` 范围, 1-10 <br>
> `/` 迭代触发  left/right, left: 触发起始时间, right:迭代触发的步长 <br>
> `L` last 只存在 `day-`中使用, 5L 代表最后的星期四(星期日为第一天) <br>
> `W` weekday 只存在 `dayOfMonth` 5W 判断 5日是否为工作日, 否则,取左右最近的一天执行, 即 ,周六取前一天, 周日取后一天 <br>
> `LW` last weekday 某月的最后一个星期五
> `#` 只存在 `dayOfMonth` left#right left: 星期几, right: 第几个

#### 举例
```
//每个月的4号凌晨3点2分1秒
1 2 3 4 * ? *
```

#### 注意事项
```
//1 注意：在使用“L”参数时，不要指定列表或范围，因为这会导致问题。
```

### 总结
```
2017-8-10
quartz 用来执行job (定期循环执行某事件)
也就是类似 定时器+触发器
cron表达式 就是定时器的规则表达式

推荐一首歌曲:纵贯线-公路
```

### 附录

#### 12个月份英文缩写
* 一月 Jan. January 
* 二月 Feb. February    
* 三月 Mar. March   
* 四月 Apr. April
* 五月 May. May 
* 六月 June. June   
* 七月 July. July   
* 八月 Aug. Aguest
* 九月 Sept. September  
* 十月 Oct. October 
* 十一月 Nov. November   
* 十二月 Dec. December

#### 星期英文缩写
* 星期一 Mon. Monday
* 星期二 Tues. Tuesday
* 星期三 wed. Wednesday
* 星期四 Thur. Thurday 
* 星期五 Fri. Friday
* 星期六 Sat. Saturday 
* 星期天 Sun. Sunday
