# 12.2 OpenGL和OpenGL ES简介

### OpengGL

OpenGL的全称是Open Graphics Library,即开放的图形库接口，它定义了一个跨编程语 言、跨平台的编程接口的规范，它主要用于三维图形（实际上二维图形也可以）编程。OpenGL 的前身是SGI公司为其图形工作站开发的IRIS GL。IRIS GL是一个工业标准的3D图形软件接口，功能虽然强大但是移植性不好，于是SGI公司便在IRIS GL的基础上幵发了 OpenGL。

OpenGL体系简单，而且具有跨平台的特性，它不像Direct3D(Microsoft开发的3D图 形库接口，OpenGL的最有力的竞争对手）只能在Windows系统上运行，因此OpenGL具有很广泛的适应性：它不仅适用于大型图形工作站，也适用于个人FC。

### OpenGL ES

在图形工作站、个人PC上，OpenGL都可以工作良好，但三维图形计算必须需要处理 大量数据，因此在一些如手机之类的小型设备上，如果希望使用OpenGL就比较困难了。为此，Khronos 集团为 OpenGL 提供了 一个子集：OpenGL ES ( OpenGL for Embedded System )。 Khronos是一个图形软硬件行业协会，该协会主要关注图形和多媒体方面的开放标准， Khronos协会针对手机、PDA和游戏主机等嵌入式设备设置了 OpenGLES。

OpenGLES是免费的、跨平台的、功能完善的2D/3D图形库接口 API,它针对多种嵌入 式系统（包括控制台、移动电话、手持设备、家电设备和汽车）专门设计，它是一个精心提取出来的OpenGL的子集。

OpenGL ES 剔除了 OpenGL 中 glBegin/glEnd ,四边形（GL_QUADS )、多边形 (GL_POLYGONS)等许多非绝对必要的特性。经过多年发展，目前的Open GL ES主要有两个版本，OpenGL ES 1.x针对固定管线硬件：0penGLES2.x针对可编程管线硬件。

OpenGLES 1.0 是以 OpenGL1.3 规范为基础的，OpenGLES 1.1 是以 OpenGL1.5 规 范为基础的，它们分别支持common和common lite两种profile。lite profile只支持定点实数，而common profile既支持定点数又支持浮点数，common profile发布于2005-8，引入了对可编程管线的支持。

目前Android SDK己经支持OpenGL ES 3.1，而且Android专门为 OpenGL 支持提供了 android.opengl 包，在该包提供了 GLSurfaceVievv、GLU、GLUtils 等 工具类，通过这些工具类在Android应用中使用OpenGL ES更加方便。
