
## 1\. 简介


* 开发环境`Ubuntu20.04`
* 目标平台：瑞芯微`RK356X`
* 目标平台内核版本：`4.19.234`
* wifi模块型号：`AIC8800D80P Wi-Fi6/BT5.0`


![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114085422416-439000212.png)


## 2\. 硬件


* `wifi`模块的`PID:VID` \= `a69c:8d80`
* 此模块挂在`RK356X`的PCIE接口上


![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114084530095-765412107.png)


	+ `01:00.0 Class 0280: a69c:8d80`
		- `Class 0280`: 表示网络控制器（通常是无线网卡）
		- `a69c:8d80`: 是供应商和设备的 ID，具体的设备信息可以通过这些 ID 查询。
	+ `00:00.0 Class 0604: 1d87:3566`
		- `Class 0604`: 表示 PCI 桥（用于连接不同的 PCI 总线）
		- `1d87:3566`: 是供应商和设备的 ID，用于标识具体的制造商和设备


## 3\. 驱动编译


* 从厂家处获取驱动源码,源码结构如下
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090337818-665844050.png)


	+ `PCIE/driver_fw/driver/aic8800`目录下存放的就是驱动源码
	![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090645233-671291412.png)
	+ `PCIE/driver_fw/fw/aic8800D80` 目录下存放着安装驱动时依赖的固件
	![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090614480-1090500542.png)
* 修改`Makefile`


	+ 指定目标平台为瑞芯微平台，修改`Platform support list`如下
	![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114085930101-1814306742.png)
	+ 修改瑞芯微平台相关配置如下，指定内核源码路径和交叉编译工具链，按实际情况进行修改
	![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114085953950-691352886.png)
* 修改源码`aicwf_pcie_api.c`的第232行，解决编译报错问题
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090051330-368332610.png)
* `make`编译，得到驱动文件`aic8800D80_fdrv.ko`,大约12M大小
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090137384-1889041800.png)


## 4\. 安装wifi固件


* 厂家已经提供了编译好的wifi固件
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114090757980-1267321419.png)
* 将固件拷贝到RK3566开发板的`/lib/firmware/aic8800D80/`路径下，路径必须正确，如需将固件放在其它路径，需要修改驱动源码，在此就不展开了
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114091018909-142950282.png)


## 5\. 安装驱动


* 将编译好的驱动拷贝到开发板上，`insmod`安装



```


|  | root@RK356X:/tmp# insmod aic8800D80_fdrv.ko |
| --- | --- |


```
* 驱动加载时的`dmesg`日志信息
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114091130139-32268729.png)
* 查看wifi对应的`interface`
![](https://img2024.cnblogs.com/blog/2857641/202411/2857641-20241114091155717-1257233089.png)


## 6\. 将wifi配置成AP模式


* 参考我的另一篇文章



> [https://github.com/zhijun1996/p/18545307](https://github.com)


 本博客参考[FlowerCloud机场](https://yunbeijia.com)。转载请注明出处！
