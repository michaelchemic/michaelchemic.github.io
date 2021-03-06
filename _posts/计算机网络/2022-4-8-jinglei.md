---
title: "数字图像与编码"
subtitle: "数字图像与编码"
layout: post
author: "Michael"
header-style: text
tags:
  - 数字图像与编码
---

@[TOC](目录)

## 数字图像与编码
### 1. 数字图像
数字图像(digital image)，是以`二维数字`组形式表示的图像，其数字单元为 `像元` ， 其形式由数组或矩阵表示，其光照位置和强度都是离散的。 数字图像是由模拟图像经过数字化得到的，并以像素为基本元素的，可以用数字电路存储和处理的图像。
 图像信息具有`直观，形象，易懂和信息量大`的特点，图像按其内容的运动状态可分为`静止图像`和`运动图像`两大类（即：图片和视频）。

#### 1.1关于图像清晰度和分辨率的对照表：
|Resolution清晰度	|Pixels分辨率|
|-|-|
540p / qHD	|960×540 pixels
720p / HD	|1280×720 pixels
1080p / Full HD / FHD	|19201080 pixels
2K	|2048×1080 pixels
1440p / QHD / QuadHD / WQHD|	2560×1440 pixels
2160p / UHD	|3840×2160 pixels
4K	|4096×2160 pixels
5K	|5120×2880 pixels
8K / 8K UHD	|7680×4320 pixels

图像和视频信号数字化具有许多模拟信号所不具备的优点。数字信号传输质量高于模拟信号传输质量，可经过多次积累而不引起噪声严重积累；易于采用信道编码技术提高传输的可靠性；便于利用时分复用技术与其它通信业务相结合；数字信号易于加密，提高信号的安全性；数字信号易于借助计算机技术进行处理，存储。图像的压缩与编码就是在保证图像质量的前提下，用最少量的数码实现数字图像的传输与存储。
### 2. 图像数据压缩
`码流（Data Rate）`
指视频文件在单位时间内使用的数据流量，也叫码率或码流率。
我们用的单位是Kb/s或者Mb/s。一般来说同样分辨率下，视频文件的码流越大，压缩比就越小，画面质量就越高。码流越大，说明单位时间内取样率越大，数据流，精度就越高，处理出来的文件就越接近原始文件，图像质量越好，画质越清晰，要求播放设备的解码能力也越高。

`帧率(frame rate)`
    1秒钟时间里传输的图片的帧数，也可以理解为图形处理器每秒钟能够刷新几次，通常用fps（Frames Per Second）表示。高的帧率可以得到更流畅、更逼真的动画。每秒钟帧数 (fps) 愈多，所显示的动作就会愈流畅。

#### 2.1 带宽计算

计算视频流的近似带宽是将捕获图像的分辨率乘以运动图像的帧数。还有其他因素，例如音频带宽和协议（通信和压缩）的开销。但是，这些不会显着增加带宽。因此，适用的公式是：
```
帧大小Frame Size = 分辨率Resolution * 颜色深度Colour Depth（色度/亮度信息）
比特率Bit Rate = 帧大小 Frame Size * 帧速率Frame Rate（fps）

如果我们拍摄以D1（DVD）分辨率（即720 x 480）捕获图像并以30 fps（每秒帧数）传输的相机;相关数字加起来如下：

视频传输带宽=屏幕像素点*3*屏幕色深*屏幕刷新率；
//常数 3 是红绿蓝分量
帧大小 = （720*480）*24（3*8） = 829440 位 = ~ 830 Kbps（千位/秒Kbits/sec）
比特率 = 829440 *30 = 248832000 = ~ 250 Mbps（兆位/秒Mbits/sec）
```
|KBPS|	MBPS|
|-|-|
1 Kbps	|0.001000 Mbps
2 Kbps|	0.002000 Mbps
3 Kbps|	0.003000 Mbps
4 Kbps|	0.004000 Mbps

