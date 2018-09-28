title: kinect开发：VS2015平台下获取人体深度图及关节点深度坐标
tags: 
  - C++
categories:
  - 教程
date: 2018-9-28
---
> 做手势、人体姿态识别应该对kinect不陌生，我最近也是初次接触kinect及其开发，还有很多不足的地方，一边学习一边记录才能进步~
>
> 开发环境：win10 + VS2015 + OpenCV3.4.1 + Kinect V2

<!--more-->

## 一、配置VS

前期准备需要下载Kinect for Windows SDK 2.0，在[官网](http://www.k4w.cn/news/1.html)中下载后点击安装即可，安装好后自带一些例程与Kinect Studio（可检测Kinect是否与电脑连接），在此不赘述。新建一个VS项目（默认空项目即可）

### 1. 配置opencv

参考我的[上一篇博客](https://elody-07.github.io/opencv3.4.1+contrib+cmake3.11.0/#1-系统环境变量)的4.2.2 配置新的工程 部分

### 2. 配置Kinect

【解决方案资源管理器】----> 【属性】

**C/C++ - 附加包含目录**

`$(KINECTSDK20_DIR)\inc`

**链接器-常规-附加库目录**

`$(KINECTSDK20_DIR)\Lib\x64` 注意选择x86或x64

**链接器-输入-附加依赖项**

`kinect20.lib`

## 二、代码及注释

以下代码实现的功能是：从Kinect中获得人体彩色图、深度图并显示。当检测到左手时，画出左手的位置，并将深度图保存成png文件，将左手在深度图中的位置信息输出到txt文件中。

```C++
#include <iostream>
#include <opencv2\imgproc.hpp>  //opencv头文件
#include <opencv2\calib3d.hpp>
#include <opencv2\highgui.hpp>
#include <Kinect.h> //Kinect头文件
#include <fstream>

using   namespace   std;
using   namespace   cv;

void    draw_color(Mat & img, Joint & r_1, Joint & r_2, ICoordinateMapper * myMapper);
void    draw_depth(Mat & img, Joint & r_1, Joint & r_2, ICoordinateMapper * myMapper);
int main(void)
{
	IKinectSensor   * mySensor = nullptr;
	GetDefaultKinectSensor(&mySensor);
	mySensor->Open();

	IColorFrameSource   * myColorSource = nullptr;
	mySensor->get_ColorFrameSource(&myColorSource);
	IColorFrameReader   * myColorReader = nullptr;
	myColorSource->OpenReader(&myColorReader);

	int colorHeight = 0, colorWidth = 0;
	IFrameDescription   * myDescription = nullptr;
	myColorSource->get_FrameDescription(&myDescription);
	myDescription->get_Height(&colorHeight);
	myDescription->get_Width(&colorWidth);

	IColorFrame * myColorFrame = nullptr;
	Mat original(colorHeight, colorWidth, CV_8UC4);

	IDepthFrameSource *myDepthSource = nullptr; //获取深度数据
	mySensor->get_DepthFrameSource(&myDepthSource);
	IDepthFrameReader *myDepthReader = nullptr;
	myDepthSource->OpenReader(&myDepthReader); //打开深度数据的Reader

	int depthHeight = 0, depthWidth = 0;
	myDepthSource->get_FrameDescription(&myDescription);
	myDescription->get_Height(&depthHeight);
	myDescription->get_Width(&depthWidth);

	IDepthFrame *myDepthFrame = nullptr;
	//Mat temp(depthHeight, depthWidth, CV_16UC1); //建立图像矩阵
	Mat depthImg(depthHeight, depthWidth, CV_16UC1);

	//**********************以上为ColorFrame的读取前准备**************************

	IBodyFrameSource    * myBodySource = nullptr;
	mySensor->get_BodyFrameSource(&myBodySource);
	IBodyFrameReader    * myBodyReader = nullptr;
	myBodySource->OpenReader(&myBodyReader);

	IBodyFrame  * myBodyFrame = nullptr;

	int myBodyCount = 0;
	myBodySource->get_BodyCount(&myBodyCount);
	
	ICoordinateMapper   * myMapper = nullptr;
	mySensor->get_CoordinateMapper(&myMapper);

	char file_name[20];
	int a = 0;

	//**********************以上为BodyFrame以及Mapper的准备***********************
	while (1)
	{

		while (myColorReader->AcquireLatestFrame(&myColorFrame) != S_OK);
		while (myDepthReader->AcquireLatestFrame(&myDepthFrame) != S_OK);

		myColorFrame->CopyConvertedFrameDataToArray(colorHeight * colorWidth * 4, original.data, ColorImageFormat_Bgra);
		Mat copy = original.clone();        //读取彩色图像并输出到矩阵

		myDepthFrame->CopyFrameDataToArray(depthHeight*depthWidth, (UINT16 *)depthImg.data); //先把数据存入16位的图向矩阵中（原始数据）
		
		//temp.convertTo(depthImg, CV_8UC1, 255.0 / 4500); //再把16位转成8位

		

		while (myBodyReader->AcquireLatestFrame(&myBodyFrame) != S_OK); //读取身体图像
		IBody   **  myBodyArr = new IBody *[myBodyCount];       //为存身体数据的数组做准备
		for (int i = 0; i < myBodyCount; i++)
			myBodyArr[i] = nullptr;

		if (myBodyFrame->GetAndRefreshBodyData(myBodyCount, myBodyArr) == S_OK)     //把身体数据输入数组
			for (int i = 0; i < myBodyCount; i++)
			{
				BOOLEAN     result = false;
				if (myBodyArr[i]->get_IsTracked(&result) == S_OK && result) //先判断是否侦测到
				{
					Joint   myJointArr[JointType_Count];
					if (myBodyArr[i]->GetJoints(JointType_Count, myJointArr) == S_OK)   //如果侦测到就把关节数据输入到数组并画图
					{
						
						/*imshow("TEST", depthImg);
						sprintf_s(file_name, "%08d.png",a++);
						imwrite(file_name, depthImg);
						Sleep(5 * 10);*/
						//cout << depthImg << endl;
						
						Joint center = myJointArr[JointType_HandLeft];
						float center_z = center.Position.Z; //Camera Point z-axis

						DepthSpacePoint center_t;
						myMapper->MapCameraPointToDepthSpace(center.Position, &center_t);
						float center_x = center_t.X;
						float center_y = center_t.Y;

						/*cout << "CameraPoint:" << center.Position.X << " " << center.Position.Y << endl;
						cout << "DepthSpace:" << center_t.X << " " << center_t.Y << endl;*/

						//写入中心点hand_left的位置信息
						ofstream outfile("F:\\Kinect\\kinect_demo\\kinect_demo\\center.txt", ios::app);
						outfile << center_x << " " << center_y << " " << center_z << endl;
						outfile.close();
												
						draw_depth(depthImg, myJointArr[JointType_WristLeft], myJointArr[JointType_HandLeft], myMapper);
						draw_color(copy, myJointArr[JointType_WristLeft], myJointArr[JointType_HandLeft], myMapper);
						
					}
				}
			}
		delete[]myBodyArr;
		myBodyFrame->Release();
		myColorFrame->Release();
		myDepthFrame->Release();
		
		imshow("depth", depthImg);
		imshow("color", copy);
		if (waitKey(30) == VK_ESCAPE)
			break;
	}
	myMapper->Release();

	myDescription->Release();
	myDepthReader->Release();
	myDepthSource->Release();
	myColorReader->Release();
	myColorSource->Release();

	myBodyReader->Release();
	myBodySource->Release();
	mySensor->Close();
	mySensor->Release();

	return  0;
}

void    draw_depth(Mat & img, Joint & r_1, Joint & r_2, ICoordinateMapper * myMapper)
{
	//用两个关节点来做线段的两端，并且进行状态过滤
	if (r_1.TrackingState == TrackingState_Tracked && r_2.TrackingState == TrackingState_Tracked)
	{
		//ColorSpacePoint t_point;    //要把关节点用的摄像机坐标下的点转换成彩色空间的点
		DepthSpacePoint t_point;
		Point   p_1, p_2;
		myMapper->MapCameraPointToDepthSpace(r_1.Position, &t_point);
		p_1.x = t_point.X;
		p_1.y = t_point.Y;
		myMapper->MapCameraPointToDepthSpace(r_2.Position, &t_point);
		p_2.x = t_point.X;
		p_2.y = t_point.Y;
		//int z_depth = img.at<uchar>(p_2.x, p_2.y);
		//int z = r_2.Position.Z;
		//cout << "z:" << r_2.Position.Z << endl;//(in meters).你就z的值，返回这个就可以了 xy返回深度的？对
		cout << "z_depth:" << img.at<uchar>(int(p_2.x), int(p_2.y)) << endl;

		//cout << "p1(x,y):" << p_1.x << ", " << p_1.y << endl;
		//cout <<"p2(x,y) "<< p_2.x << ", " << p_2.y << '/n';
		//line(img, p_1, p_2, 255, 5);
		//circle(img, p_1, 10, cv::Scalar(255));

		circle(img, p_2, 10, cv::Scalar(255));
	}
}

void    draw_color(Mat & img, Joint & r_1, Joint & r_2, ICoordinateMapper * myMapper)
{
	//用两个关节点来做线段的两端，并且进行状态过滤
	if (r_1.TrackingState == TrackingState_Tracked && r_2.TrackingState == TrackingState_Tracked)
	{
		ColorSpacePoint t_point;    //要把关节点用的摄像机坐标下的点转换成彩色空间的点
		Point   p_1, p_2;
		myMapper->MapCameraPointToColorSpace(r_1.Position, &t_point);
		p_1.x = t_point.X;
		p_1.y = t_point.Y;
		myMapper->MapCameraPointToColorSpace(r_2.Position, &t_point);
		p_2.x = t_point.X;
		p_2.y = t_point.Y;
		//line(img, p_1, p_2, Vec3b(0, 255, 0), 5);
		//circle(img, p_1, 10, Vec3b(255, 0, 0), -1);
		circle(img, p_2, 10, Vec3b(255, 0, 0), -1);
	}
}

```

