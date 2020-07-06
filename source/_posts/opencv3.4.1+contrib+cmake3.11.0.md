---
title: Win10+vs2015+opencv3.4.1+附加模块opencv_contrib+cmake3.11.0编译和配置
tags: 
  - opencv
categories: 
  - 教程
date: 2018-4-2
---
> 最近在做基于opencv的课设项目，参考了很多篇教程，retry了很多次。我的编译和配置过程大多数参考[这篇文章](https://blog.csdn.net/chentravelling/article/details/59540828)。

<!--more-->

### 一、准备工具

#### 1. 系统：Win10 64位

#### 2. [opencv3.4.1](https://sourceforge.net/projects/opencvlibrary/files/opencv-win/)

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142221.png)

#### 3. [opencv_contrib](https://github.com/opencv/opencv_contrib/releases)

**opencv_contrib的版本一定要和opencv相同！！！**

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142528.png)

#### 4. [cmake](https://cmake.org/download/)

选择Binary distributions是它已经编译好了的cmake，根据自己的计算机和VS位数选择对应版本，我的是win10 64位，VS2015 64位

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142545.png)

#### 5. VS2015（我的是64位的community版本）

### 二、Cmake编译

#### 1. 在opencv文件夹下新建一个mybuild文件夹（命名任意），存放我们的编译结果 

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142604.png)

#### 2. 安装cmake，打开bin目录下的cmake-gui.exe

在where is the source code输入opencv地址/sources地址

在where to build the libraries输入保存编译结果的地址

点击Configure选择对应自己电脑上的VS版本的编译器，**对于VS2015来说，32位的选择Visual Studio 14 2015，64位的选择Visual Studio 14 2015 Win64，点击finish后自动进行第一次编译**

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142629.png)

#### 3. 第二次编译

第一次编译完成后会显示编译opencv所需要的参数。在Name为OPENCV_EXTRA_MODULES_PATH的Value中填入**opencv_contrib-3.0.0的路径/modules**，如下图所示。然后点击Configure进行第二次编译。

**第二次编译完后一定要检查一下参数列表，如果参数列表还有红色标记的条目，就再尝试几次configure，直到所有条目都是白色为止。**

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142705.png)

#### 4. 点击Generate，直到出现Generate Done

这时候在我们的mybuild文件夹下可以看到编译出的文件。

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142726.png)

#### 5. 检查一下附加模版是否成功编译并加入到opencv中

进入之前我们新建的mybuild文件夹下，进入modules，查看是否有下图所示的文件，这些是属于附加模块opencv_contrib的。如果没有，检查一下cmake参数列表中Name为OPENCV_EXTRA_MODULES_PATH的Value是否为opencv_contrib-3.0.0的路径/modules，不是的话重新设置、重新Configure以及重新Generate。

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142743.png)

### 三、生成库文件

#### 1. 在mybuild文件夹中，找到OpenCV.sln并打开

#### 2. 在解决方案资源管理器中找到CMake Targets，右键点击“生成”

生成成功后，如果在mybuild文件夹中还没有出现一个名为install的文件夹，回到VS界面，右击INSTALL->仅用于项目->仅生成INSTALL，再次生成成功后就会出现install文件夹。

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142759.png)

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142811.png)

**问题：无法打开文件‘python27_d.lib' && 无法打开文件‘python36_d.lib'**

生成INSTALL时，我碰到过下面的问题（当时忘记截图了），问题是：无法打开文件‘python27_d.lib'和无法打开文件‘python36_d.lib'

原因是我之前在电脑上安装了Anaconda2和Anaconda3，openCV用Cmake编译时都检测到了。解决方法是分别打开python2和python3对应的pyconfig.h文件，做两处修改。

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142829.png)

如python2对应的pyconfig.h：

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142838.png)

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142854.png)

如果python3对应的也有问题的话也将其对应的pyconfig.h做以上修改。

### 四、配置

#### 1. 系统环境变量

在**计算机-环境变量-path**中增加：where to build the binaries中设置的路径\install\x64\vc14\bin

**注意：如果是32位的VS的话，路径应该是where to build the binaries中设置的路径\install\x86\vc14\bin**

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142904.png)

#### 2. 配置新的工程

打开VS2015，新建一个工程

找到属性管理器->Debug|x64->右击Microsoft.Cpp.x64.user->属性

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142921.png)

**1. VC++目录-包含目录**

```
<where to build the binaries中设置的路径>\install\include
<where to build the binaries中设置的路径>\install\include\opencv
<where to build the binaries中设置的路径>\install\include\<opencv2>
```

**2. VC++目录-库目录**

```
<where to build the binaries中设置的路径>\install\x64\vc14\lib
```

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142931.png)

**3. 链接器-输入-附加依赖项**

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142947.png)

这里添加的.lib文件都需要出现在`<where to build the binaries中设置的路径>\install\x64\vc14\lib` 中。如果lib文件被添加到附加依赖项里，但是上述文件夹中没有该lib文件，会出现找不到XXX.lib的错误。

附上我的附加依赖项（共44个）：

```
opencv_aruco341d.lib
opencv_bgsegm341d.lib
opencv_bioinspired341d.lib
opencv_calib3d341d.lib
opencv_ccalib341d.lib
opencv_core341d.lib
opencv_datasets341d.lib
opencv_dnn341d.lib
opencv_dnn_objdetect341d.lib
opencv_dpm341d.lib
opencv_face341d.lib
opencv_features2d341d.lib
opencv_flann341d.lib
opencv_fuzzy341d.lib
opencv_hfs341d.lib
opencv_highgui341d.lib
opencv_imgcodecs341d.lib
opencv_imgproc341d.lib
opencv_img_hash341d.lib
opencv_line_descriptor341d.lib
opencv_ml341d.lib
opencv_objdetect341d.lib
opencv_optflow341d.lib
opencv_phase_unwrapping341d.lib
opencv_photo341d.lib
opencv_plot341d.lib
opencv_reg341d.lib
opencv_rgbd341d.lib
opencv_saliency341d.lib
opencv_shape341d.lib
opencv_stereo341d.lib
opencv_stitching341d.lib
opencv_structured_light341d.lib
opencv_superres341d.lib
opencv_surface_matching341d.lib
opencv_text341d.lib
opencv_tracking341d.lib
opencv_video341d.lib
opencv_videoio341d.lib
opencv_videostab341d.lib
opencv_xfeatures2d341d.lib
opencv_ximgproc341d.lib
opencv_xobjdetect341d.lib
opencv_xphoto341d.lib
```

### 五、激动人心的时刻——测试

配了这么久，进了好多坑，终于到检验自己的时刻了！

在项目中新建一个cpp，**记得添加到工程里**。输入如下代码：

```C++
#include <iostream>  
#include <opencv2/core/core.hpp>  
#include<opencv2/highgui/highgui.hpp>  
using namespace cv;

	int main()
	{
		Mat img = imread("F:\\Object Tracking\\opencv_test\\timg.jpg");//读入一张图片
		namedWindow("Test");     //创建一个名为Test窗口
		imshow("Test", img);   //窗口中显示图像
		waitKey(5000);            //等待5000ms后窗口自动关闭
	}
```

![](https://raw.githubusercontent.com/Elody-07/PicBed/master/20190527142959.png)

Well Done！