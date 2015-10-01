---
layout: post
title: "Scientific Linux 安装 texlive"
date: 2014-12-26 20:17:42 +0800
comments: true
tags: [Linux,Software,texlive]
---

&#160; &#160; &#160; &#160;Scientific Linux 6 系统自带的 texlive2007 过于简化，缺少很多内容，特别是画图还有幻灯片制作方面。所以先卸载系统安装的 texlive2007 版本。
~~~shell
$ su
# yum remove texlive*
~~~
<!--more-->
## 下载镜像
下载地址：http://mirrors.ustc.edu.cn/CTAN/systems/texlive/Images/
&#160; &#160; &#160; &#160;以上下载地址是中科大的镜像源，使用该地址能保证在天朝的下载速度。不同年份版本是通过texlive后面的年份来标记的，比如：texlive2015.iso ，将其下载至本地。<font color=red>以下所有的2015都换成你相应下载的文件。</font>

## 安装
&#160; &#160; &#160; &#160;我习惯将下载文件解压到 /opt 文件夹，所以先建好文件夹 /opt/texlive2015。

具体解压、安装步骤（将终端路径指向下载文件）：
~~~shell
$ su
# mkdir /opt/texlive2015
# mount -o loop texlive2015.iso  /opt/texlive2015
# cd /opt/texlive
# ./install-tl
~~~
出现选项后，输入 **I** 直接安装（也可以更改选项）。等待安装完成。  
安装结束后解除镜像文件的挂载：
~~~shell
# cd /
# umount /opt/texlive2015
~~~

## 环境变量设置
在~/.bashrc中加入如下语句：
~~~
# TeX Live 2015
export MANPATH=${MANPATH}:/usr/local/texlive/2015/texmf-dist/doc/man
export INFOPATH=${INFOPATH}:/usr/local/texlive/2015/texmf-dist/doc/info
export PATH=${PATH}:/usr/local/texlive/2015/bin/x86_64-linux
~~~

----
&#160; &#160; &#160; &#160;


<br />
----
&#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160;**Update: 2. Last-Modified: 2015-07-19 20:46**
