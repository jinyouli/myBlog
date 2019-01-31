---
title: >-
  OpenGLES
  Android篇零基础系列(二）：OpenGL各坐标系及模型矩阵(ModelViewMatrix),投影矩阵(ProjectionMatrix)等的深入理解 
date: 2017-07-07 17:57:38
categories:
tags:
---
**上一篇我们粗略的介绍了下GLES20 中 GLSurfaceView以及Render接口的使用。** 对于三角形<!-- more -->顶点坐标的定义并没有做出注释，其实在官方的ApiDemo中，它也是赤裸裸的，一个注释都没有，且代码写得一点都不敢恭维，不知道那位同行现在是不是还在google里面。下面贴出一小段官方的ApiDemo中的代码，一起鉴赏鉴赏：
```
private static final int FLOAT_SIZE_BYTES = 4; 
private static final int TRIANGLE_VERTICES_DATA_STRIDE_BYTES = 5 * FLOAT_SIZE_BYTES; private static final int TRIANGLE_VERTICES_DATA_POS_OFFSET = 0; 
private static final int TRIANGLE_VERTICES_DATA_UV_OFFSET = 3; 
private final float[] mTriangleVerticesData = { // X, Y, Z, U, V -1.0f, -0.5f, 0, 0.0f, 0.5f, 1.0f, -0.5f, 0, 1.0f, 0.5f, 0.0f, 1.0f, 0, 0.0f, 1.0f };

```

如上，你们能看懂吗？在定义三角形顶点坐标数据时，仅仅只是简单粗暴的注释X,Y,Z,U,V,其中X,Y,Z好理解，可这U,V又是什么呢？X,Y,Z,U,V它们之间又有什么联系呢？
如果仅仅只是想从ApiDemo里面去研究，去搞懂它们是什么，怎么用，那估计不是一天两天的事，还得从浩瀚的网络中去查找。
正文：坐标系
OpenGL有6种坐标系，分别如下：
1，物体或模型坐标系(Object or model coordinates);
2，世界坐标系（World coordinates）
3，眼坐标或相机坐标（Eye (or Camera) coordinates)
4，裁剪坐标系（Clip coordinates）
5，标准设备坐标系（Normalized device coordinates）
6，屏幕坐标系（Window (or screen) coordinates）除了上面6种外，OpenGL还存在一种假想坐标系**纹理坐标系**，这个坐标系是不存在的，它其实是一系列**变换矩阵**的结果，比如它能使顶点从**物体或模型坐标系**变换到**世界坐标系**。

从object coordainates到world coordinates再到camera coordinate的变换，在OpenGL中统一称为model-view转换，初始化的时候，object coordinates和world coordinates还有camera coordinates坐标重合在原点，变换矩阵都为Identity,所以在OpenGL中用glLoadIdentity()初始化变换矩阵栈。model-view matix转换points,vectorsd到camera坐标系。
OpenGL 的重要功能之一就是将三维的世界坐标经过变换、投影
等计算，最终算出它在显示设备上对应的位置，这个位置就称为设备坐标。在屏幕、打印机等设备上的坐标是二维坐标。值得一提的是，OpenGL可以只使用设备的一部分进行绘制，这个部分称为视区或视口(viewport)。投影得到的是视区内的坐标(投影坐标)，从投影坐标到设备坐标的计算过程就是设备变换了。
我们在OpenGL ES中常用到的几种坐标系：世界坐标系、物体坐标系、设备坐标系、眼坐标系
当然还有假想的纹理坐标系

