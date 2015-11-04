---
layout: post
title: "ROOT Histograms"
date: 2015-06-07 10:16:59 +0800
comments: true
tags: [Linux,Software,Geant4-ROOT,TH1]
---
# 一些基本说明

ROOT 版本越高，其函数功能越多。随版本提升，不断修复以前版本的 bug，又增加了新功能。但是从 ROOT 6.03 开始，就要求 Python 版本大于 2.7（LLVM/Clang要求） ，但是在 Scientific Linux 6 下， 直接对系统的 Python 升级版本会造成桌面显示的问题，因为 Python 在版本提升中一些标准更改造成不兼容。一个好的方法就是在 /usr/local 下安装高版本的 Python，然后安装 ROOT 的时候给指定该 Python 的路径，还有一个偷懒的办法是直接更改 configure 文件中 Phyhon 版本的要求和 interpreter/llvm/src/configure 文件中 Python 版本的要求，这个会不会造成什么影响不清楚。<!--more-->

ROOT6 要求 gcc 版本大于 4.7，经过一年多的使用验证 gcc4.9.2 版本是可靠的。gcc5的版本暂时不要使用。这里我就选择 6.02/10 为基准来介绍 ROOT 的一些使用。

# Histograms

## TH1

### TH1(const char \*name,const char \*title,Int\_t nbinsx,Double\_t xlow,Double\_t xup);

这里 name 是该直方图的变量名字，这个名字命名最好符合 C/C++ 变量的规范，因为在将其保存成 ROOT 文件时候，该名字就是用来操作这个直方图的指针名，title 是用来描述该直方图的，nbinsx 是该直方图要分的分数，xlow 是该直方图的下限，xup 是该直方图的上限。

{% codeblock lang: C++ %}
TH1D *h=new TH1D("h","the title",100,0.0,10.0);
TH1D *hh=new TH1D("h1","",20,-1.0,1.0);
{% endcodeblock %}

### Bool\_t   Add(const TH1 \*h1, Double\_t c1=1);
该函数是将h1直方图进行c1倍的放大或缩小后加到当前直方图中。

{% codeblock lang: C++ %}
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

### Bool\_t   Add(const TH1 \*h1, const TH1 \*h2, Double\_t c1=1, Double\_t c2=1);
Replace contents of this histogram by the addition of h1 and h2.  
将 h1 放大或缩小 c1 倍加上 h2 放大或缩小 c2 倍相加赋给 h。

{% codeblock lang: C++ %}
TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
TH1D *h1=new TH1D("h1","",60,-3.0,3.0);
TH1D *h2=new TH1D("h1","",60,-3.0,3.0);
TRandom *r=new TRandom();
for (int i = 0; i<1000; ++i)
{
  // h->Fill(r->Uniform());
  h1->Fill(r->Uniform(1.5,2.5)); 
  h2->Fill(r->Uniform(-2.5,-1.5)); 
}
h->Add(h1,h2,2,1);
h->Draw();
{% endcodeblock %}

### void   AddBinContent(Int\_t bin);
在第 bin 个 bin 上计数加一。

{% codeblock lang: C++ %}
  TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
  for (int i = 0; i<10; ++i)
  {
    h->AddBinContent(10);
    h->AddBinContent(20);
    h->AddBinContent(50);
  }
  h->Draw();
{% endcodeblock %}

