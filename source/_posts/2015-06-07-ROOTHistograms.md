---
layout: post
title: "ROOT Histograms"
date: 2015-06-07 10:16:59 +0800
comments: true
categories: [Software,ROOT,TH1]
---
# 一些基本说明

ROOT 版本越高，其函数功能越多。随版本提升，不断修复以前版本的 bug，又增加了新功能。但是从 ROOT 6.03 开始，就要求 Python 版本大于 2.7（LLVM/Clang要求） ，但是在 Scientific Linux 6 下， 直接对系统的 Python 升级版本会造成桌面显示的问题，因为 Python 在版本提升中一些标准更改造成不兼容。一个好的方法就是在 /usr/local 下安装高版本的 Python，然后安装 ROOT 的时候给指定该 Python 的路径，还有一个偷懒的办法是直接更改 configure 文件中 Phyhon 版本的要求和 interpreter/llvm/src/configure 文件中 Python 版本的要求，这个会不会造成什么影响不清楚。<!--more-->

ROOT6 要求 gcc 版本大于 4.7，经过一年多的使用验证 gcc4.9.2 版本是可靠的。gcc5的版本暂时不要使用。这里我就选择 6.02/10 为基准来介绍 ROOT 的一些使用。

# Histograms

## TH1

### TH1(const char *name,const char *title,Int\_t nbinsx,Double\_t xlow,Double\_t xup);

这里 name 是该直方图的变量名字，这个名字命名最好符合 C/C++ 变量的规范，因为在将其保存成 ROOT 文件时候，该名字就是用来操作这个直方图的指针名，title 是用来描述该直方图的，nbinsx 是该直方图要分的分数，xlow 是该直方图的下限，xup 是该直方图的上限。

{% codeblock lang:C++ %}
TH1D *h=new TH1D("h","the title",100,0.0,10.0);
TH1D *hh=new TH1D("h1","",20,-1.0,1.0);
{% endcodeblock %}

### Bool\_t   Add(const TH1 *h1, Double\_t c1=1);
该函数是将h1直方图进行c1倍的放大或缩小后加到当前直方图中。

{% codeblock lang:C++ %}
TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
TH1D *h1=new TH1D("h1","",60,-3.0,3.0);
TRandom *r=new TRandom();
for (int i = 0; i<1000; ++i)
{
  h->Fill(r->Uniform());
  h1->Fill(r->Uniform(1.5,2.5)); 
}
h->Add(h1,2);
h->Draw();
{% endcodeblock %}



<br />
----
&#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160;**Last-Modified: 2015-09-27 23:12**
