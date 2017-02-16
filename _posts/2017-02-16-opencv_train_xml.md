---
layout: post
title: 利用OPENCV训练xml
author: j.s
date:   2017-02-16
categories: ComputerVision
permalink: /archivers/opencv_train_xml
---
<p class="lead">OpenCV的全称是Open Source Computer Vision Library，是一个跨平台的计算机视觉库。之前我们尝试利用openCV 2.4.13版本实现了（1）人脸识别和 （2）JAVA调用JNI程序类</p>

但是人脸识别使用的特征是opencv自己训练的xml，当然效果很不错，如果我们希望自己来定义图片的特征，举例：图片中有大片红色的定义为暴力图片，其他是非暴力的图片，那么我们就需要自己来训练得到xml。
本文尝试网上的实际例子，通过训练人脸图片来得到xml，并用我们训练得到的xml来验证效果。


我们将数据集拆分为：训练集、验证集。随机选取其中的20张图片作为验证集，剩余的图片都作为训练集。


## 准备
* windows7 64位
* eclipse 32位，jdk1.7
* openCV 2.4.13

## 一.原始图片数据预处理
为了减少计算量，将图片统一处理为20*20分辨率，使用java+openCV

{% highlight js %}

 Mat image = Highgui.imread(path);
 Mat resizeimage = new Mat();
 Size sz = new Size(20, 20);
 Imgproc.resize(image, resizeimage, sz);
}

{% endhighlight %}

## 二.准备数据集
准备数据集:
数据集下载：
[http://download.csdn.net/detail/qq_34470213/9743393](http://download.csdn.net/detail/qq_34470213/9743393)
[http://download.csdn.net/tag/yale%E4%BA%BA%E8%84%B8%E5%BA%93](http://download.csdn.net/tag/yale%E4%BA%BA%E8%84%B8%E5%BA%93)


1. 训练集，正样本：20*20图片155张 D:\MyFiles\dicproject\opencv\build\x64\vc11\bin\posdata
2. 训练集，负样本：20*20图片200张 D:\MyFiles\dicproject\opencv\build\x64\vc11\bin\negdata
3. 验证集，随机取了100X100图片30张，包含人脸和其他没有人脸的图片。（自己验证如果用20X20的图片验证时根本分辨不出，所以使用大图）
D:\MyFiles\dicproject\trainimages\validatedata

## 三.数据集处理
参考博客：
[http://blog.csdn.net/lileiyang12/article/details/10155813](http://blog.csdn.net/lileiyang12/article/details/10155813)

a)处理正样本创建集合文件格式文件info.txt

在命令行下 输入以下命令： 
> cd D:\MyFiles\dicproject\opencv\build\x64\vc11\bin\posdata

> dir /b > info.txt

b)打开info.txt, 按ctrl+h, 把所有的bmp 换成 bmp 1 0 0 20 20

c)删除info.txt最后一行的 “info.txt”

结果如下：

1.bmp 1 0 0 20 20

10.bmp 1 0 0 20 20

100.bmp 1 0 0 20 20



d)处理负样本创建集合文件格式文件bg.txt

在命令行下 输入以下命令

> cd D:\MyFiles\dicproject\opencv\build\x64\vc11\bin\negdata

> dir /b > bg.txt

e)删除bg.txt最后一行的 “bg.txt”
<dl>
  <dt>不用替换bmp 换成 bmp 1 0 0 20 20</dt>
</dl>

f)结果如下：

1.bmp

10.bmp

100.bmp

101.bmp

102.bmp


## 四.训练
首先需要创建正样例的文件

创建样本 num是正样本的图片个数 w和h是分辨率

> cd D:\MyFiles\dicproject\opencv\build\x64\vc11\bin

> opencv_createsamples.exe -info D:/MyFiles/dicproject/opencv/build/x64/vc11/bin/posdata/info.txt -vec a.vec -num 155 -w 20 -h 20

#训练得到xml

npos 是正样本的图片个数

nneg 是负样本的图片个数

其他参数保持默认

> opencv_haartraining.exe  -data mytrain -vec a.vec -bg D:/MyFiles/dicproject/opencv/build/x64/vc11/bin/negdata/bg.txt  -npos 155 -nneg 200 -nstages 4  -nsplits 1 -sym -w 20 -h 20 -mode all -mem 1024

cmd返回如下说明训练成功
{% highlight js %}
*** 1 cluster ***
POS: 155 155 1.000000
NEG: 200 0.0387147
BACKGROUND PROCESSING TIME: 1.97
Required leaf false alarm rate achieved. Branch t
Total number of splits: 0

Tree Classifier
Stage
+---+---+
|  0|  1|
+---+---+

   0---1


Cascade performance
POS: 155 155 1.000000
NEG: 200 0.0333444
BACKGROUND PROCESSING TIME: 2.34

D:\MyFiles\dicproject\opencv\build\x64\vc11\bin>
{% endhighlight %}


然后就会在目录D:\MyFiles\dicproject\opencv\build\x64\vc11\bin

生成需要的训练xml：mytrain.xml

xml部分内容如下：
{% highlight js %}
<?xml version="1.0"?>
<opencv_storage>
<mytrain type_id="opencv-haar-classifier">
  <size>
    20 20</size>
  <stages>
    <_>
      <!-- stage 0 -->
      <trees>
        <_>
          <!-- tree 0 -->
          <_>
            <!-- root node -->
            <feature>
              <rects>
                <_>
                  0 2 4 18 -1.</_>
                <_>
                  0 2 2 9 2.</_>
                <_>
                  2 11 2 9 2.</_></rects>
              <tilted>0</tilted></feature>
            <threshold>-3.8853779435157776e-002</threshold>
            <left_val>9.2973941564559937e-001</left_val>
            <right_val>-9.2286682128906250e-001</right_val></_></_>
{% endhighlight %}

			
## 五.利用训练生成的xml验证