### TObject\*   Clone(const char\* newname=0) const;
克隆直方图，这里的 Close 是新开辟一块内存空间存储新直方图的，克隆之后，原始直方图的修改或删除对克隆的直方图没有影响。而TH1D \*hnew =h;这个只是将新建的对象指向 h 的地址。
{% codeblock lang: C++ %}
TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
TRandom *r =new TRandom();
for (int i = 0; i<1000; ++i)
{
  h->Fill(r->Gaus());
}
TH1D *hnew = (TH1D*)h->Clone("hnew");
// TH1D *hnew =h;
h->Reset();
hnew->Draw();
{% endcodeblock %}
### void   Draw(Option\_t \*option="");
默认情况下，Draw 将会清空当 前Pad 再画当前直方图，如想要一个图上多个直方图，将第二个及之后的直方图 Option\_t 设为 "same"。
{% codeblock lang: C++ %}
  TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
  TH1D *h1=new TH1D("h1","",60,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
  {
    h->Fill(r->Gaus());
    h1->Fill(r->Gaus(1,1));
  }
  h->Draw();
  // h1->Draw();
  h1->Draw("same");
{% endcodeblock %}

### Int\_t   Fill(Double\_t x);
将 $x$ 填充入直方图。

### Int\_t   Fill(Double\_t x, Double\_t w);
将 $x$ 填充入直方图，其中 $w$ 为权值。

### void   FillN(Int\_t ntimes, const Double\_t \*x, const Double\_t \*w, Int\_t stride=1);

### Int\_t   FindBin(Double\_t x, Double\_t y=0, Double\_t z=0);
对一维直方图，返回 x 值所在 bin，若为二维直方图，返回 （x，y） 所在 bin，三维同理。
{% codeblock lang: C++ %}
  TH1D *h=new TH1D("h","the title",60,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
  {
    h->Fill(r->Gaus());
  }
  std::cout<<h->FindBin(1.0)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   \*GetIntegral();
Return a pointer to the array of bins integral.  
返回一个指向数组的指针，当前值为 bin 从 0到当前 bin 面积所占的比例。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  double *Integral;
  Integral=h->GetIntegral();
  for (int i = 0; i<bin; ++i)
    {
      std::cout<<i<<"  "<<Integral[i]<<std::endl;
    }
  h->Draw();
{% endcodeblock %}

### Int\_t   GetBin(Int\_t binx, Int\_t biny=0, Int\_t binz=0) const;
Return Global bin number corresponding to binx,y,z.  2-D and 3-D histograms are represented with a one dimensional structure. This has the advantage that all existing functions, such as  GetBinContent, GetBinError, GetBinFunction work for all dimensions.

对一维直方图其没有作用，将二维、三维 bin 的编号方式转为一维的 bin 编号方式。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBin(41)<<std::endl;
  h->Draw();
{% endcodeblock %}

{% codeblock lang: C++ %}
  int bin=20;
  TH2D *h =new TH2D("h","the title",bin,-1.0,1.0,bin,-1.0,1.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),r->Gaus());
    }
  std::cout<<h->GetBin(0,0)<<std::endl;
  std::cout<<h->GetBin(0,1)<<std::endl;
  std::cout<<h->GetBin(1,0)<<std::endl;
  h->Draw();
{% endcodeblock %}

### void   GetBinXYZ(Int\_t binglobal, Int\_t \&binx, Int\_t \&biny, Int\_t \&binz) const;
这个在三维直方图中使用。

### Double\_t   GetBinCenter(Int\_t bin) const;
返回该bin的中点坐标。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBinCenter(30)<<std::endl;
  std::cout<<h->GetBinCenter(31)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetBinContent(Int\_t bin) const;
返回该bin的计数。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBinContent(12)<<std::endl;
  std::cout<<h->GetBinContent(31)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetBinContent(Int\_t bin, Int\_t) const;
二维直方图返回该bin的计数。

### Double\_t   GetBinContent(Int\_t bin, Int\_t, Int\_t) const;
三维直方图返回该bin的计数。

### Double\_t   GetBinError(Int\_t bin) const;
Return value of error associated to bin number bin. if the sum of squares of weights has been defined (via Sumw2),this function returns the sqrt(sum of w2). otherwise it returns the sqrt(contents) for this bin.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBinError(12)<<std::endl;
  std::cout<<h->GetBinError(31)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetBinError(Int\_t binx, Int\_t biny) const;
for 2D histograms only

同一维一样。

### Double\_t   GetBinError(Int\_t binx, Int\_t biny, Int\_t binz) const;
for 3D histograms only

同一维一样。

### Double\_t   GetBinErrorLow(Int\_t bin) const;
Return lower error associated to bin number bin. The error will depend on the statistic option used will return the binContent - lower interval value

