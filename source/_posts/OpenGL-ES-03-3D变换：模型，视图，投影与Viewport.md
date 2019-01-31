---
title: '[OpenGL ES 03]3D变换：模型，视图，投影与Viewport'
date: 2017-06-29 11:21:38
categories:
tags:
---
前言
本来打算直接写教程 04 的，但是想到3D变换涉及的数学知识较多，往往是很多初学者的拦路虎（比如我自己）。<!--more -->再加上OpenGL ES 2.0 不再提供OpenGL ES 1.0中3D 变换相关的一些重量级函数，如 glMatrixMode(GL_PROJECTION);glMatrixMode(GL_MODELVIEW); glLoadMatrixf; glMultMatrix 等，这些函数在OpenGL ES 2.0 中均需要我们自己去实现。如果不对线性代数与几何知识作一些简单介绍，恐怕不少人难以理解文中的一些步骤为什么要那么做。因此今天这一篇文章将放弃原定计划，先来介绍一些3D 数学以及 3D 变换相关的知识。BTW，原定计划的代码示例已经写好了，有兴趣的同学可以先行浏览，代码放在这里，运行效果如下：
![OpenGL ES 04 示例](http://upload-images.jianshu.io/upload_images/977602-fbd8c9f7da2017ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
一，3D数学历史
我们都学过几何学，应该都知道欧几里得（公元前3世纪希腊数学家）这位几何学鼻祖，正是这位大牛创建了欧几里得几何学，他提出了基于X，Y，Z 三轴的三维空间概念。到了17世纪，又出了位大牛笛卡尔，我们通常所说的笛卡尔坐标就是他的创造，笛卡尔坐标非常完美地将欧几里得几何学理论与代数学联系到一块。正是因为有了笛卡尔坐标，我们才能够用简单的**矩阵（Matrix）**来表示三维变换。但用矩阵来表示三维变换操作有一个无法解决的问题-**万向节锁** 。什么是万向节锁呢？简单地说就是两个轴旋转到同一个方向上去了，这两个轴平行了，因此就比原来少了一维（详情可参考这里）。过了一百多年，汉密尔顿（SirWilliam Rowan Hamilton）创建了**四元数（quaternion）**解决了因为旋转而导致万向节锁的问题，然后四元数还有其他用处，但在3D数学里主要是用来处理旋转问题。
好吧，或许你看得一头雾水，不要紧，你只要知道：**用矩阵来表示3D变换，但矩阵在表示旋转时可能会导致万向节锁的问题，而使用四元数可以避免万向节锁**就可以了。
 
二，矩阵变换
在前面提到可使用 Matrix来表示三维变换操作，那么变换又是如何通过 Matrix 实现的呢？下面就来讲这个。在这里我推荐一本3D数学入门书籍：《[3D数学基础：图形与游戏开发](http://book.douban.com/subject/1400419/)》
通常我们使用 4 维向量 (x, y, z,w) 表示在3D空间中的一个点，最后一维 w 表示齐次坐标。齐次坐标的含义是两条平行线在投影平面的无穷远处相交于一点，但在Matrix中没有表示无穷大，所以增加了齐次坐标这一维。你可以想象下，火车轨道的两条边在无限远处看起来就相交于一点，齐次坐标详细的介绍可以参考[这篇文章](http://www.cnblogs.com/kesalin/archive/2009/09/09/homogeneous.html)。
![齐次坐标](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757351823.jpg)
 
矩阵运算规则：
1) 若矩阵 A 和 B不是互[逆](http://zh.wikipedia.org/wiki/%E9%80%86%E7%9F%A9%E9%98%B5)矩阵，则不满足乘法交换律，即A × B 不等于 B × A； 2) M × N 阶的矩阵只能和 N × O 阶的矩阵相乘，即 N 的阶数相等，结果为 M × O阶的矩阵；  3) 矩阵 A × B 的运算过程是 A 的每一行依次乘以 B的每一列作为结果矩阵中的一行；  4) 矩阵 A 的[逆矩阵](http://zh.wikipedia.org/wiki/%E9%80%86%E7%9F%A9%E9%98%B5) B满足 A × B = B × A =单位矩阵。  5) 单位矩阵是对角线上的值为1，其余均为 0的矩阵。单位矩阵不影响坐标变换（你可以将下面的3D变换矩阵换成单位矩阵来思考下）。
3D空间的物体投影到2D平面上时，就需要使用到齐次坐标，因此我们需要使用 4 × 4 的 Matrix来表示变换。在编程语言中，这样的 Matrix 可用大小为 16 的一维数组或4 × 4的二维数组来表示。由于矩阵乘法不满足乘法交换律，用数组表示 Matrix又分为两种形式：行主序和列主序，它们在本质上是等价的，只不过是一个是右乘（行主序，矩阵放右边）和一个是左乘（列主序，矩阵放左边）。**OpenGL使用列主序矩阵，即列矩阵**，**因此我们总是倒过来算的（左乘矩阵，变换效果是按从右向左的顺序进行）：投影矩阵 × 视图矩阵 × 模型矩阵 × 3D位置。**
4×4列矩阵的数组表示：数字表示数组下标对应的行列位置：
![列矩阵](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757354855.png)
那么
**平移矩阵**可表示为：
![transition_matrix](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757359838.png)
平移矩阵 × 列矩阵(a, b, c, 1)= 列矩阵(a + x, b + y, c + z, 1)。
**缩放矩阵**可表示为：
![scale_matrix](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757359282.png) 
缩放矩阵 × 列矩阵(a, b, c, 1)= 列矩阵(a × sx, b × sy, c × sz, 1)。
**绕 X 轴旋转的旋转矩阵**可表示为：
![rotate_x](http://upload-images.jianshu.io/upload_images/977602-17ab45b2b027e74a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
绕 X 轴旋转的旋转矩阵 × 列矩阵(a,b, c, 1) = 列矩阵(a, b × cos(θ) - c × sin(θ), b × -sin(θ) + c ×cos(θ), 1)。
**绕 Y 轴旋转的旋转矩阵**可表示为：
![rotate_y](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757366741.png)
绕 Y 轴旋转的旋转矩阵 × 列矩阵(a,b, c, 1) = 列矩阵(a × cos(θ) - c × sin(θ), b , a × -sin(θ) + c ×cos(θ), 1)。
**绕 Z 轴旋转的旋转矩阵**可表示为：
![rotate_z](http://upload-images.jianshu.io/upload_images/977602-1db83b8f67e8a2c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
绕 Z 轴旋转的旋转矩阵 × 列矩阵(a,b, c, 1) = 列矩阵(a × cos(θ) - b × sin(θ), a ×-sin(θ) + b × cos(θ), c, 1)。
 
三，OpenGL 中的实现
**OpenGL使用右手规则进行旋转**，因此逆时针方向的选择是正角度的，而顺时针方向的旋转是负角度的。还记得中学学物理时候的右手规则么？忘记了的话，看下图：
![OpenGL 使用右手规则进行旋转](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757366675.jpg) 
 
**注意：**
前面说到矩阵乘法不满足乘法交换律，因此你对一个3D坐标先进行旋转，然后进行平移（平移矩阵 × 旋转矩阵 ×3D坐标）；与先进行平移，然后进行旋转（旋转矩阵 × 平移矩阵 × 3D坐标）得到的效果是大为迥异的。如下图所示：
![变换次序影响变换结果](http://images.cnblogs.com/cnblogs_com/kesalin/201212/201212041757378593.jpg)
在第一种情况下，我们通常称旋转是在 localspace 中进行，因为它是绕着物体自己的中心点进行的，而在后一种情况下的旋转通常称为是在 world space中进行的。我们知道点是可以在坐标空间之间相互转换的，这是一个很重要的概念。**OpenGL 中物体最初是在本地坐标空间中，然后转换到世界坐标空间，再到camera 视图空间，再到投影空间，这一系列转换都是靠 matrix 计算来实现。**
上面的这个过程在 OpenGL 及OpenGL ES 1.0 中，对应的代码类似于：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-edeb59a8d0bba486.gif?imageMogr2/auto-orient/strip)

```
glViewport (0, 0, (GLsizei) w, (GLsizei) h); 　　a)
glMatrixMode (GL_PROJECTION);　　　　　　　   b)
glLoadIdentity (); glFrustum (-1.0, 1.0, -1.0, 1.0, 1.5, 20.0);　　 c) 
glMatrixMode (GL_MODELVIEW);　　　　　　　　　　　　 d) 
glClear (GL_COLOR_BUFFER_BIT); 
glColor3f (1.0, 1.0, 1.0); 
glLoadIdentity (); 
gluLookAt (0.0, 0.0, 5.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0);　　　　e) 
glScalef (1.0, 2.0, 1.0); 　f) 
glutWireCube (1.0);　　　　　　　　　　　　　　　　　　　　　　　　　 g) 
glFlush ();
```
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-edeb59a8d0bba486.gif?imageMogr2/auto-orient/strip)

说明：
a)是用于viewport（视口）变换，viewport 变换发生在投影到2D投影平面之后，该变换是将投影之后**归一化**的点映射到屏幕上一块区域内的坐标。视口变换的目的是指定投影之后图像在屏幕上显示的区域。如下示意图所示：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-e1a5711e74e434b4.gif?imageMogr2/auto-orient/strip)
视口变换 glViewport(x, y,width, height); x,y是投影平面描绘在屏幕或窗口上的起始位置（注意屏幕坐标以左上方为原点），width和height是以像素为单位，指投影平面在屏幕上描绘的区域大小。如果投影平面的宽高比，与width/height比不相同（如上面的右图），那么描绘的场景就会扭曲。
从裁剪到屏幕的整个过程如下图所示，w就是前面提到的齐次坐标那一维，从 Clip Space 到 Normalized Device Space 就是投影规范化的过程，从Normalized Device Space 到 Window Space 就是 viewport 变换过程。
![投影，视口变换过程](http://upload-images.jianshu.io/upload_images/977602-60063623f569d214.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
该转换内部计算公式为：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-a17841541ec85dfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 (xw,yw)是屏幕坐标，(x, y, width, height)是传入的参数，(xnd, ynd)是投影之后经归一化之后的点（上图中Normalized Device Space 空间的点）。因此 viewport变换就是将投影之后归一化的点转换为真正可用于在屏幕上进行渲染的屏幕坐标；
b) 是说明下面的 matrix是用于投影变换的，在本例中，是通过语句c) glFrustum 来设置透视投影变换的。投影变换有两种：正交投影和透视投影，后面会有详细介绍；
d) 是说明下面的 matrix是用于模型视图变换，注意，OpenGL 和 OpenGL ES都将模型变换与视图变换结合在一起，而不是分开为两个，这是因为模型变换等价于视图变换的逆变换。视图变换是将物体转换到观察者（一般称之为camera）的视线空间中。你可以想象一下，照相时，你可以：A）照相机不懂，旋转自己的头找个侧面像，也可以B）自己不动，照相机旋转一定的角度来达到同样的效果。下面的两幅图分别描述了情形A）和情形B）：
情形A）：旋转物体，相机不动
![情形A）：旋转物体，相机不动](http://upload-images.jianshu.io/upload_images/977602-432ec61d8640a76f.gif?imageMogr2/auto-orient/strip)
情形B）：旋转相机，物体不动
![情形B）：旋转相机，物体不动](http://upload-images.jianshu.io/upload_images/977602-d1892d5b65988d66.gif?imageMogr2/auto-orient/strip)
在 OpenGL中，我们在设置场景（scene）的时候通常是采取情形B）的做法，因此在语句 e)处，我们设置相机的位置和朝向，来设定视图变换，之后的语句 f) glScale 是设定在模型变换的，最后语句 g)在本地空间描绘物体。
**注意**
**写 OpenGL 代码时从前到后的顺序依次是：设定viewport（视口变换），设定投影变换，设定视图变换，设定模型变换，在本地坐标空间描绘物体。**而在前面为了便于理解做介绍时，说的顺序是**OpenGL中物体最初是在本地坐标空间中，然后转换到世界坐标空间，再到 camera视图空间，再到投影空间。**由于模型变换包括了本地空间变换到世界坐标空间，所以我们**理解3D变换是一个顺序，而真正写代码时则是以相反的顺序进行的，如果从左乘**矩阵**这点上去理解就很容易明白为什么会是反序的。**
有了上面 3D变换的整体概念，下面来详细说说投影变换与视图变换。
 
