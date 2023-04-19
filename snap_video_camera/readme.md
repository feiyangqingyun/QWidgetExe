
## 前言说明
1. 如果采用内核qcamera或者v4l2则不需要依赖任何第三方库。
2. 如果采用内核ffmpeg在编译好之后记得将动态库文件比如dll_ffmpeg4下面的所有文件复制到可执行文件同一目录。
3. 编译后生成的可执行文件在当前项目源码下的bin目录中。
4. 动态库区分32和64位，对应动态库目录后面有_64字样表示是64位的库。
5. 动态库下载链接: https://pan.baidu.com/s/13LDRu6mXC6gaADtrGprNVA 提取码: ujm7
6. 项目作品大全: https://qtchina.blog.csdn.net/article/details/97565652

## 其他说明
1. 如果选择 QCamera 方案的话建议用Qt6，有巨大的改进和提升，由Qt公司CTO亲自操刀编写。
2. 通用性最强可以选择 ffmpeg 方案，比如windows、linux、嵌入式linux等系统。
3. 嵌入式linux系统或者linux系统推荐用 v4l2 方案，无需任何依赖，纯v4l2框架代码实现。
4. 嵌入式linux系统如果要求流畅度推荐用ffmpeg方案，因为有缓存机制，ffmpeg方案处理的最好。
5. 采用ffmpeg方案如果官网没有提供对应的二进制包，需要自行编译，编译很简单，不要怕，就打几行命令就ok。

## 功能特点
1. 同时支持 QCamera、ffmpeg、v4l2 三种内核解析本地摄像头。
2. 提供函数 findCamera 自动搜索环境中的所有本地摄像头设备，搜索结果信号发出。
3. 支持自动搜索和指定设备两种模式，自动搜索模式下会将搜索到的第一个设备作为当前设备打开。
4. 支持同时打开多路设备，亲测4路，受限于具体的环境比如带宽。
5. 支持自动重连，默认开启，失败后会自动重新搜索和尝试打开。
6. ffmpeg方案、v4l2方案都支持回调模式（采集后转成QImage绘制）和句柄模式（采集后YUV数据GPU绘制，性能高）。
7. 视频显示位置自动调整算法，当视频分辨率超过显示控件大小则等比例缩放居中显示，不超过则原尺寸居中显示，还可设置拉伸填充显示。（自动调整、等比例缩放、拉伸填充）。
8. 可选不同的分辨率来打开摄像头，支持 160x120、320x240、640x480、800x600、1280x720、1280x960、1920x1080 等。
9. 可选不同的帧率来打开摄像头，支持 0（采用默认值）、5、、10、15、20、25、30 等。
10. 支持抓拍截图，传入文件名则自动保存截图文件，不传入则将图片数据QImage信号发出。
11. 提供函数接口 开始播放play、停止播放stop、暂停播放pause、继续播放next。
12. 支持动态热插拔加载，包括自动读取所有设备名称到下拉框。
13. 支持录像文件存储（目前仅限QCamera方案），提供开始录像recordStart、暂停录像recordPause、停止录像recordStop 等函数。
14. 提供二维码示例，自动采集画面识别二维码，支持自动将识别到的二维码重新生成大图，支持选择图片文件解析二维码，支持手动输入文本内容生成二维码。
15. 提供图片传输示例，自动将打开的摄像头视频实时传输出去，服务器端接收后解析显示。次方案可以作为将本地的摄像头实时画面远程传输，比如嵌入式板子上的摄像头画面传输到PC端显示。
16. 支持等比例拉伸填充显示，画面宽高小于显示控件的宽高则以原视频大小为准，大于则按照显示控件的尺寸等比例缩放居中。
17. 视频控件悬浮条自带开始和停止录像切换、声音静音切换、抓拍截图、关闭视频等功能。
18. 音频组件支持声音波形值数据解析，可以根据该值绘制波形曲线和柱状声音条，默认提供了声音振幅信号。
19. 内核ffmpeg支持音视频合并保存到MP4文件。
20. 代码框架和结构优化到极致，性能彪悍，持续迭代更新升级。
21. 源码支持Qt4、Qt5、Qt6，兼容所有版本。

## 视频控件
1. 可动态添加任意多个osd标签信息，标签信息包括名字、是否可见、字号大小、文本文字、文本颜色、标签图片、标签坐标、标签格式（文本、日期、时间、日期时间、图片）、标签位置（左上角、左下角、右上角、右下角、居中、自定义坐标）。
2. 可动态添加任意多个图形信息，这个非常有用，比如人工智能算法解析后的图形区域信息直接发给视频控件即可。图形信息支持任意形状，直接绘制在原始图片上，采用绝对坐标。
3. 图形信息包括名字、边框大小、边框颜色、背景颜色、矩形区域、路径集合、点坐标集合等。
4. 每个图形信息都可指定三种区域中的一种或者多种，指定了的都会绘制。
5. 内置悬浮条控件，悬浮条位置支持顶部、底部、左侧、右侧。
6. 悬浮条控件参数包括边距、间距、背景透明度、背景颜色、文本颜色、按下颜色、位置、按钮图标代码集合、按钮名称标识集合、按钮提示信息集合。
7. 悬浮条控件一排工具按钮可自定义，通过结构体参数设置，图标可选图形字体还是自定义图片。
8. 悬浮条按钮内部实现了录像切换、抓拍截图、静音切换、关闭视频等功能，也可以自行在源码中增加自己对应的功能。
9. 悬浮条按钮对应实现了功能的按钮，有对应图标切换处理，比如录像按钮按下后会切换到正在录像中的图标，声音按钮切换后变成静音图标，再次切换还原。
10. 悬浮条按钮单击后都用名称唯一标识作为信号发出，可以自行关联响应处理。
11. 悬浮条空白区域可以显示提示信息，默认显示当前视频分辨率大小，可以增加帧率、码流大小等信息。
12. 视频控件参数包括边框大小、边框颜色、焦点颜色、背景颜色（默认透明）、文字颜色（默认全局文字颜色）、填充颜色（视频外的空白处填充黑色）、背景文字、背景图片（如果设置了图片优先取图片）、是否拷贝图片、缩放显示模式（自动调整、等比例缩放、拉伸填充）、视频显示模式（句柄、绘制、GPU）、启用悬浮条、悬浮条尺寸（横向为高度、纵向为宽度）、悬浮条位置（顶部、底部、左侧、右侧）。