### Double\_t   GetBinErrorUp(Int\_t bin) const;
Return upper error associated to bin number bin. The error will depend on the statistic option used will return the binContent - upper interval value

### Double\_t   GetBinLowEdge(Int\_t bin) const;
return bin lower edge for 1D historam
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBinLowEdge(12)<<std::endl;
  std::cout<<h->GetBinLowEdge(31)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetBinWidth(Int\_t bin) const;
返回bin的宽度。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetBinWidth(10)<<std::endl;
  std::cout<<h->GetBinWidth(31)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetEntries() const;
 returns the number of entries
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetEntries()<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t GetKurtosis(Int\_t axis=1) const;
For axis =1, 2 or 3 returns kurtosis of the histogram along x, y or z axis.Kurtosis(gaussian(0, 1)) = 0.

For axis =11, 12 or 13 returns the approximate standard error of kurtosis of the histogram along x, y or z axis. Note, that since third and fourth moment are not calculated at the fill time, kurtosis and its standard error are computed bin by bin
{% codeblock lang: C++ %}

{% endcodeblock %}

### Double\_t   GetMean(Int\_t axis=1) const;
returns the mean value along axis. For axis = 1,2 or 3 returns the mean value of the histogram along X,Y or Z axis. For axis = 11, 12, 13 returns the standard error of the mean value of the histogram along X, Y or Z axis.

Note that the mean value/RMS is computed using the bins in the currently defined range (see TAxis::SetRange). By default the range includes all bins from 1 to nbins included, excluding underflows and overflows. To force the underflows and overflows in the computation, one must call the static function TH1::StatOverflows(kTRUE) before filling the histogram.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetMean(1)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetMeanError(Int\_t axis=1) const;
Return standard error of mean of this histogram along the X axis.

Note that the mean value/RMS is computed using the bins in the currently defined range (see TAxis::SetRange). By default the range includes  all bins from 1 to nbins included, excluding underflows and overflows. To force the underflows and overflows in the computation, one must call the static function TH1::StatOverflows(kTRUE) before filling the histogram. 

 Also note, that although the definition of standard error doesn't include the assumption of normality, many uses of this feature implicitly assume it.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetMeanError(1)<<std::endl;
  h->Draw();
{% endcodeblock %}
### Int\_t   GetNbinsX() const;
获取该轴bin数。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),0.5);
    }
  std::cout<<h->GetNbinsX()<<std::endl;
  h->Draw();
{% endcodeblock %}

### Int\_t   GetNbinsY() const;
获取该轴bin数，在二维或三维直方图中使用。

### Int\_t   GetNbinsZ() const;
获取该轴bin数，在三维直方图中使用。

### Int\_t   GetNcells() const;
number of bins(1D), cells (2D) +U/Overflows
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),0.5);
    }
  std::cout<<h->GetNcells()<<std::endl;
  h->Draw();
{% endcodeblock %}
{% codeblock lang: C++ %}
  int bin=20;
  TH2D *h =new TH2D("h","the title",bin,-1.0,1.0,bin,-1.0,1.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),r->Gaus());
    }
  // int binx,biny;
  // h->GetBinXYZ(h->GetBin(10,10),binx,biny);
  // cout<<"binx:"<<binx<<"  biny:"<<biny<<std::endl;
  std::cout<<h->GetNcells()<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetNormFactor() const;
Normalization factor

如果没有设置将会返回0。
这个因子是将直方图面积设为该因子。设置该因子参考 SetNormFactor(Double\_t factor=1)。

### Double\_t   GetRandom() const;
return a random number distributed according the histogram bin contents.  This function checks if the bins integral exists. If not, the integral is evaluated, normalized to one. The integral is automatically recomputed if the number of entries is not the same then when the integral was computed. NB Only valid for 1-d histograms. Use GetRandom2 or 3 otherwise. If the histogram has a bin with negative content a NaN is returned
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetRandom()<<std::endl;
  h->Draw();
{% endcodeblock %}