四，投影变换
投影变换的目的是确定 3D空间的物体如何投影到 2D 平面上，从而形成2D图像，这些 2D图像再经视口变换就被渲染到屏幕上。前面提到投影变换有两种：正交投影和透视投影。透视投影用的比较广泛，它与真实世界更相近：近处的物体看起来要比远处的物体大；而正交投影没有这个效果，正交投影通常用于CAD或建筑设计。下面是正交投影与透视投影效果示意图：
正交投影
透视投影

![正交投影](http://upload-images.jianshu.io/upload_images/977602-075e391370b8f370.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-bdee61fa84859e72.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 
    
 
 
 
 
透视投影可以通过两种方式来表述，OpenGL及 OpenGL ES 1.0 提供其中一种： glFrustum，而 glut辅助库提供了另外一种：gluPerspective。它们本质上是相同的，只不过是不同的表述而已：
**视锥体/视景体**：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-11a3f410a83e6b85.gif?imageMogr2/auto-orient/strip)
glFrustum(left, right, bottom, top, zNear, zFar);
left，right， bootom，top定义了 near 裁剪面大小，而 zNear 和 zFar 定义了从 Camera/Viewer到远近两个裁剪面的距离（注意这两个距离都是正值）。由这六个参数可以定义出六个裁剪面构成的锥体，这个锥体通常被称之为视锥体或视景体。只有在这个锥体内的物体才是可以见的，不在这个锥体内的物体就相当于不再视线范围内，因而会被裁减掉，OpenGL不会这些物体进行渲染。
由于 OpenGL ES 2.0不提供此函数，因此我们需要自己实现该函数。其计算公式如下：
假设：l = left, r = right,b = bottom, t = top, n = zNear, f = zFar，有
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-011b89b5743682a4.gif?imageMogr2/auto-orient/strip)
**透视图**：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-fed81a615706fc1b.gif?imageMogr2/auto-orient/strip)
gluPerspective(fovy,aspect, zNear, zFar);
fovy 定义了 camera 在 y方向上的视线角度（介于 0 ~ 180 之间），aspect 定义了近裁剪面的宽高比 aspect = w/h，而 zNear 和zFar 定义了从 Camera/Viewer到远近两个裁剪面的距离（注意这两个距离都是正值）。这四个参数同样也定义了一个视锥体。
在 OpenGL ES 2.0中，我们也需要自己实现该函数。我们可以通过三角公式 tan(fovy/2)  = (h /2)/zNear 计算出 h ，然后再根据 w =  h * aspect 计算出w，这样就可以得到 left, right, top, bottom, zNear, zFar六个参数，代入在介绍视锥体时提到的公式即可。 
正交投影在 OpenGL 及 OpenGLES 1.0 中是由 glOrtho来提供的，我们可以把正交投影看成是透视投影的特殊形式：即近裁剪面与远裁剪面除了Z位置外完全相同，因此物体始终保持一致的大小，即便是在远处看上去也不会变小。
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-ab8a58d93d4587a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
glOrtho(*left*, *right*, *bottom*, *top*, *zNear*, *zFar*);
left，right， bootom，top定义了 near 裁剪面大小，而 zNear 和 zFar 定义了从 Camera/Viewer到远近两个裁剪面的距离（注意这两个距离都是正值）。
假设：xmax = right, xmin =left, ymax = top, ymin = bottom, zmax = far, zmin =near，正交投影的计算可分为两步：首先平移到视锥体的中心，然后缩放。
平移矩阵：（图中的2min 应为zmin）
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-23bae60285311813.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
缩放矩阵：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-a6fb48b04c14658f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
正交投影矩阵 R = S× T：
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-aee4ce370ed75eec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
五，视图变换
视图变换的目的是为了让我们能观察到某个角度的场景（从观察者的角度来说）或者说是为了将物体从世界坐标转换到相机视线所在视图空间中来（从3D物体角度来说）。这可以通过设定观察者的位置和朝向来实现的或对物体进行3D变换来实现，通常前面一种方式来实现（即设定观察者的位置与朝向）。如下图所示，xyz坐标轴表示的是世界坐标，蓝白色区域为视图空间，视图变换就是要将长方体从世界空间中转换到视图空间的坐标体系中去，然后再投影规范化，然后再经viewport 转换映射到屏幕上渲染出来。
![视图变换](http://upload-images.jianshu.io/upload_images/977602-e2704e8bdc2eadc6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在 OpenGL 中，我们可以通过工具库提供的gluLookAt 这个函数来实现此功能。该函数的原型为：
gluLookAt(eyex, eyey,eyez, centerx, centery, centerz, upx, upy, upz);
eye 表示 camera/viewer的位置， center 表示相机或眼睛的焦点（它与 eye 共同来决定 eye 的朝向），而 up 表示 eye 的正上方向，注意up 只表示方向，与大小无关。通过调用此函数，就能够设定观察的场景，在这个场景中的物体就会被 OpenGL处理。**在 OpenGL 中，eye的默认位置是在原点，指向 Z 轴的负方向（屏幕往里），up 方向为 Y 轴的正方向。**在接下来的教程 04中，使用的就是这个默认设置。
OpenGL ES 2.0也没有提供该函数，glulookat的内部实现其实就是先旋转到与观察者视线相同的方向，然后再平移到观察者所在的位置。其实现伪码如下：
Matrix4 GetLookAtMatrix(Vector3 eye, Vector3 at, Vector3up){

    
Vector3forward, side;

    
forward= at - eye;

    
normalize(forward);

    
side= cross(forward, up);

    
normalize(side);

    
up= cross(side, forward);

 
    
Matrix4res = Matrix4(

                          
side.x,up.x, -forward.x, 0,

                          
side.y,up.y, -forward.y, 0,

                          
side.z,up.z, -forward.z, 0,

                          
0,0, 0, 1);

    
translate(res,Vector3(0 - eye));

    
return
 res;

}

上面代码中的 cross是叉积，normalize 是规范化，Matrix4 是列主序，translate 是平移。
 
六，后记
3D变换是对初学者来说是比较困难的，我尽量写得明白点，但效果如何就不得而知了。写这一篇花了我不少时间，但对四元数和万向节锁也只是提及而已，未详细介绍，以后再单独介绍吧。NateRobin 写了一个3D变换的可视化教程工具，对于理解投影，视图，模型变换非常有帮助，强烈建议下载运行该程序，并调整相关参数看看效果。下面传张截图以诱惑你去下载：[点此进入下载页面](http://user.xmission.com/%7Enate/tutors.html)（Windows和 Mac 版本都有）
![[OpenGL <wbr>ES <wbr>03]3D变换：模型，视图，投影与Viewport](http://upload-images.jianshu.io/upload_images/977602-3a1260eaafb138a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
七，引用
1，《[OpenGL编程指南](http://book.douban.com/subject/4311129/)》
2，《[3D数学基础：图形与游戏开发](http://book.douban.com/subject/1400419/)》
3，http://cse.csusb.edu/tong/courses/cs420/notes/viewing2.php
4，http://www.mesa3d.org/
5，http://db-in.com/blog/2011/04/cameras-on-opengl-es-2-x/

原文地址：http://blog.sina.com.cn/s/blog_a974a8b60102w9fy.html

