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