### void   GetStats(Double\_t *stats) const;
fill the array stats from the contents of this histogram
The array stats must be correctly dimensioned in the calling program.
stats[0] = sumw
stats[1] = sumw2
stats[2] = sumwx
stats[3] = sumwx2

If no axis-subrange is specified (via TAxis::SetRange), the array stats is simply a copy of the statistics quantities computed at filling time.If a sub-range is specified, the function recomputes these quantities from the bin contents in the current axis range.

Note that the mean value/RMS is computed using the bins in the currently defined range (see TAxis::SetRange). By default the range includes all bins from 1 to nbins included, excluding underflows and overflows. To force the underflows and overflows in the computation, one must call the static function TH1::StatOverflows(kTRUE) before filling the histogram.

### Double\_t   GetStdDev(Int\_t axis=1) const;
返回 GetRMS(Int\_t axis=1)

### Double\_t   GetStdDevError(Int\_t axis=1) const;
返回 GetRMSError(Int\_t axis=1)

### Double\_t   GetSumOfWeights() const;
Return the sum of weights excluding under/overflows.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),0.5);
    }
  std::cout<<h->GetSumOfWeights()<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetRMS(Int\_t axis=1) const;
For axis = 1,2 or 3 returns the Sigma value of the histogram along X, Y or Z axis. For axis = 11, 12 or 13 returns the error of RMS estimation along X, Y or Z axis for Normal distribution.

Note that the mean value/sigma is computed using the bins in the currently defined range (see TAxis::SetRange). By default the range includes all bins from 1 to nbins included, excluding underflows and overflows. To force the underflows and overflows in the computation, one must  call the static function TH1::StatOverflows(kTRUE) before filling  the histogram.

Note that this function returns the Standard Deviation (Sigma) of the distribution (not RMS). The Sigma estimate is computed as Sqrt((1/N)*(Sum(x\_i-x\_mean)\^2)). The name "RMS" was introduced many years ago (Hbook/PAW times). 
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetRMS(1)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   GetRMSError(Int\_t axis=1) const;
Return error of RMS estimation for Normal distribution.

Note that the mean value/RMS is computed using the bins in the currently defined range (see TAxis::SetRange). By default the range includes all bins from 1 to nbins included, excluding underflows and overflows.  To force the underflows and overflows in the computation, one must call the static function TH1::StatOverflows(kTRUE) before filling the histogram.

Value returned is standard deviation of sample standard deviation.
Note that it is an approximated value which is valid only in the case that the original data distribution is Normal. The correct one would require the 4-th momentum value, which cannot be accurately estimated from an histogram since the x-information for all entries is not kept.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->GetRMSError(1)<<std::endl;
  h->Draw();
{% endcodeblock %}

### TAxis*   GetXaxis();
返回指向该坐标轴 TAxis* 的指针，可对该轴进行设置。具体参考TAxis类的使用。

### TAxis*   GetYaxis();
返回指向该坐标轴 TAxis* 的指针，可对该轴进行设置。具体参考TAxis类的使用。

### TAxis*   GetZaxis()
返回指向该坐标轴 TAxis* 的指针，可对该轴进行设置。具体参考TAxis类的使用。

### Double\_t   Integral(Option\_t *option="") const;
Return integral of bin contents. Only bins in the bins range are considered. By default the integral is computed as the sum of bin contents in the range.
if option "width" is specified, the integral is the sum of the bin contents multiplied by the bin width in x.
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<1000; ++i)
    {
      h->Fill(r->Gaus(),0.3);
    }
  std::cout<<h->Integral()<<std::endl;
  std::cout<<h->Integral(10,40)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Double\_t   Integral(Int\_t binx1, Int\_t binx2, Option\_t \*option="") const;
Return integral of bin contents in range [binx1,binx2]. By default the integral is computed as the sum of bin contents in the range.
if option "width" is specified, the integral is the sum of the bin contents multiplied by the bin width in x.

