.. vim: syntax=rst

编译uboot
-------

本章节内容处于开发笔记状态，还待整理至最终版的教程。

本章节内容处于开发笔记状态，还待整理至最终版的教程。

uboot下载
~~~~~~~

在编译内核前我们首先要下载到官方提供的uboot镜像，打开\ http://git.freescale.com/git/\ 网页，可以看到很多nxp官方提供的镜像，有内核镜像也有uboot镜像，我们下载官方提供的uboot镜像，名字为uboot-imx，我们点击uboot-
imx.git链接，跳转到下载页面，可以发现该下载页面中有很多uboot相关的信息，如它的分支，标签等，当然我们要注意的是uboot下载界面中的下载信息，我们可以通过git clone命令去拉取uboot-imx源码，他们的地址就是在下载页面信息的最下方：

Clone

.. code-block:: sh
   :linenos:

   git://git.freescale.com/imx/uboot-imx.git http://git.freescale.com/git/cgit.cgi/imx/uboot-imx.git

具体如下图所示。



.. image:: media/building_uboot002.png
   :align: center
   :alt: 未找到图片02|



.. image:: media/building_uboot003.png
   :align: center
   :alt: 未找到图片03|



编译我们提供的uboot
~~~~~~~~~~~~

除了下载官方的uboo以外，我们还会提供我们的uboot，当然，我们
的uboot跟官方uboot是一样的，只不过多了我们的配置文件，仅此而已。

我们把uboot源码解压到linux虚拟机中，然后准备开始编译。

首先使用git branch命令检查一下我们当前源码的分支是属于哪个
分支，我们在编译uboot的时候，要切换到imx_v2016.03_4.1.15_2.0.0_ga分支
中，如果当前分支不是imx_v2016.03_4.1.15_2.0.0_ga则使用git checkout命令切换分支。

命令（检查并列出所有分支）


.. code-block:: sh
   :linenos:

   git branch -a

输出


.. code-block:: sh
   :linenos:

   \* master remotes/origin/HEAD -> origin/master remotes/origin/imx_3.14.38_6ul_engr
   
   ….（此处省略其他分支）

命令（切换分支）


.. code-block:: sh
   :linenos:

   git checkout imx_v2016.03_4.1.15_2.0.0_ga

输出


.. code-block:: sh
   :linenos:

   Checking out files: 100% (14231/14231), done.Branch 'imx_v2016.03_4.1.15_2.0.0_ga' set up to track remote branch 'imx_v2016.03_4.1.15_2.0.0_ga' from
   'origin'.Switched to a new branch 'imx_v2016.03_4.1.15_2.0.0_ga'

切换分支后，由于我们的源码已经打上补丁的，所以不需要修改就可直接编译，我们可
以通过git log命令查看一下是否已经打上补丁，如果存在以下代码则表示打上了补丁：

命令



.. code-block:: sh
   :linenos:

   git log

输出

commit d23b9c3540a14e03d78279e378220143adde7096 (HEAD -> imx_v2016.03_4.1.15_2.0.0_ga)Author: pengjie <jiejie.128@163.com>Date: Tue Sep 17 22:43:26
2019 +0800 1.修改设备树,适配个别的HDMI显示器,使其能够正常的显示 Signed-off-by: pengjie <jiejie.128@163.com>

commit b107e6ba6f0648c36af5e4ef146e8757041d4825Author: pengjie <jiejie.128@163.com>Date: Mon Aug 5 21:31:59 2019 +0800 add for 4.3 & 5.0 & 7.0 LCD
Signed-off-by: pengjie <jiejie.128@163.com>

在编译之前，需要装交叉编译器arm-linux-gnueabihf-gcc，版本是v7.4.0，当然，如果
虚拟机中已经装有交叉编译器的话则可以略过此步骤。


.. code-block:: sh
   :linenos:

   sudo apt-get install gcc-arm-linux-gnueabihf

编译的时候需要我们自定义配置，而nxp官方会提供一些默认配置，这些配置
在uboot /configs/目录下，如mx6ull_14x14_evk_defconfig、mx6ull_14x14_evk_emmc_defconfig、mx6ull_14x14_evk_nand_defconfig等就是n
xp官方为imx6ull提供的配置文件，可以编译生成从SD卡启动的uboot、从emmc启
动的uboot以及从nand启动的uboot，我们可以根据需求选择不同的配置文件从而编译不同的uboot。

进入uboot源码目录下，如果想要编译从SD卡启动的uboot，则可以运行以下命
令选择SD卡版本的uboot配置：

命令（编译sd卡版本uboot）


.. code-block:: sh
   :linenos:

   make ARCH=arm mx6ull_14x14_evk_defconfig

而如果想编译从emmc启动的uboot，则运行：

