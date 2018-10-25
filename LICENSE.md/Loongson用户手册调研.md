# 龙芯3A3000Cache调研  

## Cache存储层次总览  

* Cache层次图

![](pic/Cache存储层次总览.png)

* 结构总述

  根据各级缓存与处理器运算流水线的距离，由近及远，依次为：第一级的指令缓存(Instruction-Cache，I-Cache)和数据缓存(Data-Cache，D-Cache)，第二级的牺牲缓存(Victim-Cache，V-Cache)，第三级的共享缓存(Shared-Cache，S-Cache)。其中 I-Cache、D-Cache 和 V-Cache 为每个处理器核私有，S-Cache 为多核及 I/O共享。处理器核通过芯片内部及芯片之间的互联网络访问 S-Cache。

  具体细节参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第59页

* 各级Cache重要参数表

  ![1540467357443](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\参数.png)

## 各级Cache具体信息

* 一级指令Cache

  * 缓存行数据结构

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\icache.png)

  * 索引方式与实现

  * 数据校验方式与错误处理

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第60页

* 一级数据Cache

  * 缓存行数据结构

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\dcache.png)

  * Tag信息

  * 索引方式与实现

  * 数据校验方式与错误处理

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第61页

* 二级牺牲Cache

  * 缓存行数据结构

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\vcache.png)

  * Tag信息

  * 索引方式与实现

  * 数据校验方式与错误处理

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第62页

* 三级共享Cache

  * 共享缓存的分体结构

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\三级共享缓存体选择位与索引地址.png)

  * 共享缓存的锁机制

  * 共享缓存的缓存行结构

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\5 三级共享缓存行结构示意.png)

  * 共享缓存的校验

    参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第63页

## 缓存算法

* 非缓存算法
* 一致性缓存算法
* 非缓存加速算法

参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第64-65页

## 缓存一致性

* GS464E 实现了基于目录的缓存一致性协议，由硬件保证 I-Cache、D-Cache、V-Cache、S-Cache、内存以及来自 HT 的 IO 设备之间数据的一致性，无需软件利用 Cache 指令来维护缓存一致性。具体实现参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第65页

* 一级指令缓存与一级数据缓存间的一致性维护

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第69页

* 处理器与 DMA 设备间的缓存一致性维护

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第69页

## 缓存管理

* Cache指令

  * 简述：参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第66页

  * 根模式下的Cache指令

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\根cache指令1.png)

    ![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\根cache指令2.png)

  * 客模式下的Cache指令

    客模式下 CACHE 指令的使用受到根模式的控制，具体定义如下：

    * GuestCtl0.CG=0 时，使用任何 CACHE 指令都将触发客模式特权敏感指令例外(GPSI)。
    *  GuestCtl0.CG=1 时，使用 op[4:2]=1, 2, 6, 7 的 CACHE 指令将触发客模式特权敏感指令例外(GPSI)。
    *  GuestCtl0.CG=1，但 Diag.GCAC=0 时，使用 CACHE0、CACHE1、CACHE3 指令将触发客模式特
      权敏感指令例外(GPSI)。

* 缓存初始化

  * 基于硬件的初始化
  * 基于软件的初始化

  参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第67页

## Cache的错误例外

当取指或 load/store 操作执行时发现 Cache 的 tag 或 data 出现校验错误时，触发该例外。该例外不可屏蔽。因为该例外涉及的错误在 Cache 中，所以采用专门的例外入口，位于非映射非缓存地址段。该例外仅在根模式下处理。

![](C:\Users\Lumos\Desktop\大三上\编译原理H\Loongson用户手册调研\pic\例外.png)

参考[龙芯3A3000用户手册](http://www.loongson.cn/uploadfile/cpu/3A3000/Loongson3A3000_3B3000user2.pdf)第77页