### Double\_t Interpolate(Double\_t x);
Given a point x, approximates the value via linear interpolation based on the two nearest bin centers.
获取x的y值。其值为最近两个bin线性插值。
{% codeblock lang: C++ %}
  int bin=60;
  TH1D *h=new TH1D("h","the title",bin,-3.0,3.0);
  TRandom *r =new TRandom();
  for (int i = 0; i<100000; ++i)
    {
      h->Fill(r->Gaus());
    }
  std::cout<<h->Interpolate(0.0)<<std::endl;
  h->Draw();
{% endcodeblock %}

### Bool\_t   IsBinOverflow(Int\_t bin) const;
Return true if the bin is overflow.

### Bool\_t   IsBinUnderflow(Int\_t bin) const;
Return true if the bin is overflow.

### void     LabelsDeflate(Option\_t *axis="X");
 Reduce the number of bins for the axis passed in the option to the number of bins having a label. The method will remove only the extra bins existing after the last "labeled" bin.

Note that if there are "un-labeled" bins present between "labeled" bins they will not be removed.
{% codeblock lang: C++ %}
   const Int_t nx = 20;
   const char *people[nx] = {"Jean","Pierre","Marie","Odile","Sebastien","Fons","Rene","Nicolas","Xavier","Greg","Bjarne","Anton","Otto","Eddy","Peter","Pasha","Philippe","Suzanne","Jeff","Valery"};
   TCanvas *c1 = new TCanvas("c1","demo bin labels",10,10,900,500);
   TH1F *h = new TH1F("h","test",3,0,3);
   for (Int_t i=0;i<5000;i++) {
      Int_t r = gRandom->Rndm()*20;
      h->Fill(people[r],1);
   }
   h->LabelsDeflate("X");
   h->Draw();
{% endcodeblock %}

### void     LabelsInflate(Option\_t *axis="X");
Double the number of bins for axis.Refill histogram. This function is called by TAxis::FindBin(const char \*label).

### oid     LabelsOption(Option\_t \*option="h", Option\_t \*axis="X");
Set option(s) to draw axis with labels

option = "a" sort by alphabetic order  
\hspace*{1.75cm}= ">" sort by decreasing values  
\hspace*{1.75cm}= "<" sort by increasing values  
\hspace*{1.75cm}= "h" draw labels horizontal  
\hspace*{1.75cm}= "v" draw labels vertical  
\hspace*{1.75cm}= "u" draw labels up (end of label right adjusted)  
\hspace*{1.75cm}= "d" draw labels down (start of label left adjusted)  
{% codeblock lang: C++ %}
   const Int_t nx = 12;
   const Int_t ny = 20;
   const char *month[nx]  = {"January","February","March","April","May","June","July","August","September","October","November","December"};
   const char *people[ny] = {"Jean","Pierre","Marie","Odile","Sebastien","Fons","Rene","Nicolas","Xavier","Greg","Bjarne","Anton","Otto","Eddy","Peter","Pasha","Philippe","Suzanne","Jeff","Valery"};
   TCanvas *c1 = new TCanvas("c1","demo bin labels",10,10,800,800);
   // c1->SetGrid();
   // c1->SetLeftMargin(0.15);
   // c1->SetBottomMargin(0.15);
   TH2F *h = new TH2F("h","test",3,0,3,2,0,2);
   // h->SetCanExtend(TH1::kAllAxes);
   // h->SetStats(0);
   gRandom->SetSeed();
   for (Int_t i=0;i<15000;i++) {
      Int_t rx = gRandom->Rndm()*nx;
      Int_t ry = gRandom->Rndm()*ny;
      h->Fill(people[ry],month[rx],1);
   }
   h->LabelsDeflate("X");
   h->LabelsDeflate("Y");
   h->LabelsOption("v","X");
   // h->LabelsOption("u","Y");
   h->Draw("text");
{% endcodeblock %}

### Bool\_t   Multiply(TF1 \*h1, Double\_t c1=1);
Performs the operation: this = this*c1*f1. if errors are defined (see TH1::Sumw2), errors are also recalculated.

Only bins inside the function range are recomputed. IMPORTANT NOTE: If you intend to use the errors of this histogram later you should call Sumw2 before making this operation. This is particularly important if you fit the histogram after TH1::Multiply The function return kFALSE if the Multiply operation failed.