|MBPS	| GBPS|
|-|-|
1 Mbps	|0.001000 Gbps
2 Mbps	|0.002000 Gbps
3 Mbps	|0.003000 Gbps
4 Mbps	|0.004000 Gbps

以4K（3840x2160）@30Hz信号为例，需传输带宽为5.97Gbps。
视频传输带宽=屏幕像素点*3*屏幕色深*屏幕刷新率；
3840*2160*3*8*30=5.97Gbps
![在这里插入图片描述](https://img-blog.csdnimg.cn/ec55b78f19a94a0299d7778b061d9555.png)


图像信号可以压缩的根据有两方面，一方面是图像信号中存在大量`冗余度压缩`，这种冗余度在解码后可`无失真恢复`；另一方面是利用人的`视觉特性`，在不被主观视觉察觉的容限内，通过减少表示信号的精度，以`失真换取数据压缩`。

图像信号的冗余度存在于`结构和统计`两方面。图像信号结构上的冗余度变现为很强的空间（帧内）和时间（帧间）相关性。电视信号在相邻像素间，相邻行间，相邻帧间存在着这种强相关性。通过隐藏并采取适当的信号处理技术，解出图像和视频中由于`高度相关性带来的冗余`，让频带可以压缩。

信号统计上的冗余度来源于被编码信号概率密度的分布不均匀。例如在预测编码系统中，需要编码传输的是预测误差信号，它是当前待传像素值与预测值间的差分信号。由于电视信号在相邻像素间的强相关性，预测误差很小，预测误差集中分布在0附近。这种即不均匀的概率分布对采用变字长编码压缩码率极为有利，因为在编码时可以对出现概率高的预测误差用短码，对出现概率低的预测误差用长码，则总的平均码长比用固定码长编码短很多。这种编码叫`统计编码(熵编码)`。


### 3. 图像压缩信源编码过程

图像和视频信源编码的整个过程一般由以下三个步骤完成。

对表示信号的形式进行某种映射，即变换描写信号的方式。通过这种映射解除或削弱存在于图像信号内部的相关性，降低其冗余度。例如，在预测编码中，取代原始的像素值，用预测误差表示信号。
在满足对图像质量一定要求的前提下，减少表示信号的精度。这通过采用符合主观视觉特性的量化来实现。
利用统计编码（例如霍夫曼编码，算术编码等）消除追踪被编码符号所含的统计冗余度。
其中信号映射和统计编码这两个环节是可逆的过程，而量化是不可逆的，即量化必然产生失真。

PCM是由模拟信号转变为数字信号所必需经过的取样，量化过程。PCM对模拟信号在时间，空间和幅值上的离散处理会不可避免地引入误差。为了保证图像和视频信号从模拟形式变成PCM信号不产生主观上可以察觉的误差，必需保证对信号有足够高的取样频率和量化精度。除过模拟到数字信号的PCM量化外，信源编码过程中的量化实际上是对信号的2次量化。

数字信号在信道中传输时，由于干扰会产生传输误码，特别是当通过信源编码使冗余度压缩越多时，误码影响越严重。所以一般在经过信源编码之后，都要在编码后的码流中有目的地按一定规则加入差错校正码，进行误码防护，这就是信道编码。显然对整个通信系统而言，信道编码增加的冗余度比信源编码去除的冗余度要小的多，从而在通信的有效性和可靠性间实现合理的平衡。

### 4. 图像编码算法分类

图像编码技术有多种分类方法。根据编码对象的不同，可分为静止图像编码，活动图像编码，黑白图像编码，彩色图像编码等。根据压缩过程中有无信息损失可分为有损编码，无损编码。根据算法中是否采用自适应技术，可分为自适应编码和非自适应编码。最常见的是按算法原来进行分类。常见的图像编码算法主要有如下几类：

#### 4.1 预测编码

预测编码使利用图像信号在局部空间和时间范围内的高度相关性，以已经传出的近邻像素为参考，预测当前像素值，然后量化，编码预测误差。最常用的是差分脉冲编码调制（DPCM）。

与运动估值技术相结合的运动补偿帧间预测是目前视频压缩编码系统中去除信号时间域冗余信息最常用的方法。

#### 4.2 编换编码

变换编码使将一组像素值经过某种形式的正交变换转换成一组变换系数，然后根据人的主观视觉特性对各变换系数进行不同精度的量化后编码的技术。正交变换的作用是解出像素间的空间相关性，降低冗余度。用于图像编码的正交变换如离散傅里叶变换（DFT），沃尔什-哈达吗变换（WHT），哈尔变换（Harr），离散余弦变换（DCT）等。这些变换一般都有快速算法。

DCT已被目前的多种静止和活动图像编码的国际标准所采用。

#### 4.3 统计编码

这是一类根据信息熵原理进行的信息保持型边字长编码，也称熵编码。编码时对出现概率高的时间用短码表示，对出现概率低的事件用长码表示。在目前图像编码国际标准中，常见的熵编码有霍夫曼（Huffman）编码和算术编码。

#### 4.4 子带编码

子带编码属于分析-综合类的编码技术。子带编码的基本思想是，在编码端将图像信号在频率域分裂成若干子带（subband），而后对各个子带用与其统计特性相适合的编码器及比特分配方案进行数据压缩。

除了通过专门设计的正交镜像滤波器实现的经典子带编码方法之外，小波变换是目前使用最多的子带编码方法。

#### 4.5 基于模型的编码

模型基图像编码是一种基于景物三维模型的参数编码方法。相对于基于波形的编码方法而言，对参数编码所需的比特数要少得多。依据对图像内容先验知识的了解，在编解码双方建立一个相同景物的三维模型，基于这个模型，在编码器中对图像内容进行分析，提取景物参数，然后将这些参数编码传送给解码端，解码端根据接收到的参数和建立起的景物模型，采用图像合成技术再重建图像。

模型编码也属于分析-综合编码技术。模型基图像编码目前主要用于以头肩像为对象的低码率可视电话编码。

#### 4.6 其它编码算法

除过上述几大类编码算法外，还有很多其它压缩算法，例如比特平面编码，矢量量化编码，块截断编码，神经网络编码，轮廓编码等。

### 5. 图像编码的标准

ITU（国际电信联盟）和ISO/IEC（国际标准化组织）等几大标准化组织自20世纪80年代后期以来在全世界范围内积极推动，联合各国在相关领域的专家进行共同研究，先后制定了一系列静止和活动图像编码的国际标准，并致力于面向未来的多媒体编码标准的研究。

关于静止图像压缩编码，1991年通过了JPEG标准。2000年，JPEG委员会公布的国际标准JPEG 2000以小波变换作为基本算法，采用了嵌入式编码技术，在达到更高的图像质量和更高的压缩效率的同时，还能满足在移动和网络环境下对互操作性和可分级性的要求。

1990年颁布了用于视听业务的视频编码标准即H.261。随后ITU-T针对不同的电信通信网络中对实时视频通信系统的需要，先后完成了H.26X系列中多个视频编码标准，其中包括H.261，H.262，H.263，H.264，H.265。

在H.261制定的同时，ISO/IES联合技术委员会下属的分委员会于1988年成立了运动图像专家组MPEG。该委员会先后通过了MPEG系列的多个音视频压缩编码标准，包括MPEG-1，MPEG-2，MPEG4等。

ITU-T和ISO/IES这两个标准化组织除了独立制定相关标准外，还进行合作。于2001年成立了视频联合工作组JVT。2003年该工作组正式公布了H.264/MPEG-4 AVC。H.264/MPEG-4 AVC的压缩性能明显超出了以前的视频压缩标准。

HEVC是High Efficiency Video Coding的缩写，是一种新的视频压缩标准，用来以替代H.264/AVC编码标准，2013年1月26号，HEVC正式成为国际标准。
