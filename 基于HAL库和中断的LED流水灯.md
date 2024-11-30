


@[TOC](文章目录)

---

# 前言

本文章基于stm32F103C8T6最小系统板开展实验，主要使用stm32cubemx生成工程实现LED流水灯，并且使用proteus进行仿真

---



# 一、使用proteus仿真LED流水灯（笔者使用proteus8.15）
## 1.在proteus里面创建工程并且添加元器件

点击左上角的P按键
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8de03503356543228a320861c4e9ba13.png)
在关键词搜索框输入对应的元器件缩写或者芯片型号（这里面以stm32F103C8T6为例）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b51f246da92d4123a606a1b431f3ed8b.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/021ca87b15a14c9385916a65f05baf28.png)
双击之后，元器件会添加到左上角的库中 如图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5af7b1c2ad914da0a5e858b80c9223f6.png)
依次添加仿真会使用到的元器件：3个不同颜色的LED灯 如图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4ed3df0924ca4b06840974fa1da4bf3a.png)


根据实验代码连接好原理图 PA0,PA1,PA2分别接3个led灯，如图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7978636336d34f368aab81425cf9d2bc.png)
接着添加电源：点击左边双箭头按钮，选择power 如图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d8bb3c13ca5a4a0581325c764a18f092.png)
将电源添加到原理图中：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b85fe36bab7f4b2b97665b4c98e9c5fd.png)


## 2.在proteus里面配置电源电压

电源端默认为+5V，但是我们单片机的供电电压是3.3v，需要在电源网络中进行添加。具体的配置方法如下：
首先对电源进行命名，双击原理图中的电源，命名为+3.3V
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5544aa7f7e8e401eb560439cf86fe85a.png)
依次点击设计和配置供电网:
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f7f94cf27b4e48189a1f0e9b8584680f.png)
进行如下配置：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0d9702214a5e45fd81847228cad8165b.png)
然后确定

## 3.将代码传入stm32核心板
双击原理图中stm32F103C8的核心板，传入hex文件，将代码烧录进系统板里面 
（keil在编译和下载代码时，会产生一个hex文件，一般存放在object文件夹里面，hex文件是可烧录文件夹，将它上传到proteus的stm32里面，就相当于该核心板已经被烧录了代码，仿真时，核心板就会执行有关代码）
具体在keil里面代码见我的上一篇博客
如图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/690b9b3aef5146bea34e19902381c9f7.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f6b01e6726304a4a9dacdff25af721bb.png)
然后开始仿真，就可以观察到LED灯被依次点亮
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/45dae29606dc49a886e1330e2f6a76b9.png)
仿真到这里就完成


# 二、使用stm32cubemx创建工程代码（LED流水灯和基于外部中断控制的LED流水灯）
## 1.下载F103的库文件
双击进入stm32cubemx，点击上方的help，选择倒数第二个，点击如图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7a20ac93ccf84adfb98bfa357a3b985c.png)
进入该页面，选择stm32F1，勾选最新版，然后点击install（这里电脑应该联网，否则不能安装成功），安装成功后，stm32cubemx就有了stm32F1系列核心板的库，我们就能直接配置该系列的板子了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/adf3e3a92a794582a3e3dba43ce175b1.png)
## 2.初始化配置
建立stm32cubemx工程文件
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ce4ecec9cc8a46b5982cd485324c5fd1.png)选择stm32f103c8t6
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9d0a7b69fb22492eafb27b47b281c5c7.png)
设置sys
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3e0db8ad62c0456eaf31915c5a823055.png)
 设置GPIO的引脚，直接在图上配置（将PA0,PA1,PA2设置为GPIO_OUTPUT，如果需要用到外部中断，我选择配置PA3作为外部中断引脚，设置PA3为GPIO_EXTI3）：
 ![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9d6105e80ecb4280be799b779fd79fbe.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c7a215aa0d3245d7894720f269c1a7d1.png)
需要注意的是，不要配置sys，RCC的引脚作为GPIO的输入输出，否则代码烧录时会出现问题



接着我们对外部中断相关的参数进行配置,如图：这里我选择的是下降沿触发，上拉输入的模式
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0c686553836c473cad5464dbbe8701e0.png)
在下图所示的位置选择是上拉输入还是下拉输入，这里我选的是上拉
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/346f8e3e2e89425f90cf11c76cdd75f6.png)
在下图所示的位置选择是上升沿触发还是下降沿触发，这里我选的是下降沿触发

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d81e9c997da4460f802073e24903649c.png)
配置时钟树：HCLK配置为72MHZ
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/716847f0d0e44fbb9b5a67d291feabfc.png)
工程管理：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0ea0b4df92e14d7b8f4167d9b6e474ff.png)
选择第二大行第一个（创建的工程就可以同时生成.h和.C文件）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a291d2d2e50244f99becfd2856123c0b.png)
对工程命名，选择存放的文章，然后选择使用的工具MDK-ARM
配置好后，点击右上角生成工程
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4ee793a9fc34447e86c0029e6c83321b.png)

