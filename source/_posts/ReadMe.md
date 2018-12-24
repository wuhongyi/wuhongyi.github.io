title: Hello ! Read Me.
date: 3000-01-01 00:00:00 +0800
comments: true
tags: []
---
&#160; &#160; &#160; &#160;Welcome to Hongyi Wu(吴鸿毅) blog! If you have any problems when using my blog, you can ask me for email: wuhongyi@qq.com / wuhongyi@pku.edu.cn.


&#160; &#160; &#160; &#160;1987 年 9 月，CANET（中国学术网）在北京计算机应用技术研究所内正式建成中国第一个国际互联网电子邮件节点，并于 9 月 14 日发出了中国第一封电子邮件：“Across the Great Wall we can reach every corner in the world.(越过长城，走向世界)”，揭开了中国人使用互联网的序幕。这封电子邮件是通过意大利公用分组网 ITAPAC 设在北京的 PAD 机，经由意大利 ITAPAC 和德国 DATEX-P 分组网，实现了和德国卡尔斯鲁厄大学的连接，通信速率最初为 300bps。现在我们时常能感受到了“越过长城走向世界"里面的那个长城的伟大。中国的前辈们很有先见之明，在 N 年前已经知道了后辈们将会遭遇什么，并以神的口吻告诉我们“越过长城，走向世界”。

## 常用配置

[Hongyi Wu Github](https://github.com/wuhongyi)
*程序包源码暂不公开* ([Code Project](https://github.com/wuhongyi/CodeProject))

**下载我的模板及配置**
[系统安装之后自动配置软件脚本](https://github.com/wuhongyi/BasicConfiguration)
[yum SL6 SL7](https://github.com/wuhongyi/ScientificLinuxYumSet)
[emacs set linux/windows](https://github.com/wuhongyi/EmacsSet)
[linux latex fonts](https://github.com/wuhongyi/fonts) 
[cmake template](https://github.com/wuhongyi/cmakeTemplate)
[latex template](https://github.com/wuhongyi/LatexTemplate)
[ROOT GUI template](https://github.com/wuhongyi/ROOTGUITemplate)



**我的笔记**

[Code Project And Tool](http://wuhongyi.cn/CodeProject)
[DAQ Note](http://wuhongyi.cn/DAQNote)
[Hardware Note](http://wuhongyi.cn/HardwareNote)
[ROOT Note](http://wuhongyi.cn/ROOTNote)
[Geant4 Note](http://wuhongyi.cn/Geant4Note/)
[Python Note](http://wuhongyi.cn/PythonNote)
[FPGA Note](http://wuhongyi.cn/FPGANote/)
[Neutron Simulation Note](http://wuhongyi.cn/NeutronSimulationNote/)
[CGI Note](http://wuhongyi.cn/CGINote/)
[TCL Note](http://wuhongyi.cn/TCLNote/)
[HTML Note](http://wuhongyi.cn/HTMLNote)
[Qt Note(未更新上传)](http://wuhongyi.cn/QtNote/)
[C++ Note(未更新上传)](http://wuhongyi.cn/CPlusPlusNote/)
[Analysis 不公开](https://github.com/wuhongyi/AnalysisNote/)


**PKU DAQ源码/固件**

[XIA Pixie16 DAQ](https://github.com/wuhongyi/PKUXIADAQ)
[XIA Pixie Net](https://github.com/wuhongyi/PKUXIAPixieNet)
[CAEN Desktop Digitizer](https://github.com/wuhongyi/PKU_CAENDesktopDigitizer)
[CAEN V1724 Digitizer](https://github.com/wuhongyi/CAENV1724Digitizer)
[CAEN VME](https://github.com/wuhongyi/VMEDAQ)
[CAEN DT5495](http://wuhongyi.cn/CAEN_DT5495/)
[XIA MZTIO](https://github.com/wuhongyi/MZTIO)

*HEU CAMAC DAQ 暂不公开* ([HEU CAMAC](https://github.com/wuhongyi/HEUCAMAC))
*暂不公开* ([PCIE](https://github.com/wuhongyi/PCIE))
*暂不公开* ([LUPO](https://github.com/wuhongyi/LUPO))


**其它**


[FFTW](https://github.com/wuhongyi/FFTW)
[CSM计算](https://github.com/wuhongyi/CSM)
[PB-5/MSCF远程控制界面(未完成)](https://github.com/wuhongyi/SerialCommunications)



----

<!-- more -->

## 本网站由 Hexo 搭建
Hexo新建博客：
为了预览效果，本地得安装 mathjax
安装 npm nodejs
~~~
# npm install -g hexo
$ npm init Hexo   //建立文件夹
$ cd Hexo
$ npm install    //这是使用默认模板，可以下载额外的模板来安装
~~~

常用命令
~~~
hexo clean
hexo g  //生成
hexo s  //预览 localhost:4000
hexo deploy  //发布
~~~

----
执行hexo server提示找不到该指令(在Hexo 3.0 后server被单独出来了，需要安装server):
~~~
npm install hexo-server --save
~~~

----
部署提示找不到git(在Hexo 3.0版本后deploy git 被分开的，所以需要安装):
~~~
npm install hexo-deployer-git --save
~~~

----
部署的时候执行：hexo deploy 命令行没有任何输出，也没有错误。 
解决办法： 
在部署的_config.yml文件中，找到deploy:标签，在每个冒号后面必须要空格，否则就会出现上述问题。我的配置如下：
~~~
deploy:
  type: git
  repository: git@github.com:wuhongyi/wuhongyi.github.io.git
  branch: master
~~~

## 扩展插件

### hexo-blog-encrypt

> https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md

- 在 hexo 根目录的 package.json 中添加 '"hexo-blog-encrypt": "1.1.*"' 依赖
- 然后执行 npm install 命令

在 _config.yml 中启用该插件
```
# Security
##
encrypt:
    enable: true
    default_abstract: the content has been encrypted, enter the password to read.</br>
    default_message: Please enter the password to read.	
```

## hexo升级

查看哪些需要升级
```
npm outdated
```

根据提示的版本修改一下 package.json 文件  

修改好了以后，就 npm 更新一下：
```
npm install --save
```

运行 Hexo 看看效果
```
hexo version
```



## gitbook 制作
得先安装 npm nodejs
```
# npm install -g gitbook-cli
$ gitbook -V
npm install gitbook-plugin-mermaid   流程图
npm install --save gitbook-plugin-anchor-navigation   锚点导航
```

### 更新到gitbook的最新版本
```
gitbook update
```

### 新建 gitbook

```
//在当前路径下新建Gitbook文件夹，并在里面生成README.md SUMMARY.md 文件
gitbook init Gitbook
gitbook serve [-p port] <Pathto>/Gitbook
```

### 插件 book.json

新建一个 book.json 文件，可以配置网站信息、在 plugins 和 pluginsConfig 字段添加插件等。

```
gitbook install ./
```
不要忘记这步，根目录 node_modules 文件下能看到安装那些插件。


<br />
----
**Update: 9. Last-Modified: 2018-09-15 12:11**