命令（编译emmc版本uboot）


.. code-block:: sh
   :linenos:

   make ARCH=arm mx6ull_14x14_evk\_ emmc \_defconfig

想编译从nand启动的uboot，则运行：

命令（编译nand版本uboot）


.. code-block:: sh
   :linenos:

   make ARCH=arm mx6ull_14x14_evk\_ nand \_defconfig

在运行以上命令后，Makefile会根据对应的配置文件的内容将配置更新到当前目录下的 .config文件中，如果读者感兴趣可以查看一下该文件的内容，接下来的编译则是根据.config文件的配置进行编译的。

开始编译，运行以下命令：

命令（编译uboot）


.. code-block:: sh
   :linenos:

   make -j4 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-

输出


.. code-block:: sh
   :linenos:

   scripts/kconfig/conf --silentoldconfig Kconfig CHK include/config.h UPD include/config.h GEN include/autoconf.mk GEN include/autoconf.mk.dep
   ….(省略输出)
   LD u-boot OBJCOPY u-boot-nodtb.bin OBJCOPY u-boot.srec SYM u-boot.sym COPY u-boot.bin CFGS board/freescale/mx6ullevk/imximage.cfg.cfgtmp MKIMAGE
   u-boot.imx

命令中带有jN 参数，表示最大使用N个线程编译，如j4则表示最
大使用4个线程编译，根据自己电脑配置决定即可，当然线程数量越多，编译越
快。在输出信息的最下方，可以看到已经生成对应的uboot（名字是u-boot.imx），可以直接烧录到开发板中。

尝试在uboot中添加自己的修改
~~~~~~~~~~~~~~~~

有时候官方的uboot总是不能满足我们的需求，我们得学会修改对
应的uboot配置，比如开机的logo，比如显示屏配置，HDMI配置等，我们就简单
修改一下我们的配置，让他能在我们的屏幕上运行，也把开机logo替换为我们的logo。

首先我们要知道这些配置写在什么地方，然后才能去修改它，比如我
们先改开机logo，找到uboot /tools/目录下的Makefile文件，大约在200行，就
有关于开机logo的配置，具体如下：


.. code-block:: sh
   :linenos:

   # Generic logoifeq ($(LOGO_BMP),)LOGO_BMP= $(srctree)/$(src)/logos/denx.bmp# Use board logo and fallback to vendorifneq ($(wildcard $(srctree)/$(src)/
   logos/$(BOARD).bmp),)LOGO_BMP= $(srctree)/$(src)/logos/$(BOARD).bmpelseifneq ($(wildcard $(srctree)/$(src)/logos/$(VENDOR).bmp),)LOGO_BMP= $(srctree)/
   $(src)/logos/$(VENDOR).bmpendifendifendif # !LOGO_BMP

解释一下Makefile文件的描述：

使用ifeq 判断是否指定了LOGO_BMP变量（该变量表示开机logo图片），如果
不指定则使用默认logo图片denx.bmp，该图片在logos目录下。

然后判断一下是否存在使用开发板名字命名的图片（如$(BOARD)，它是一个变量
的引用，表示开发板的名字），如果是则使用$(BOARD).bmp。

最后判断一下是否存在以供应商名字命名的图片（如$(VENDOR).bmp），那么很显然，nxp官方提供的uboot必然是以它们的名字命名logo，那么uboot就会使用它们的logo图片，我们可以到uboot /tools/logos目录下查看一番，就会发
现存在freescale.bmp文件，如下图所示。

注意：开机logo必须是bmp类型的图片，否则可能出现错误。

.. image:: media/building_uboot004.png
   :align: center
   :alt: 未找到图片04|



既然要修改logo，我们把自己的开机logo图片放进去替换
原本的logo即可，我们的开机logo如下所示（注意：logo图片格式必须为bmp格式）。

.. image:: media/building_uboot005.png
   :align: center
   :alt: 未找到图片05|



这些logo在uboot启动时就会被显示在屏幕上，具体的显
示logo的函数在uboot /board/esd/common/目录下的lcd.c文件中，大约
在81行左右，感兴趣的读者可以去看看源码，在这里就不深入分析。

接着我们可以修改显示屏，让我们的开发板支持显示屏的其他尺寸，那么
此时就要去配置源码中修改了，nxp官方支持的imx6ull开发板相关的配置
源码文件在/uboot/board/freescale/mx6ullevk/目录下的mx6ullevk.c文件中，我
们简单修改一下displays这个数组，它是记
录了与显示屏相关信息的数组，具体修改如下：