### Bool\_t   Multiply(const TH1 *h1);
Multiply this histogram by h1.  this = this*h1

If errors of this are available (TH1::Sumw2), errors are recalculated.  Note that if h1 has Sumw2 set, Sumw2 is automatically called for this if not already set.

IMPORTANT NOTE: If you intend to use the errors of this histogram later you should call Sumw2 before making this operation. This is particularly important if you fit the histogram after TH1::Multiply. The function return kFALSE if the Multiply operation failed

### Bool\_t   Multiply(const TH1 \*h1, const TH1 \*h2, Double\_t c1=1, Double\_t c2=1, Option\_t \*option="");
Replace contents of this histogram by multiplication of h1 by h2. this = (c1*h1)*(c2*h2)

If errors of this are available (TH1::Sumw2), errors are recalculated. Note that if h1 or h2 have Sumw2 set, Sumw2 is automatically called for this if not already set.

IMPORTANT NOTE: If you intend to use the errors of this histogram later you should call Sumw2 before making this operation. This is particularly important if you fit the histogram after TH1::Multiply. The function return kFALSE if the Multiply operation failed.

### void     Paint(Option\_t \*option="");}
Control routine to paint any kind of histograms.  This function is automatically called by TCanvas::Update.(see TH1::Draw for the list of options)

### void     Print(Option\_t \*option="") const;}
Print some global quantities for this histogram.

If option "base" is given, number of bins and ranges are also printed  
If option "range" is given, bin contents and errors are also printed for all bins in the current range (default 1-->nbins)  
If option "all" is given, bin contents and errors are also printed for all bins including under and overflows.

### TH1     \*Rebin(Int\_t ngroup=2, const char \*newname="", const Double\_t \*xbins=0);
Rebin this histogram

-case 1  xbins=0

If newname is blank (default), the current histogram is modified and a pointer to it is returned. If newname is not blank, the current histogram is not modified, and a new histogram is returned which is a Clone of the current histogram with its name set to newname. The parameter ngroup indicates how many bins of this have to be merged into one bin of the result. If the original histogram has errors stored (via Sumw2), the resulting histograms has new errors correctly calculated.

examples: if h1 is an existing TH1F histogram with 100 bins
~~~
h1->Rebin();  //merges two bins in one in h1: previous contents of h1 are lost
h1->Rebin(5); //merges five bins in one in h1
TH1F *hnew = h1->Rebin(5,"hnew"); // creates a new histogram hnew merging 5 bins of h1 in one bin
~~~

NOTE:  If ngroup is not an exact divider of the number of bins, the top limit of the rebinned histogram is reduced to the upper edge of the last bin that can make a complete group. The remaining bins are added to the overflow bin. Statistics will be recomputed from the new bin contents.

-case 2  xbins!=0

A new histogram is created (you should specify newname). The parameter ngroup is the number of variable size bins in the created histogram. The array xbins must contain ngroup+1 elements that represent the low-edges of the bins. If the original histogram has errors stored (via Sumw2), the resulting histograms has new errors correctly calculated.

NOTE:  The bin edges specified in xbins should correspond to bin edges in the original histogram. If a bin edge in the new histogram is in the middle of a bin in the original histogram, all entries in the split bin in the original histogram will be transfered to the lower of the two possible bins in the new histogram. This is probably not what you want.

