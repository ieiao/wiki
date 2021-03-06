# RTOS简单对比

大多数信息来源于网络，具有一定的参考价值，参考链接如下：

https://www.zhihu.com/question/301250055

https://zhuanlan.zhihu.com/p/23618181

实时操作系统（Real-time operating system, RTOS），又称即时操作系统，它会按照排序运行、管理系统资源，并为开发应用程序提供一致的基础。

目前兴起的物联网操作系统其核心就是RTOS，更多的是围绕其开发各自的生态，从而使开发更加方便，加快产品化的速度。

目前网上各类开源的RTOS/物联网操作系统层出不穷，国内受众广泛的以及比较知名的主要有：FreeRTOS、ucosiii、RT-Thread、Contiki、Zephyr、Nuttx、RIOT、mbedOS、RTX...

* FreeRTOS：全世界使用量最大的了，还有一个SafeRTOS的版本。老牌系统，稳定性有保障。
* ucos III：开源协议变来变去的。本来ucosii的时候用户基数也很大的。代码容错做了很多，保证了稳定性。老牌系统，有保障。
* RT-Thread：上海赛睿德开发的，国内社区建设很好。提出了软件包概念和一些兼容Unix接口。熟悉Linux的或者已有Linux代码的项目移植起来很方便。使用起来还是很方便的。
* Contiki：这个严格说不是个RTOS，实际使用PT协程调度写的，作者也就是PT的作者，记得是个瑞典的大佬。所以没有进程栈，相当于还是在一个while里运行的switch分支。也正因为如此，开销很小，8051内个都跑的动。Contiki也是个微内核（microkernel），所有的系统服务都是通过启线程完成，Protothreads线程整合了线程间事件通讯，使得编写系统服务非常容易。驱动程序方面，Contiki没有统一的驱动程序框架，驱动都是各家MCU自带开发包提供，这样的好处是能够保证生成的二进制代码够小。
* Nuttx：和linux很像，功能也非常全。
* RIOT是面向开发者的，开源的，适合物联网的操作系统。它的背后没有某个公司的支持，而完全是由社区驱动。
* Zephyr:Linux基金会旗下项目，和Linux很像。支持的开发板很多，组件也很多，社区也很活跃，缺点是入门难度较大，编辑环境构建复杂。
* RTX：rtx这个真心不错。从51就开始用，集成在keil里面，建工程很方便，功能也很强大。还有jlink内个segger家的，也挺好，挺喜欢他家的各种组件的

项目阶段:推荐freertos,ucosiii或者rt-thread的ltsc分支，依顺序推荐。稳定性最重要，如果项目需要特殊认证啥的需要你再看看

传感器项目:contiki,nuttx都不错

特殊需求比如zigbee协议栈蓝牙协议栈或者什么的，你再看具体场景吧用keil的可以尝试下rtx

总的来说，各类RTOS的核心都是调度，剩下的都是围绕这个核心增加各类设备驱动模型、通信模型、网络通信接口、POSIX兼容接口，基本都是生态的比拼，作为爱好者或者开发人员还是需要根据具体的情况来选择适合自己使用的系统。