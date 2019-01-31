---
title: '【转.译】关于万向节死锁(Gimbal Lock) '
date: 2017-06-29 11:27:39
categories:
tags:
---
在[http://blog.donews.com/wanderpoet/archive/2005/07/04/453608.aspx](http://blog.donews.com/wanderpoet/archive/2005/07/04/453608.aspx)看到一篇关于Gimbal Lock的E文，解释得挺清楚的，<!-- more -->翻译如下：Gimbal Lock
What's Gimbal Lock?

Gimbal lock is the phenomenon of two rotational axis of an object pointing in the same direction. Actually, if two axis of the object become aligned, then we say that there's a gimbal lock. In other words, a rotation in one axis could 'override' a rotation in another, making you lose a degree of freedom.

万向节锁是什么

万象节锁是指物体的两个旋转轴指向同一个方向。实际上，当两个旋转轴平行时，我们就说万向节锁现象发生了，换句话说，绕一个轴旋转可能会覆盖住另一个轴的旋转，从而失去一维自由度

How Gimbal Lock occurred?

Generally speaking, it occurred when you rotate the object which only use Eular Angles to denote it. The reason for this is that Eular angles evaluate each axis independently in a set order. Let's see a certain scene. First the object travels down the X axis. When that operation is complete it then travels down the Y axis, and finally the Z axis. The problem with gimbal lock occurs when you rotate the object down the Y axis, say 90 degrees. Since the X component has already been evaluated it doesn't get carried along with the other two axis. What winds up happening is the X and Z axis get pointed down the same axis.

通常说来，万向节锁发生在使用Eular Angles（欧拉角）的旋转操作中，原因是Eular Angles按照一定的顺序依次独立地绕轴旋转。让我们想象一个具体的旋转场景，首先物体先绕转X轴旋转，然后再绕Y轴，最后绕Z轴选择，从而完成一个旋转操作（飘飘白云译注：实际是想绕某一个轴旋转，然而Eular Angle将这个旋转分成三个独立的步骤进行），当你绕Y轴旋转90度之后万向节锁的问题就出现了，因为X轴已经被求值了，它不再随同其他两个轴旋转，这样X轴与Z轴就指向同一个方向（它们相当于同一个轴了）。

Here's a pic showing what happened: 
万向节锁现象图：

![](http://upload-images.jianshu.io/upload_images/977602-cd85148402a41e85.gif?imageMogr2/auto-orient/strip)

以上我添加的译文，下面是转贴的译文，原译者：[SoRoMan](http://www.cnblogs.com/soroman/)Maybe it's a bit difficult to understand. OK, let me show you a real sence.
可能有点不好理解。让我们看个现实中的场景。

Say that we have a telescope and a tripod to put the telescope on. The tripod is put on the ground. The top of the tripod holding the telescope is leveled with the horizon (reference plane) so that a vertical rotation axis (we call it X axis) is perfectly vertical to the ground plane. The telescope can then be rotated around 360 degrees in X axis so that it can scan the horizon in all the directions of the compass. Zero degrees azimuth is usually set toward a heading of true north. A second horizontal axis parallel to the ground plane (we call it Y axis), enables the telescope to be rotated in elevation upward or downward from the horizon. The horizon is usually set at zero degrees and the telescope can be rotated +90 degrees upward in elevation so that it is looking straight up toward the zenith or rotated -90 degrees downward so that it is looking vertically at the ground plane.

假如我们有一个望远镜和一个用来放望远镜的三脚架，（我们将）三脚架放在地面上，使支撑望远镜的三脚架的顶部是平行于地平面（参考平面）的，以便使得竖向的旋转轴（记为x轴）是完全地垂直于地平面的。现在，我们就可以将望远镜饶x轴旋转360度，从而观察（以望远镜为中心的）水平包围圈的所有方向。通常将正北朝向方位角度记为0度方位角。第二个坐标轴，即平行于地平面的横向的坐标轴（记为y轴）使得望远镜可以饶着它上下旋转，通常将地平面朝向的仰角记为0度，这样，望远镜可以向上仰+90度指向天顶，或者向下-90度指向脚底。

OK, that's all we needed. every point in the sky (and the ground) can be referenced by only ONE unique pair of X and Y readings. For example an X of 90 degrees and Y of 45 degrees specifies a point exactly due east of the telescope and in a skyward direction half way up toward the zenith.

好了，万事俱备。现在，天空中（包括地面上）的每个点只需要唯一的一对x和y度数就可以确定。比如x=90度,y=45度指向的点是位于正东方向的半天空上。

Now let me show you how the gimal lock occurred. We detect a high flying aircraft, near the horizon, due east from the telescope (X = 90 degrees, Y = 10 degrees) and we follow it (track it) as it comes directly toward us. The X angle stays at 90 degrees and the Y angle slowly increases. As the aircraft comes closer the Y angle increases more rapidly and just as the aircraft reaches an Y of 90 degrees (exactly overhead), it makes a sharp turn due south. We find that we cannot quickly move the telescope toward the south because the Y angle is exactly +90 degrees so we loose sight (loose track) of the aircraft . We have GIMBAL LOCK!

现在，看看万向节死锁是怎么发生的。一次，我们探测到有一个飞行器贴地飞行，位于望远镜的正东方向（x=90度，y=10度），朝着我们直飞过来，我们跟踪它。飞行器飞行方向是保持x轴角度90度不变，而y向的角度在慢慢增大。随着飞行器的临近，y轴角增长的越来越快且当y向的角度达到90度时（即将超越），突然它急转弯朝南飞去。这时，我们发现我们不能将望远镜朝向南方，因为此时y向已经是90度，造成我们失去跟踪目标。这就是万向节死锁！(译注：为什么说不能将望远镜朝向南方呢，让我们看看坐标变化，从开始的（x=90度，y=10度）到（x=90度，y=90度），这个过程没有问题，望远镜慢慢转动跟踪飞行器。当飞行器到达（x=90度，y=90度）后，坐标突然变成（x=180度，y=90度）（因为朝南），x由90突变成180度,所以望远镜需要饶垂直轴向x轴旋转180-90=90度以便追上飞行器,但此时，望远镜已经是平行于x轴，我们知道饶平行于自身的中轴线的的旋转改变不了朝向，就象拧螺丝一样，螺丝头的指向不变。所以望远镜的指向还是天顶。而后由于飞行器飞远，坐标变成（x=180度，y<90度）时，y向角减小，望远镜只能又转回到正东指向，望'器'兴叹。这说明用x,y旋转角（又称欧拉角）来定向物体有时并不能按照你想像的那样工作，象上面的例子中从（x=90度，y=10度）到（x=90度，y=90度），按照欧拉角旋转确实可以正确地定向，但从（x=90度，y=90度）到（x=180度，y=90度），再到（x=180度，y<90度）,按照欧拉角旋转后的定向并非正确。）

It's a example of 2D coordinate frame. It's very similar in 3D frame. We say that you have a vector which is parellel to the X axis. And we rotate it around  Y axis so that the vector is parellel to the Z axis. Then we find that any rotations around  Z axis will have no effect on the vector. We say that we have a GIMBAL LOCK

上面是2维坐标系中的例子，同样，对于3维的也一样。比如有一个平行于x轴的向量，我们先将它饶y旋转直到它平行于z轴，这时，我们会发现任何饶z的旋转都改变不了向量的方向，即万向节死锁。 

原文地址：http://blog.csdn.net/kesalin/article/details/2161254

