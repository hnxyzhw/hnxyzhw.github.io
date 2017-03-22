---
layout: post
title: Mac本地推流直播服务器(nginx、rtmp服务器+ffmpeg推流)
date: 2017-02-24 17:20:0.000000000 +09:00
tags: iOS,推流直播
---
***文章简书地址：[Mac本地推流直播服务器(nginx、rtmp服务器+ffmpeg推流)](http://www.jianshu.com/p/5bd4c8791228)***

&emsp;&emsp;在2016过去的一年里移动端直播的火爆程度，不亚于求年楼盘的疯涨。作为一个合格的开发者，也要跟进时代的潮流。下面介绍一下基于Mac os系统搭建一个本地的直播服务器。主要是基于nginx+rtmp环境搭建的。
&emsp;&emsp;nginx、rtmp是在Mac上的终端安装Homebrow后，执行命令行来安装的。(提示安装最好开vpn翻墙，不然会很慢甚至有可能会安装失败)

#### 终端安装Homebrow
##### 安装命令行:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```
##### 卸载命令行:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

相关操作截图:

![Snip20170222_1.png](http://upload-images.jianshu.io/upload_images/683658-e884339d93c6ccab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### brew安装nginx
##### clone nginx项目到本地的命令行:

```
brew tap homebrew/nginx
```
相关操作截图:

![Snip20170222_3.png](http://upload-images.jianshu.io/upload_images/683658-1ae03a58b5608840.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####下载成功后，执行安装命令行(注意执行是输入命令是nginx-full，中间不能有空格):

```
brew install nginx-full --with-rtmp-module
```
**这个过程会比较长，请耐心等待**

相关操作截图:

![Snip20170222_4.png](http://upload-images.jianshu.io/upload_images/683658-57faf6c904af6e2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![Snip20170222_5.png](http://upload-images.jianshu.io/upload_images/683658-9962d4e88f272d9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Snip20170222_6.png](http://upload-images.jianshu.io/upload_images/683658-fee20020e61f5e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 可能会出现一些文件操作权限的问题的解决办法
打开finder，复制对应的路径，找到对应的文件夹或者文件，右键显示简介。修改共享与权限一栏中，用户对该文件夹或者文件的权限为读与写。修改完后可以在执行命令:

```
brew link nginx
```
如果仍然又问题，可以执行诊断的命令,对相应的错误进行排查:

```
brew doctor
```
相关操作截图:

![Snip20170222_9.png](http://upload-images.jianshu.io/upload_images/683658-ef727b306b31ea64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按照以上的操作后nginx、rtmp已经在Mac上安装完成了，但是还需要配置nginx、rtmp。

#### 配置nginx、rtmp
终端打印出nginx的相关信息：

```
brew info nginx-full
```
可以看到到nginx的安装位置(1.10.3是安装后的nginx的版本号，可以通过nginx -v命令查看)：

`/usr/local/Cellar/nginx-full/1.10.3/bin/nginx`

nginx配置文件的位置：

`/usr/local/etc/nginx/nginx.conf`

##### nginx服务操作
服务启动：

```
nginx
或者
sudo nginx
```
其他操作：

```
nginx -s 重新加载配置|重启|停止|退出
nginx -s reload|reopen|stop|quit

nginx -V 查看版本，以及配置文件地址
nginx -v 查看版本
nginx -c filename 指定配置文件
nginx -h 帮助
nginx -t 测试配置是否有语法错误
```
nginx启动后，在浏览器里输入下面的地址：[http://localhost:8080](http://localhost:8080)或者[http://127.0.0.1:8080/](http://127.0.0.1:8080/)
会在浏览器上出现一下提示，表示安装成功：

```
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```
##### 如果说端口号8080被占用了,那么你可以执行下面操作来解决nginx服务的启动：
查看被占用8080的PID

```
lsof -i tcp:8080
```
根据输出的PID，kill掉9934

```
kill 9934
```
然后再重新执行nginx，打开之前的链接地址，查看是否启动成功。

##### 修改配置文件
```
brew info nginx-full
```
找到配置文件的路径：`/usr/local/etc/nginx/nginx.conf`
在finder中打开后，找到对应的nginx.conf文件，使用Mac自带的文本编辑打开。然后在文件的最下面也就是{}的外边，添加上rtmp的配置并保存,如下所示:

```
rtmp {
   server {
        #监听的端口号,rtmp协议的默认端口号是1935
        listen 1935;
        #直播流配置,访问路径是rtmplive
        application rtmplive {
              #开启实时
              live on;
              #为rtmp引擎设置最大连接数.默认为off
              max_connections 1024;
              #不记录数据
             record off;
        }
    }
}
```

然后重启nginx，使配置文件生效:

```
nginx -s reload 或者 sudo nginx -s reload
```
如果说你无法重新启动nginx，可能会出现下面的错误(或者说其他的错误)：

```
“/usr/local/var/run/nginx.pid”failed
```

可以通过下面的操作来解决:

```
nginx -c /usr/local/etc/nginx/nginx.conf
nginx -s reload
或者
sudo nginx -c /usr/local/etc/nginx/nginx.conf
sudo nginx -s reload
```
#### VLC支持rtmp协议的播放器的安装
在测试推流前，我们需要在Mac上安装一个支持rtmp协议的播放器，那就是鼎鼎大名的VLC。[VLC Mac版官网,需要vpn翻墙](www.videolan.org/vlc/download-macosx.html)

#### 安装ffmepg工具，进行推流
ffmpeg安装命令:

```
brew install ffmpeg
```
也可以用brew info ffmpeg指令进行查看Mac是否安装有ffmpeg:
![Snip20170224_2.png](http://upload-images.jianshu.io/upload_images/683658-90e92f744560031c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在终端上看到Options有下面的输出，可以看到默认是没有安装rtmpdump的(后边有个小红叉):

```
--with-rtmpdump
	Enable RTMP protocol
```
那么可以执行下面的命令行进行安装(经测试发现好像只安装ffmpeg,也是可以进行推流的，所以也可以省略这一步)：

```
brew reinstall ffmpeg --with-rtmpdump
或者
sudo brew reinstall ffmpeg --with-rtmpdump
```
##### ffmpeg简单操作
转换视频格式命令行:

```
ffmpeg -i CirlProgress.mov circle.mp4
```
ffmpeg推流是以flv格式传输的，所以在推流是可能要注意你的视频格式，还又就是要注意视频的码率等问题。具体问题可以自行百度google解决。
推流命令行:

 ```
ffmpeg -re -i circle.mp4 -vcodec copy -f flv rtmp://localhost:1935/rtmplive/room
 ```
 参考:
 
 ```
 ffmpeg -re -i 你的视频文件的绝对路径 -vcodec copy -f flv rtmp://localhost:1935/rtmplive/room
 ```
相关操作截图:
 
![Snip20170222_14.png](http://upload-images.jianshu.io/upload_images/683658-eb1d93fb0fb1863b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视频推流到本地的服务上后，可以打开VLC，在File->open network->选项中输入：

```
rtmp://localhost:1935/rtmplive/room
```
注意，在使用ffmpeg推流时可能会出现一下的错误提示:

```
Connection to tcp://localhost:1935 failed (Connection refused), trying next address 
```
出现这个问题，因为配置了nginx.conf后，没有需要重启的原因。
执行下面的命令:

```
sudo nginx -s reload
或者
nginx -s reload
```
**如果输入命令行后仍然有错误，那么试着关闭nginx的服务，以及8080的的相关进程，重启终端等其他方式重新操作一次。**

相关操作截图:
![Snip20170224_4.png](http://upload-images.jianshu.io/upload_images/683658-7c848e12c08f9ca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考博客：
 
[mac 安装 nginx 环境以及nginx常用命令](http://blog.csdn.net/dracotianlong/article/details/21817097)

[ffmpeg常用命令](http://blog.csdn.net/zh952016281/article/details/52683552)