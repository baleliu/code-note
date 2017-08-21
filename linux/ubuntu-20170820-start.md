# Ubuntu 输入法 & 源

linux 版本: `ubuntu 16.04 lts`  -- 源对应该版本

[Ubuntu 官网](http://www.ubuntu.org.cn/global)

###  命令初识
> sudo是superuser do的简写，sudo是linux系统管理指令，是允许系统管理员让普通用户执行一些或者全部的root命令的一个工具，如halt，reboot，su等等。这样不仅减少了root用户的登陆 和管理时间，同样也提高了安全性。<br>
apt是个很完整和先进的软件包管理程式，使用他能够让您，又简单，又准确的找到您要的的软件包， 并且安装或卸载都很简洁。他还能够让您的任何软件都更新到最新状态，而且也能够用来对ubuntu进行升级。<br>
"apt是需要用命令来操作的软件，但是现在也出现了很多有图像的软件，比如Synaptic, Kynaptic 和 Adept。"    -- 百度知道

### 设置中文:
```
//具体的有点忘记, 大概流程
左边setting-->
	language-->
		安装chinese-->
			汉语(中国)拖到最上边-->
				应用-->ok
```


### 安装输入法
`搜狗输入法 for linux`
```
sudo apt-get install gdebi
cd Downloads
sudo gdebi xxxx.deb
```

### 查看系统更新和源:
```
左边search-->
	输入update-manager-->
		点击图标-->ok
```

### 修改源:
```
sudo gedit /etc/apt/source.list
```
阿里源:
```
# deb cdrom:[Ubuntu 16.04 LTS _Xenial Xerus_ - Release amd64 (20160420.1)]/ xenial main restricted
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```

执行源更新:

```
sudo apt-get update
```