examples: if h1 is an existing TH1F histogram with 100 bins
~~~
Double\_t xbins[25] = {...} array of low-edges (xbins[25] is the upper edge of last bin
h1->Rebin(24,"hnew",xbins);  //creates a new variable bin size histogram hnew
~~~

### void     Rebuild(Option\_t \*option="");
Using the current bin info, recompute the arrays for contents and errors

### void     RecursiveRemove(TObject \*obj);

### void     Reset(Option\_t \*option="");
Reset this histogram: contents, errors, etc.

if option "ICE" is specified, resets only Integral, Contents and Errors.  
if option "ICES" is specified, resets only Integral, Contents , Errors and Statistics  
if option "M"   is specified, resets also Minimum and Maximum  
The option "ICE" is used when extending the histogram (in ExtendAxis, LabelInflate, etc..)  
The option "ICES is used in combination with the buffer (see BufferEmpty and BufferFill)  

### void     ResetStats();
Reset the statistics including the number of entries and replace with values calculates from bin content. The number of entries is set to the total bin content or (in case of weighted histogram) to number of effective entries.

### void     Scale(Double\_t c1=1, Option\_t \*option="");
Multiply this histogram by a constant c1. this = c1*this

Note that both contents and errors(if any) are scaled. This function uses the services of TH1::Add

IMPORTANT NOTE: If you intend to use the errors of this histogram later you should call Sumw2 before making this operation. This is particularly important if you fit the histogram after TH1::Scale

One can scale an histogram such that the bins integral is equal to the normalization parameter via TH1::Scale(Double\_t norm), where norm is the desired normalization divided by the integral of the histogram.

If option contains "width" the bin contents and errors are divided by the bin width.

### void     SetAxisColor(Color\_t color=1, Option\_t \*axis="X");

### void     SetAxisRange(Double\_t xmin, Double\_t xmax, Option\_t \*axis="X");

### void     SetBarOffset(Float\_t offset=0.25) {fBarOffset = Short\_t(1000*offset);}

### void     SetBarWidth(Float\_t width=0.5) {fBarWidth = Short\_t(1000*width);}

### void     SetBinContent(Int\_t bin, Double\_t content);

### void     SetBinContent(Int\_t bin, Int\_t, Double\_t content);

### void     SetBinContent(Int\_t bin, Int\_t, Int\_t, Double\_t content);

### void     SetBinError(Int\_t bin, Double\_t error);

### void     SetBinError(Int\_t binx, Int\_t biny, Double\_t error);

### void     SetBinError(Int\_t binx, Int\_t biny, Int\_t binz, Double\_t error);

### void     SetBins(Int\_t nx, Double\_t xmin, Double\_t xmax);

### void     SetBins(Int\_t nx, const Double\_t \*xBins);

### void     SetBins(Int\_t nx, Double\_t xmin, Double\_t xmax, Int\_t ny, Double\_t ymin, Double\_t ymax);

### void     SetBins(Int\_t nx, const Double\_t \*xBins, Int\_t ny, const Double\_t \*yBins);

### void     SetBins(Int\_t nx, Double\_t xmin, Double\_t xmax, Int\_t ny, Double\_t ymin, Double\_t ymax, Int\_t nz, Double\_t zmin, Double\_t zmax);

### void     SetBins(Int\_t nx, const Double\_t \*xBins, Int\_t ny, const Double\_t \* yBins, Int\_t nz, const Double\_t \*zBins);

### void     SetBuffer(Int\_t buffersize, Option\_t \*option="");

### void     SetDirectory(TDirectory \*dir);

### void     SetLabelColor(Color\_t color=1, Option\_t \*axis="X");

### void     SetLabelFont(Style\_t font=62, Option\_t \*axis="X");

### void     SetLabelOffset(Float\_t offset=0.005, Option\_t \*axis="X");

### void     SetLabelSize(Float\_t size=0.02, Option\_t \*axis="X");

### void     SetMaximum(Double\_t maximum = -1111);
Set the minimum / maximum value for the Y axis (1-D histograms) or Z axis (2-D histograms). By default the maximum / minimum value used in drawing is the maximum / minimum value of the histogram plus a margin of 10\%. If these functions are called, the values are used without any extra margin.

### void     SetMinimum(Double\_t minimum = -1111);

### void     SetName(const char \*name);
Change the name of this histogram

Histograms are named objects in a THashList. We must update the hashlist if we change the name. We protect this operation

### void     SetNameTitle(const char \*name, const char \*title);
Change the name and title of this histogram

Histograms are named objects in a THashList. We must update the hashlist if we change the name

### void     SetNdivisions(Int\_t n=510, Option\_t \*axis="X");

### void     SetNormFactor(Double\_t factor=1);

### void     SetStats(Bool\_t stats=kTRUE);

### void     SetTickLength(Float\_t length=0.02, Option\_t \*axis="X");

### void     SetTitleFont(Style\_t font=62, Option\_t \*axis="X");

### void     SetTitleOffset(Float\_t offset=1, Option\_t \*axis="X");

### void     SetTitleSize(Float\_t size=0.02, Option\_t \*axis="X");

### void     SetTitle(const char \*title);
Change (i.e. set) the title

if title is in the form "stringt;stringx;stringy;stringz" the histogram title is set to stringt, the x axis title to stringx, the y axis title to stringy, and the z axis title to stringz. To insert the character ";" in one of the titles, one should use "\#;" or "\#semicolon".

### void     SetXTitle(const char \*title);

### void     SetYTitle(const char \*title);

### void     SetZTitle(const char \*title);

### TH1     \*ShowBackground(Int\_t niter=20, Option\_t \*option="same");
This function calculates the background spectrum in this histogram.The background is returned as a histogram.
Function parameters:  
\hspace*{0.5cm}-niter, number of iterations (default value = 2). Increasing niter make the result smoother and lower.  
\hspace*{0.5cm}-option: may contain one of the following options  
\hspace*{1.0cm}- to set the direction parameter "BackDecreasingWindow". By default the direction is BackIncreasingWindow  
\hspace*{1.0cm}- filterOrder-order of clipping filter,  (default "BackOrder2"  
\hspace*{2.0cm}                  -possible values= "BackOrder4"  
\hspace*{5.1cm}                                    "BackOrder6"  
\hspace*{5.1cm}                                    "BackOrder8"  
\hspace*{1.0cm}- "nosmoothing"- if selected, the background is not smoothed. By default the background is smoothed.  
\hspace*{1.0cm}- smoothWindow-width of smoothing window, (default is "BackSmoothing3")  
\hspace*{2.0cm}                  -possible values= "BackSmoothing5"  
\hspace*{5.1cm}                                    "BackSmoothing7"  
\hspace*{5.1cm}                                    "BackSmoothing9"  
\hspace*{5.1cm}                                    "BackSmoothing11"  
\hspace*{5.1cm}                                    "BackSmoothing13"  
\hspace*{5.1cm}                                    "BackSmoothing15"  
\hspace*{1.0cm}- "nocompton"- if selected the estimation of Compton edge will be not be included   (by default the compton estimation is set)  
\hspace*{1.0cm}- "same" : if this option is specified, the resulting background histogram is superimposed on the picture in the current pad. This option is given by default.  

NOTE that the background is only evaluated in the current range of this histogram. i.e., if this has a bin range (set via h->GetXaxis()->SetRange(binmin, binmax), the returned histogram will be created with the same number of bins as this input histogram, but only bins from binmin to binmax will be filled with the estimated background.

### Int\_t    ShowPeaks(Double\_t sigma=2, Option\_t \*option="", Double\_t threshold=0.05);
Interface to TSpectrum::Search.

The function finds peaks in this histogram where the width is > sigma and the peak maximum greater than threshold*maximum bin content of this. For more details see TSpectrum::Search. Note the difference in the default value for option compared to TSpectrum::Search option="" by default (instead of "goff").

### void     Smooth(Int\_t ntimes=1, Option\_t \*option="");
Smooth bin contents of this histogram. if option contains "R" smoothing is applied only to the bins defined in the X axis range (default is to smooth all bins). Bin contents are replaced by their smooth values. Errors (if any) are not modified. the smoothing procedure is repeated ntimes (default=1)

### void     SmoothArray(Int\_t NN, Double\_t \*XX, Int\_t ntimes=1);
smooth array xx, translation of Hbook routine hsmoof.F  
based on algorithm 353QH twice presented by J. Friedman in Proc.of the 1974 CERN School of Computing, Norway, 11-24 August, 1974.


** 还未发布 **

<br />
----
&#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160; &#160;**Last-Modified: 2015-09-27 23:12**