.. code-block:: c
   :caption: 修改结果
   :linenos:

   struct display_info_t const displays[] = {{
   
   .bus = MX6UL_LCDIF1_BASE_ADDR,
   
   .addr = 0,
   
   .pixfmt = 24,
   
   .detect = NULL,
   
   .enable = do_enable_parallel_lcd,
   
   .mode = {
   
   .name = "TFT43AB",
   
   .xres = 480,
   
   .yres = 272,
   
   .pixclock = 108695,
   
   .left_margin = 8,
   
   .right_margin = 4,
   
   .upper_margin = 2,
   
   .lower_margin = 4,
   
   .hsync_len = 41,
   
   .vsync_len = 10,
   
   .sync = 0,
   
   .vmode = FB_VMODE_NONINTERLACED
   
   }
   
   },
   
   {
   
   .bus = MX6UL_LCDIF1_BASE_ADDR,
   
   .addr = 0,
   
   .pixfmt = 24,
   
   .detect = NULL,
   
   .enable = do_enable_parallel_lcd,
   
   .mode = {
   
   .name = "TFT50AB",
   
   .xres = 800,
   
   .yres = 480,
   
   .pixclock = 108695,
   
   .left_margin = 46,
   
   .right_margin = 22,
   
   .upper_margin = 23,
   
   .lower_margin = 22,
   
   .hsync_len = 1,
   
   .vsync_len = 1,
   
   .sync = 0,
   
   .vmode = FB_VMODE_NONINTERLACED
   
   }
   
   },
   
   {
   
   .bus = MX6UL_LCDIF1_BASE_ADDR,
   
   .addr = 0,
   
   .pixfmt = 24,
   
   .detect = NULL,
   
   .enable = do_enable_parallel_lcd,
   
   .mode = {
   
   .name = "TFT70AB",
   
   .xres = 800,
   
   .yres = 480,
   
   .pixclock = 108695,
   
   .left_margin = 46,
   
   .right_margin = 22,
   
   .upper_margin = 23,
   
   .lower_margin = 22,
   
   .hsync_len = 1,
   
   .vsync_len = 1,
   
   .sync = 0,
   
   .vmode = FB_VMODE_NONINTERLACED
   
   }
   
   }
   
   };

这里的配置是支持3个野火显示屏尺寸的，4.3寸、5寸、7寸，不同的屏幕尺寸
稍微不一样，具体阅读以上配置即可，此处不深入研究。

当然除此之外还会修改一些其他地方（此处就不细说），如果是初学者，建议
使用我们提供的补丁，把内核源码打上补丁，这种方式是最简单的方式，当
然，如果是已经入门的读者，可以直接阅读补丁文件的内容，下面就介绍打补丁去过程。

首先将补丁文件拷贝到uboot源码目录下，然后可以通过git am命令给uboot源码
打补丁，具体操作如下：

命令（在打补丁前确认分支是imx_v2016.03_4.1.15_2.0.0_ga）


.. code-block:: sh
   :linenos:

   git branch

输出


.. code-block:: sh
   :linenos:

   \* imx_v2016.03_4.1.15_2.0.0_ga master

命令（打第一个补丁）


.. code-block:: sh
   :linenos:

   git am 0001-add-for-4.3-5.0-7.0-LCD.patch

输出


.. code-block:: sh
   :linenos:

   Applying: add for 4.3 & 5.0 & 7.0 LCD

命令（打第二个补丁）


.. code-block:: sh
   :linenos:

   git am 0002-1.-HDMI.patch

输出


.. code-block:: sh
   :linenos:

   Applying: 1.修改设备树,适配个别的HDMI显示器,使其能够正常的显示

注意：需要在打补丁前确认分支是imx_v2016.03_4.1.15_2.0.0_ga，当然，也必须确认uboot源码没有被修改过，可以通过git status命令查看，如果修改过则通过git reset --hard [commit]
命令欢迎到imx_v2016.03_4.1.15_2.0.0_ga分支。

当源码打完补丁后，可以通过 git log 查看日志信息，然后可以跟句需要进行编译，具体编译过程在上一小节中已讲解，此处不再重复赘述。

烧录uboot并测试
~~~~~~~~~~

我们编译一个emmc的uboot，具体过程如下：


.. code-block:: sh
   :linenos:

   #emmc版本make ARCH=arm mx6ull_14x14_evk_emmc_defconfig
   
   #编译make -j4 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-

并且将uboot烧录到开发板中可以看到启动时图片已经变为我们的logo了，具体见

（此处要加一张开机logo）

Clone



.. |buildi002| image:: media/building_uboot002.png
   :width: 5.76806in
   :height: 3.62709in
.. |buildi003| image:: media/building_uboot003.png
   :width: 5.76806in
   :height: 3.13519in
.. |buildi004| image:: media/building_uboot004.png
   :width: 3.37313in
   :height: 1.20801in
.. |buildi005| image:: media/building_uboot005.png
   :width: 3.43284in
   :height: 1.2483in