直接进入工程，如图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2f1fa51805f541019189c19ab28fd940.png)
到此初始化完成，我们就可以开始编程了



# 三、代码编写并烧录，观察实验现象
使用stm32cubemx生成工程之后，写代码需要注意的很重要的一点就是：**写代码时只能在begin和end中间写，不要在end和begin中间写**，否则下次打开工程时，代码很容易就凭空消失了。


首先实现普通的LED流水灯：代码如下：

```c
HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_0);
		HAL_Delay(500);//延时500ms
		HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_0);
		
		HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_1);
		HAL_Delay(500);
		HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_1);
		HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_2);
		HAL_Delay(500);
		HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_2);
```

接着我们使用外部中断控制LED流水灯，外部中断的输入是PA3引脚，我们采用状态机的模式编写程序，代码如下：
定义一个变量sign，当sign=0时，灯灭，当sign=1时，流水灯工作
```c
if(sign==0)
	{
	HAL_GPIO_WritePin(GPIOA,GPIO_PIN_0,GPIO_PIN_SET);
			  HAL_GPIO_WritePin(GPIOA,GPIO_PIN_1,GPIO_PIN_SET);
			  HAL_GPIO_WritePin(GPIOA,GPIO_PIN_2,GPIO_PIN_SET);
	
	}
		if(sign==1)
	
	{
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_0);
	HAL_Delay(500);
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_0);
	
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_1);
	HAL_Delay(500);
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_1);
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_2);
	HAL_Delay(500);
	HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_2);

	}
```
中断回调函数：

```c
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
  if(GPIO_Pin==GPIO_PIN_3)
  {
   sign=1;
	}
}
```
根据我们对外部中断的配置，我们配置的上拉输入，下降沿触发，所以当PA3引脚接入低电平的时候（这里由于没有按键，我们只能用杜邦线代替按键的功能），流水灯就开始工作，实验现象如下：



[video(video-RCDtYqik-1732964128035)(type-csdn)(url-https://live.csdn.net/v/embed/436542)(image-https://v-blog.csdnimg.cn/asset/5bea42b238a609da3c354e4c66cb698d/cover/Cover0.jpg)(title-VID_20241130_183626)]

# 四、Keil的软件仿真逻辑分析仪功能观察管脚
在用示波器进行分析之前，首先对keil进行如下配置：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f49b1c155fe144ce8272305c109df3eb.png)
**注意4和6，这里应该填-pSTM32F103C8**因为我们使用的芯片是stm32F103C8T6
配置好后，添加管脚，进入示波器进行观察：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c2da1140872c45b69b9f0f6ca2cf8d74.png)
可以发现的是，在PA3接入低电平之前，由于引脚PA0,PA1,PA2一直输出高电平，所以它们的点位如下图（红色是PA0，绿色是PA1, 蓝色是PA2  黄色是PA3）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/41c49d2323524f4988f7feb8ac0bdc42.png)
# 五、在proteus电路中加入示波器模块，观察GPIO端口波形
添加示波器到原理图，硬件连接方式如图（PA0接A，PA1接B，PA2接C）：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a0a48bebf32448a1bd1e425ad55fd10b.png)

点击开始仿真，效果图如下：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f27b88d75ad84451909faec2b373a523.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5c22e2b9b2d840f58a40d9c7ef1354fb.png)
# 六. 总结
在嵌入式系统开发中，LED流水灯实验是一个经典的基础实验，它可以帮助开发者熟悉GPIO操作、中断机制以及如何利用HAL库进行开发。本次实验通过使用STM32微控制器，结合HAL库和外部中断，实现LED灯的流水效果，在学习过程中我加深了对中断处理机制的理解，掌握了HAL库的基本使用方法，同时我学会了stm32cubemx的基本用法，这是一个很方便的代码生成工具，对开发者很友好。
经过一系列努力后，我成功实现了基于 HAL 库和外部中断的 LED 流水灯实验。当系统正常运行时，LED 都熄灭，当按下外部中断按键时（本实验用杜邦线替代），流水灯能够根据设定的逻辑恢复运行，达到了预期的实验效果。
在实验过程中，不可避免地会遇到各种问题，而调试技能的高低直接影响到问题解决的效率。通过本次实验，我学会了使用多种调试工具和方法，如示波器、逻辑分析仪等，能够逐步定位和分析问题所在，并最终找到解决问题的方法。
总之，在本次基于 HAL 库和外部中断的 LED 流水灯实验中，我深入了解了嵌入式系统开发中的关键技术与概念，不仅在实践技能上取得了显著进步，更在对嵌入式系统的理解上有了质的飞跃。
