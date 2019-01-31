---
title: 顶点缓存对象（VBO）
date: 2017-07-14 10:03:23
categories:
tags:
---
**GL_ARB_vertex_buffer_object**扩展致力于提供<!-- more -->[顶点数组](http://www.cnblogs.com/hefee/p/3822844.html)与[显示列表](http://www.cnblogs.com/hefee/p/3824298.html)的优势来提升OpenGL效率，同时避免它们实现上的不足。顶点缓存对象（VBO）准许顶点数组数据存放在服务端的高性能显卡内存中，且提供高效数据传输。如果缓存对象用于保存像素数据，就被称为[像素缓存对象（PBO）](http://www.cnblogs.com/hefee/p/3824303.html)。

使用顶点数组可以降低函数调用次数与降低共享顶点的重复使用。然而，顶点数组的不足之处是顶点数组函数处在客户端状态中，且每次引用都须向服务端重新发送数据。

此外，显示列表为服务端函数，因此，它并不受限于数据传输的开销。不过，一旦显示列表编译完成，显示列表中的数据不能够修改。

顶点缓存对象（VBO）在服务器端高性能内存中为顶点属性创建“缓存对象”，并且提供引用这些数组的访问函数，这些函数与顶点数组中使用的函数相同，如**glVertexPointer**()、**glNormalPointer**()、**glTexCoordPointer**()等。

顶点缓存对象中的内存管理根据用户提示（“target”与“Usage”模式）将缓存对象放置在最合适内存位置。因此，内存管理能够通过在系统、AGP与显卡内存三种内存之间做出平衡的方式优化缓存。

与显示列表不同，可以通过映射缓存到客户端内存空间的方式读取与更新顶点缓存对象中的数据。

VBO的另一个重要优势是就像显示列表与纹理那样可以在多个客户端共享缓存数据。因为VBO处在服务器端，多个客户端可以通过相应的标示符访问同一个缓存。
____
创建VBO
创建VBO需要3个步骤：
使用glGenBuffers()生成新缓存对象。
使用glBindBuffer()绑定缓存对象。
使用glBufferData()将顶点数据拷贝到缓存对象中。
___
glGenBuffers()
glGenBuffers()创建缓存对象并且返回缓存对象的标示符。它需要2个参数：第一个为需要创建的缓存数量，第二个为用于存储单一ID或多个ID的GLuint变量或数组的地址。
```
void glGenBuffers(GLsizei n, GLuint* buffers)
```
___
glBindBuffer()
当缓存对象创建之后，在使用缓存对象之前，我们需要将缓存对象连接到相应的缓存上。glBindBuffer()有2个参数：target与buffer。
```
void glBindBuffer(GLenum target, GLuint buffer)
```
target告诉VBO该缓存对象将保存顶点数组数据还是索引数组数据：GL_ARRAY_BUFFER或GL_ELEMENT_ARRAY。任何顶点属性，如顶点坐标、纹理坐标、法线与颜色分量数组都使用GL_ARRAY_BUFFER。用于glDraw[Range]Elements()的索引数据需要使用GL_ELEMENT_ARRAY绑定。注意，target标志帮助VBO确定缓存对象最有效的位置，如有些系统将索引保存AGP或系统内存中，将顶点保存在显卡内存中。

当第一次调用glBindBuffer()，VBO用0大小的内存缓存初始化该缓存，并且设置VBO的初始状态，如用途与访问属性。
___
glBufferData()
当缓存初始化之后，你可以使用glBufferData()将数据拷贝到缓存对象。
```
void glBufferData(GLenum target，GLsizeiptr size, 
const GLvoid*  data, GLenum usage);
```

第一个参数target可以为GL_ARRAY_BUFFER或GL_ELEMENT_ARRAY。size为待传递数据字节数量。第三个参数为源数据数组指针，如data为NULL，则VBO仅仅预留给定数据大小的内存空间。最后一个参数usage标志位VBO的另一个性能提示，它提供缓存对象将如何使用：static、dynamic或stream、与read、copy或draw。

VBO为usage标志指定9个枚举值：
```

GL_STATIC_DRAW

GL_STATIC_READ

GL_STATIC_COPY

GL_DYNAMIC_DRAW

GL_DYNAMIC_READ

GL_DYNAMIC_COPY

GL_STREAM_DRAW

GL_STREAM_READ

GL_STREAM_COPY
```

”static“表示VBO中的数据将不会被改动（一次指定多次使用），”dynamic“表示数据将会被频繁改动（反复指定与使用），”stream“表示每帧数据都要改变（一次指定一次使用）。”draw“表示数据将被发送到GPU以待绘制（应用程序到GL），”read“表示数据将被客户端程序读取（GL到应用程序），”copy“表示数据可用于绘制与读取（GL到GL）。

注意，仅仅draw标志对VBO有用，copy与read标志对顶点/帧缓存对象（[PBO](http://www.cnblogs.com/hefee/p/3824303.html)或[FBO](http://www.cnblogs.com/hefee/p/3824309.html)）更有意义，如GL_STATIC_DRAW与GL_STREAM_DRAW使用显卡内存，GL_DYNAMIC使用AGP内存。_READ_相关缓存更适合在系统内存或AGP内存，因为这样数据更易访问。
___
glBufferSubData()
```
void glBufferSubData(GLenum target, GLintptr offset, GLsizeiptr size, const GLvoid* data);
```
与glBufferData()类似，glBufferSubData()用于向VBO中拷贝数据，不过它仅仅将从给定offset开始的一定范围的数据替换到现存缓存中。（在使用glBufferSubData()之前，整个缓存必须由glBufferData()指定。）
___
glDeleteBuffers()
```
void glDrawBuffers(GLsizei n, const GLenum* bufs);
```
在VBO不再使用时，你可以使用glDeleteBuffers()删除一个VBO或多个VBO。在混存对象删除之后，它的内容将丢失。
下列代码是为顶点坐标创建单一VBO的实例。注意，在你拷贝数据到VBO之后，你可以应用程序中为顶点数组分配的内存。
```
GLuint vboId;                              // VBO标示符
GLfloat* vertices = new GLfloat[vCount*3]; // 创建顶点数组
...
// 创建新的VBO并获取相关标示符
glGenBuffers(1, &vboId);
// 绑定VBO以待使用
glBindBuffer(GL_ARRAY_BUFFER_ARB, vboId);
// 更新数据到VBO
glBufferData(GL_ARRAY_BUFFER_ARB, dataSize, vertices, GL_STATIC_DRAW_ARB);
// 在拷贝数据到VBO之后，可以安全删除
delete [] vertices;
...
// 程序终止时删除VBO
glDeleteBuffers(1, &vboId);
```
绘制VBO
由于VBO基于现有的顶点数组实现之上，渲染VBO与使用[顶点数组](http://www.cnblogs.com/hefee/p/3822844.html)渲染几乎一致。仅有的不同是顶点数组指针现在作为当前绑定缓存对象的偏移值。因此，绘制VBO时除了glBindBuffer()之外不需别的API。
```

// 为顶点数组与索引数组绑定VBO

glBindBuffer(GL_ARRAY_BUFFER_ARB, vboId1);         
// 顶点坐标

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER_ARB, vboId2); 
// 索引坐标

 
// 除了指针都与顶点数组一致

glEnableClientState(GL_VERTEX_ARRAY);             
// 开启顶点坐标数组

glVertexPointer(3, GL_FLOAT, 0, 0);               
// 最后一个参数为offset，而非ptr

 
// 使用索引数组偏移绘制6个四边形

glDrawElements(GL_QUADS, 24, GL_UNSIGNED_BYTE, 0);

 
glDisableClientState(GL_VERTEX_ARRAY);            
// 禁用顶点数组

 
// 用0绑定，因此，切换到标准指针操作

glBindBuffer(GL_ARRAY_BUFFER_ARB, 0);

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER_ARB, 0);
```
使用0绑定缓存对象将关闭VBO操作。在使用完VBO之后，最好将之关闭，因此具有绝对指针的顶点数组操作将重新开启。
___
更新VBO
 VBO相对于[显示列表](http://www.cnblogs.com/hefee/p/3824298.html)的优势在于客户端可以读取与编辑缓存对象数据，而显示列表不能这样做。更新VBO的最简便方法是使用glBufferData()或glBufferSubData()将新数据重新拷贝到绑定的VBO中。这种情况下，你的应用程序需要始终拥有一个有效的顶点数组。也就是说，你必须始终拥有2份顶点数据：一份在应用程序中，另一份在VBO中。

修改缓存对象的另一个方式是将缓存对象映射到客户端内存，客户端可以使用映射缓存的指针更新数据。下文描述如何将VBO映射到客户端内存以及如何访问映射数据。
___
glMapBuffer()
VBO提供glMapBuffer()以将缓存对象绑定到客户端内存。
```
void * glMapBuffer(GLenum target, GLenum access);
```
如果OpenGL能够将缓存对象映射到客户端地址空间，glMapBuffer()返回指向缓存的指针。否则它返回NULL。
第一个参数target早在glBindBuffer()中涉及，第二个参数access标志指定怎样使用映射数据：读取、改写或两者都。
```
GL_READ_ONLY

GL_WRITE_ONLY

GL_READ_WRITE
```
注意，glMapBuffer()引起同步问题。如果GPU任然工作于该缓存对象，glMapBuffer()将一直等待直到GPU结束对应缓存对象上的工作。
为了避免等待，你可以首先使用NULL调用glBufferData()，然后再调用glMapBuffer()。这样，前一个数据将被丢弃且glMapBuffer()立即返回一个新分配的指针，即使GPU任然工作于前一个数据。
然而，由于你丢弃了前一个数据，这种方法只有在你想更新整个数据集的时候才有效。如果你仅仅希望更改部分数据或读取数据，你最好不要释放先前的数据。
___
 glUnmapBuffer()
```
GLboolean glUnmapBuffer(GLenum target)
```
在完成VBO数据的修改之后，必须将缓存对象从客户端内存解除映射。如果成功，glUnmapBuffer()返回GL_TRUE。如返回GL_FALSE，绑定之后的VBO缓存内容是坏的。腐坏现象源自显示器分辨率的改变或窗口系统的特定事件。此种情况，数据必须重发。
下面是使用绑定方式改变VBO的实例代码。
```
// 绑定然后映射该VBO
glBindBuffer(GL_ARRAY_BUFFER_ARB, vboId);
float * ptr =(float*)glMapBuffer(GL_ARRAY_BUFFER_ARB, GL_WRITE_ONLY_ARB);

// 如果指针为空（映射后的），更新VBO
if(ptr)
{   
updateMyVBO(ptr, ...);                 // 修改缓存数据
glUnmapBufferARB(GL_ARRAY_BUFFER_ARB); // 使用之后解除映射
}
 
// 你可以绘制更新后的VBO
...
```
实例
 ![](http://upload-images.jianshu.io/upload_images/977602-8eb9f8cad4b281eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
该实例程序沿法线防线创建VBO抖动。它映射VBO并且使用指向映射缓存的指针每帧更新一次顶点数据。你可以与传统顶点数组实现方式继进行性能对比。
它使用2个顶点缓存：一个为了顶点坐标与法向量，另一个仅仅保存索引数组。
下载源文件与二进制文件：[vbo.zip](http://www.smallkingworld.com/others/vbo.zip)，[vboSimple.zip](http://www.smallkingworld.com/others/vboSimple.zip)。
 vboSimple是使用VBO与顶点数组绘制立方体的简单例子。你可以很容易看出VBO与VA的相同点与不同点。

原文：http://www.cnblogs.com/hefee/p/3824300.html

英文原文：[http://www.songho.ca/opengl/gl_vbo.html](http://www.songho.ca/opengl/gl_vbo.html)