一、屏幕坐标系
对于移动设备来说，我们都知道，左上角为坐标原点，向右为X轴，向下为Y轴。如下图（图画的丑了点，先忍忍啊^_^）：![这里写图片描述](http://upload-images.jianshu.io/upload_images/977602-19b4d34a5143fbf8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
二、世界坐标系
这个世界坐标系是针对OpenGL来说明的。即三维坐标系X,Y,Z.它有一个漂亮的学名：右手笛卡尔坐标系统
,这个坐标系常用来描述物体及光源的位置。在移动设备中，屏幕中心为坐标三点，水平向右为X轴，在原点垂直X轴向上为Y轴，在原点垂直X,Y轴指向屏幕外为Z轴（正面对手机屏幕，直戳你眼睛的就是Z轴），同样如下图：![这里写图片描述](http://upload-images.jianshu.io/upload_images/977602-5451d50f1999c5b2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
将物体放到场景中也就是将物体平移到特定位置、旋转一定角度，这些操作就是坐标变换。OpenGL中提供了glTranslate*/glRotate*/glScale*三条坐标变换命令，利用OpenGL的矩阵运算命令，则可以实现任意复杂的坐标变换。在这里还要提一个重要的概念：**坐标变换矩阵栈（ModelView)**
坐标变换矩阵栈
用来存储一系列的变换矩阵，栈顶就是当前坐标的变换矩阵，进入OpenGL管道的每个坐标(齐次坐标)都会先乘上这个矩阵，结果才是对应点在场景中的世界坐标。OpenGL中的坐标变换都是通过矩阵运算完成的。如图：![这里写图片描述](http://upload-images.jianshu.io/upload_images/977602-2629f93e80f9f4a1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)ModelViewMatrix:模型矩阵ProjectionMatrix:投影矩阵
三、纹理坐标系
通过上一节，我们知道，纹理是图片，视频等的一种渲染方式，图片只有通过纹理才能加载到GLES中。因此纹理坐标系是指图片，视频等在手机屏幕上的坐标系，即U,V也有叫ST。该坐标系是一种假想坐标系，并不真正存在的，只是变换矩阵的结果。下面就统一叫UV坐标系。当应用初始化时，UV坐标系与三维坐标系（世界坐标系）重合。我们要注意的是，在OpenGL绘制过程中，它是可以选择绘制模式的，比如：点，线，面，且都是坐标集合里面进行顺序绘制的。i.e.:
```
private final float[] mTriangleVerticesData = { // X, Y, Z, -1.0f, -0.5f, 0, //1 1.0f, -0.5f, 0, //2 0.0f, 1.0f, 0, //3 };
```

当我们以纹理的形式加载一个图片到OpenGL中时，如何让它显示在世界坐标系中呢？这时就用到了纹理贴图的方式（即根据在世界坐标系中绘制顶点的先后顺序，把UV坐标系中的坐标与其一一对应），画图更加直接（[从网上copy的图，在此感谢](http://blog.csdn.net/u011153817/article/details/%E5%AF%B9AndroidopenglES%E4%B8%96%E7%95%8C%E5%9D%90%E6%A0%87%E7%B3%BB%E5%92%8C%E7%BA%B9%E7%90%86%E5%9D%90%E6%A0%87%E7%B3%BB%E7%9A%84%E7%90%86%E8%A7%A3)）:
![](http://upload-images.jianshu.io/upload_images/977602-79b16ee6cf6562cb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![倒立的图片](http://upload-images.jianshu.io/upload_images/977602-bda39e6d15b9db95?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
四、物体坐标系
物体坐标系是以物体的某一个点为原点来建立的三维坐标系（世界坐标系）。仅针对该物体。物体放到场景中时，各部分经历的坐标变换矩阵相同，相对位置不变，所以可视为一个整体
五、眼坐标系或相机坐标系
以视点为原点，以**视线的方向**为Z+轴正方向的坐标系中的方向。OpenGL管道会将**世界坐标**先变换到**眼坐标**，然后进行**裁剪**，只有在视线范围(视见体)之内的场景才会进入下一阶段的计算。
六、裁剪坐标系
由眼坐标可知，OpenGL管道首先会将目标从世界坐标变换到眼坐标，然后对视线范围外的部分进行裁剪。裁剪过程中用到投影变换矩阵栈(ProjectionMatrix)，栈顶矩阵就是当前投影变换矩阵，负责将场景各坐标变换到眼坐标，由所得到的结果是裁剪后的场景部分，称为裁剪坐标
我们上面说到了ModelViewMatrix 与ProjectionMatrix两个矩阵栈，那矩阵栈是怎么切换的呢？用函数：**glMatrixMode(GL_MODELVIEWING或GL_PROJECTION)**;本命令执行后参数所指矩阵栈就成为当前矩阵栈，以后的矩阵栈操纵命令将作用于它。紧接着glMatrixMode()就是初始化矩阵，我们在上面也讲到，所有矩阵都为Identity,所以用方法**glLoadIdentity()**初始化矩阵。
[上一节](http://blog.csdn.net/u011153817/article/details/51891759)
参考资料
[OpenGL坐标系介绍](http://my.oschina.net/firemiles/blog/376382) [OpenGL中各种坐标系的理解](http://blog.csdn.net/meegomeego/article/details/8686816) [Android OpenGL20 世界坐标系,屏幕坐标系,纹理坐标系](http://blog.csdn.net/qq_31726827/article/details/51456498) [Android OpenGL 坐标系](http://blog.csdn.net/qq_31726827/article/details/51265186) [ 详解OpenGL的坐标系、投影和几何变换-矩阵压栈思想/矩阵列式存储](http://blog.csdn.net/blues1021/article/details/51329885) [OpenGL 矩阵变换（讲的太好了~！）](http://blog.csdn.net/lyx2007825/article/details/8792475)

原文地址：http://blog.csdn.net/u011153817/article/details/51899091

