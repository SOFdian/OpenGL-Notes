### GLSL的内建变量

#### `gl_Position`

顶点着色器的裁剪空间输出位置向量，用于在屏幕上显示图像，是必须的且是这个变量的唯一功能

#### `gl_PointSize `

首先改成点渲染模式(`GL_POINTS`)：

![image-20250520115148606](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115148606.png)

启用点大小：

![image-20250520115239230](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115239230.png)

顶点着色器中直接根据z设置点大小，片段着色器直接设置点为白色

![image-20250520115254083](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115254083.png)

![image-20250520115304150](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115304150.png)

近小远大（这里有个天空盒所以看不出来是近是远）：

![image-20250520115357123](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115357123.png)

![image-20250520115407525](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520115407525.png)

#### `gl_VertexID`

是只读的

整型变量`gl_VertexID`储存了正在绘制顶点的当前ID。当（使用`glDrawElements`）进行索引渲染的时候，这个变量会存储正在绘制顶点的当前索引。当（使用`glDrawArrays`）不使用索引进行绘制的时候，这个变量会储存从渲染调用开始的已处理顶点数量。

#### `gl_FragCoord`

`gl_FragCoord`的x和y分量是片段的窗口空间(Window-space)坐标，其原点为窗口的左下角

`gl_FragCoord`的z分量等于对应片段的深度值

改成这样：

![image-20250520120249745](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520120249745.png)

当一个像素的x坐标小于400时为红色

![image-20250520120230987](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520120230987.png)

#### `gl_FrontFacing`

`gl_FrontFacing`可以表明当前片段属于正向面的一部分还是背向面的一部分

![image-20250520145110291](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520145110291.png)

通过这个变量，可以实现同一个面的内外纹理不同

![image-20250520145212002](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520145212002.png)

#### `gl_FragDepth`

用于在着色器内设置片段的深度值

只要我们在片段着色器中对`gl_FragDepth`进行写入，OpenGL就会禁用所有的提前深度测试(Early Depth Testing)。它被禁用的原因是，OpenGL无法在片段着色器运行**之前**得知片段将拥有的深度值，因为片段着色器可能会完全修改这个深度值。

![image-20250520145525004](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250520145525004.png)

### 接口块

块名为`VS_OUT`，在顶点着色器和片段着色器中需要是一样的。只要两个接口块的名字一样，它们对应的输入和输出将会匹配起来。

`vs_out`和`fs_in`是实例名，这个可以不一样，随便起名。

![image-20250521140550864](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521140550864.png)

![image-20250521140622460](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521140622460.png)

### Uniform块

`Uniform`块

![image-20250521142112927](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521142112927.png)

偏移的规则大概是这样的（每4个字节将会用一个`N`来表示）：

![image-20250521142130413](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521142130413.png)

### Uniform缓冲对象

问题：当使用多于一个的着色器时，尽管大部分的uniform变量都是相同的，我们还是需要不断地设置它们（例如投影矩阵，摄像机位置这些变量）。

首先声明一个`Uniform`块

![image-20250521144825099](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521144825099.png)

然后创建`Uniform`缓冲对象，之后可以通过`glBufferSubData`来更新这个缓冲块中任意区域的内容了：

![image-20250521144513664](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521144513664.png)

现在仍然有一个问题，就是如何将`uboMatrices`对应到`Matrices`这个缓冲块

在`OpenGL`中，有很多绑定点：

![image-20250521144900079](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521144900079.png)

![image-20250521144917398](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521144917398.png)

前两行代码首先获取了着色器中已定义`Uniform`块的位置值索引

后两行代码将`Matrices`这个缓冲块链接到绑定点0

现在只需要将`uboMatrices`也链接到绑定点0就可以了：

![image-20250521145135250](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521145135250.png)

![image-20250521145158825](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521145158825.png)

然后就可以设置`view`和`projection`的值了：

![image-20250521145812214](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521145812214.png)

![image-20250521145826121](C:\Users\SOF\Desktop\OpenGL笔记\assets\image-20250521145826121.png)

像`projection`这种不变的，可以在渲染循环外设置

### 好处

第一，一次设置很多uniform会比一个一个设置多个uniform要快很多。

第二，比起在多个着色器中修改同样的uniform，在Uniform缓冲中修改一次会更容易一些。

第三，如果使用Uniform缓冲对象的话，你可以在着色器中使用更多的uniform。OpenGL限制了它能够处理的uniform数量，这可以通过GL_MAX_VERTEX_UNIFORM_COMPONENTS来查询。当使用Uniform缓冲对象时，最大的数量会更高。所以，当你达到了uniform的最大数量时（比如再做骨骼动画(Skeletal Animation)的时候），你总是可以选择使用Uniform缓冲对象。