# review

## 小结

这篇论文源于Google发起的一个开放源代码计划，利用沙盒技术提出了Native Client，使得应用程序在浏览器能直接运行程序机器码，从而让各种Web应用程序在一个受控的安全的环境下运行，在保证安全性的前提下，有着接近于机器码运作的性能。之所以需要追求足够的安全性，是因为不可信的NaCl module可能会导致各种副作用，危害系统安全。

系统设计：Native Client提供了内外两层沙盒，其中内层沙盒主要是利用代码静态分析技术来对不可信的x86代码进行安全漏洞检测，利用x86的分段式内存来对数据和指令的内存引用进行限制。此外，内层沙盒还在本地的进程中创建了安全子域，利用trampoline/springboard机制来实现可信代码与不可信代码的转化。与沙盒机制提供的安全性不同，服务运行时机制提供的是进程间通信的安全，用于为NaCl模块之间以及与浏览器之间的交互提供容器。Native Client提供了一个可信的报文抽象，即IMC，在此基础上还提供了SRPC和NPAPI两种更高级的抽象接口。

系统实现：内层沙盒利用一系列的规则来保证可靠的分解，包括一个编译工具链和一个静态分析器。基于CISC的故障隔离，利用80386段内存将数据引用限制在虚拟32位地址空间的连续子段中。定义7条二进制代码规则，用于对不可信二进制文件进行限制。不允许硬件异常和外部中断，但对于c++异常是支持的。为保证异常隔离，每个NaCl module都运行在自己的进程中。服务运行时实现了dynamic enforcement机制，利用x86的内存段式和页式保护机制来阻止不可信代码的非法内存访问。Trampoline和springboard两种机制负责不可信代码与可信代码的相互转化控制。进程间通信的基础是IMC，是基于NaCl socket实现的，提供双向、可靠、有序的数据传输服务。上层的SRPC（基于不可信代码）和NPAPI提供了JavaScript和NaCl modules间或者NaCl module间的通信服务。

系统测试：基于Native Client，通过在SPEC2000、Compute+Graphics性能测试、H.264视频解码、Bullet开源模拟系统以及Quake等的一系列实验和测试，表明了Native Client在确保较高安全性的基础上，其运行效率极其接近直接运行的结果，从而证明了Native Client的安全性和高效性。

## 观点：

优点：

1. 提出了内外两层沙盒的技术，将应用程序与操作系统进行隔离，利用定义的规则对应用程序代码进行静态检查，保证了系统的安全性。
2. 提出了可信代码与不可信代码间转化的trampoline/springboard机制，用于Service Runtime（可信）和Inner Sandbox（不可信）的控制挑转。
3. 提出了在浏览器端运行的应用程序在保证其受控安全的基础上，确保其运行性能和原生应用程序相比不会有明显降低的解决方案，这是一个非常有前瞻性的观点。在如今JavaScript大火，对Android和iOS原生应用程序造成较大冲击的环境下，这个解决方案提供了一个非常有吸引力的选择。
4. Native Client支持直接执行C/C++/Java等代码，可以简化移植过程，具有较好的可移植性。

缺点：

1. 外层沙盒所做的工作不多，只是简单地对白名单中的46个系统调用进行拒绝或通过，可以参考Android中对系统权限的分级管理策略，对不同系统调用给予不同的管理策略。
2. 随着技术的发展，目前浏览器端的应用程序所负责的内容倾向于为用户提供良好的交互体验和平滑的内容显示，高计算量的部分已经逐渐后移到服务器端。

