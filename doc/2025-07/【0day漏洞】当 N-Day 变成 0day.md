#  【0day漏洞】当 N-Day 变成 0day  
chosenny  神农Sec   2025-07-18 05:00  
  
扫码加圈子  
  
获内部资料  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
网络安全领域各种资源，EDUSRC证书站挖掘、红蓝攻防、渗透测试等优质文章，以及工具分享、前沿信息分享、POC、EXP分享。  
不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（知识星球优惠卷）。  
#   
  
文章作者：chosenny  
  
文章来源：https://www.freebuf.com/articles/vuls/421491.html  
  
  
**探索当 N-Day 变成 0day**  
  
  
## 背景  
  
2022年6月23日，Exodus Intelligence公开了一个影响TP-Link制造的WR940N V5和WR941ND V6路由器的漏洞。这个漏洞被标记为“未初始化指针漏洞”，但由于手头只有WR940N V6型号，我决定先分析WR940N V5的固件，然后再看V6型号。但是在分析过程中，我注意到了一些空白点。  
## 发现漏洞  
  
有多种方式可以发现这个漏洞，且没有哪一种方式是错误的，因为最终结果都是一样的。我将展示两种使用非常原始技术发现这个漏洞的方法。  
## 原始技术 #1  
  
Exodus Intelligence的通报详细说明了该漏洞在“处理UPnP/SOAP SUBSCRIBE请求”时被触发，这一点在UPnP设备架构2.0 PDF中有详细描述。通报中列出的HTTP方法SUBSCRIBE与UPnP的GENA（通用事件通知架构）部分相关，负责处理事件通知，外部设备可以对其进行订阅（SUBSCRIBE）和取消订阅（UNSUBSCRIBE）。  
  
在提取的固件中进行简单的grep  
搜索，查找“SUBSCRIBE”会返回三个二进制文件：  
```
$ grep -r "SUBSCRIBE" .
Binary file ./sbin/hostapd matches
Binary file ./lib/libwpa_common.so matches
Binary file ./usr/bin/httpd matches

```  
  
在进入bindiff之前，决定先比较这些二进制文件的哈希值，以查看是否可以立即排除某些二进制文件。尽管使用的哈希方法（MD5）被称为“不安全”，但在本发布时，尚无已知的路由器厂商在其生成的二进制文件中使用MD5哈希碰撞。  
  
**未打补丁的固件（v.211111）：**  
```
ef7abcd4f5a2289c24a50c9fa9fda8a1  ./sbin/hostapd
45725cdfe9ad7d8323c50167908acc23  ./lib/libwpa_common.so 
4dac0ec14e36001092cc2560f297a715  ./usr/bin/httpd

```  
  
**已打补丁的固件（v.220610）：**  
```
8aa55621c7277b7cc998ecc80fd9d6a4  ./sbin/hostapd
45725cdfe9ad7d8323c50167908acc23  ./lib/libwpa_common.so
4feb70561feb0391404ee29712b0144e  ./usr/bin/httpd

```  
  
哈希二进制文件并进行比较可能是浪费时间，但在这种情况下，它帮助排除了文件./lib/libwpa_common.so  
，因为两个MD5哈希值相同。这只剩下两个文件./sbin/hostapd  
和./usr/bin/httpd  
。接下来，我们需要使用Bindiff来找出哪个二进制文件包含漏洞。  
### 代码解析：  
1. **grep命令**  
：grep -r "SUBSCRIBE" .  
是一个递归搜索命令，用来在当前目录及其子目录中查找包含“SUBSCRIBE”字样的文件。在这段代码中，返回了三个二进制文件，分别是./sbin/hostapd  
、./lib/libwpa_common.so  
和./usr/bin/httpd  
。  
  
1. **哈希比较**  
：  
为了缩小调查范围，作者对提取的二进制文件进行了MD5哈希比较。哈希值是一种通过算法生成的文件的唯一标识符，通常用于验证文件是否被修改。通过对未打补丁和打补丁后的固件进行哈希值比较，发现./lib/libwpa_common.so  
的MD5哈希值在两个固件版本中相同，因此排除了该文件，认为它与漏洞无关。  
  
1. **Bindiff**  
：  
使用Bindiff  
（一个二进制差异分析工具）来进一步对比./sbin/hostapd  
和./usr/bin/httpd  
这两个文件，找出哪个文件包含漏洞。Bindiff  
通常用于比较两个二进制文件的差异，帮助研究人员确定新旧版本的代码或功能差异，从而定位漏洞。  
  
## 原始技术 #2  
  
让我们回到之前的假设，假设通报中删除了关于UPnP / GENA的参考信息。这种假设的情况在漏洞通报中经常发生。例如，CVE-2014-4126包含以下信息：“Microsoft Internet Explorer 10和11允许远程攻击者通过精心构造的网站执行任意代码或导致拒绝服务（内存损坏），即“Internet Explorer内存损坏漏洞”。  
  
由于漏洞存在于某个服务中，因此一个不错的策略是首先列出提取的固件中的可执行文件。通过使用find  
命令，我们可以查看固件中有多少个文件被标记为可执行文件。  
```
$ find . -executable -type f | wc -l
170

```  
  
哇！一开始看似固件镜像中有很多可执行文件，但如果在binwalk提取固件时发生了某些事情呢？通过查看find  
命令的手册页，发现可以使用-exec  
动作对每个找到的文件执行附加任务。通过利用file  
命令，可以打印出每个文件名旁边的文件类型信息。查看前几个条目后，显然返回的某些文件不是ELF文件。  
```
$ find . -executable -type f -exec file '{}' \;
./etc/ath/wsc_config.txt: ASCII text
./etc/ath/default/default_wsc_cfg.txt: ASCII text
[removed]
./web/login/input-box1.png: PNG image data, 250 x 32, 8-bit/color RGBA, non-interlaced
[removed]
./web/help/WanSlaacCfgHelpRpm.htm: HTML document, ASCII text, with very long lines, with CRLF line terminators

```  
  
为了减少文件的数量，find  
的输出被管道传递给grep  
，搜索输出中的“ELF 32-bit”。  
```
$ find . -executable -type f -exec file '{}' \; | grep "ELF 32-bit" -c
72

```  
  
可执行文件（包括库）的列表减少了58%。接下来的步骤是创建一个小的bash命令来哈希文件并进行比较。这可能是一个延伸的操作，但它可能有助于进一步减少文件列表。  
  
为了生成列表，以下命令在每个提取的squashfs  
目录中执行。  
```
$ find . -executable -type f -exec file '{}' \; | grep "ELF 32-bit" | cut -d ":" -f 1 | while read -r string; do md5sum $string; done
50a9bc41ebc4db4bcdf526b64e8b9ae2  ./bin/busybox
0781d8cd42137165f4b38eb67b41e07c  ./sbin/wifitool
e5d4b3d6d5ce16592b23c82a7872b97e  ./sbin/iwpriv
dd22c3d54c059547fabc4ce7d9c92adf  ./sbin/iwlist
9a71298edacef371a920509249373d06  ./sbin/iptables-multi
17dbb88ae25c0323984e754aa0628ed8  ./sbin/tc
4a9af1c3a57b36ef4e31542c9a1228aa  ./sbin/iwconfig
02dbb91d7fd6401158aea9021de72cf5  ./sbin/wpa_supplicant
ef7abcd4f5a2289c24a50c9fa9fda8a1  ./sbin/hostapd
c1efb3e78c002b7a82d2d28739bfcb1d  ./sbin/wlanconfig
e1aef559e8ccf27b79b83e05f577bd59  ./lib/ld-uClibc-0.9.30.so
45725cdfe9ad7d8323c50167908acc23  ./lib/libwpa_common.so
7c34616b9c965c7dd4f8e1b1f2d18d6f  ./lib/libip6tc.so.0.0.0
3d5625439ce9cd389bd3b7ebcc3eb6e1  ./lib/libxtables.so.2.1.0
368cd21ea41bcece3ecd41335fcbba97  ./lib/libwolfssl.so.14.0.0
452a4826c92c8a51284af5007d9a6db8  ./lib/libiptc.so.0.0.0
b3a33a68a1ef0cfa2a5bd6878d2e3310  ./lib/libwpa_ctrl.so
[...等]

```  
  
运行diff  
命令对比两个文件的差异：  
```
$ diff unpatched_executables_md5.txt patched_executables_md5.txt 
< 9060164431357066c3607ebc476761c6  ./bin/busybox
---
> 50a9bc41ebc4db4bcdf526b64e8b9ae2  ./bin/busybox
9c9
< 8aa55621c7277b7cc998ecc80fd9d6a4  ./sbin/hostapd
---
> ef7abcd4f5a2289c24a50c9fa9fda8a1  ./sbin/hostapd
60c60
< 4feb70561feb0391404ee29712b0144e  ./usr/bin/httpd
---
> 4dac0ec14e36001092cc2560f297a715  ./usr/bin/httpd

```  
  
72个文件的列表现在已经减少到仅剩下3个文件！下一步是将这些文件通过Binexport处理，然后再通过Bindiff进行分析。  
### 代码解析：  
1. **find命令**  
：  
结果显示，固件中有170个可执行文件。  
  
1. find . -executable -type f  
：查找当前目录及其子目录中的所有可执行文件。  
  
1. wc -l  
：计算查找到的文件数量。  
  
1. **file命令**  
：  
  
1. -exec file '{}' \;  
：对find  
命令返回的每个文件执行file  
命令，输出每个文件的类型。  
  
1. 输出中，file  
识别了某些文件不是ELF格式的可执行文件（例如文本文件、图片、HTML文件）。  
  
1. **grep过滤**  
：  
  
1. grep "ELF 32-bit" -c  
：过滤并统计ELF格式的可执行文件数量，最终筛选出72个ELF文件。  
  
1. **哈希生成与比较**  
：  
  
1. 使用md5sum  
命令对筛选出的ELF文件进行MD5哈希计算，生成文件的唯一标识符。  
  
1. 通过diff  
命令对未打补丁和已打补丁的文件哈希值进行比较，找出差异文件（如./sbin/hostapd  
和./usr/bin/httpd  
）。  
  
## Bindiff  
  
在这两种原始技术中，/sbin/hostapd  
和/usr/bin/httpd  
都出现在两个列表中，而文件/bin/busybox  
只出现在其中一个列表中。为了提高发现漏洞的机会，首先会分析hostapd  
和httpd  
文件，至于busybox  
，则会暂时留到最后再看，或者甚至不看。  
### HTTPd Bindiff 分析  
  
从httpd  
开始，并按相似度得分从高到低对Bindiff分析结果进行排序，发现大多数分析的函数都是虚假的（即充满了MIPS NOP指令。NOP = 0x00000000），或者与WAN连接相关。被分析的函数没有包含与初始化局部变量相关的指令（例如sw $zero ($sp)  
），也似乎没有修补任何漏洞。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVyeaE9UibaL3o0Q31EmGaHnsBiaXuCmicyAr0yiaGzmVEh6ne2ofUHPj4PA/640?wx_fmt=jpeg&from=appmsg "")  
  
现在是时候转到下一个二进制 hostapd 了。  
### Hostapd Bindiff 分析  
  
在分析hostapd  
的Bindiff输出时，按相似度得分从高到低排序，只有一个函数引起了注意，那就是sub_004498D4  
。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVficEShbQoWXTw2gTHe7cGFglbvY2eEsfFtWAKs1rFPk5ibpBbrdBDHNg/640?wx_fmt=jpeg&from=appmsg "")  
  
当观察差异时，只有一条指令显示出不同。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVmDRvFf51NejAQmUv8jNcbDE9J8d20ye7iaululBlfVsE6SFnyZjpj3Q/640?wx_fmt=jpeg&from=appmsg "")  
  
  
该指令sw $zero 0x20($sp)  
的意思是将$zero  
寄存器中的字（4字节，始终为 0x00000000）存储到栈指针偏移量为 0x20 的局部变量中。这条指令意味着偏移量为 0x20 的局部变量从未被初始化。（例如：int foo = 0;  
与int foo;  
）  
  
当查看该函数的调用图时，很明显，这个函数是某种解析器，因为它包含了循环和边缘。  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aV5XNRHbNg0dIu2Ly5h3ia1owiaoUIDsh3OwYNLvgyHNdplcWYaicQvqib3g/640?wx_fmt=jpeg&from=appmsg "")  
  
通过更深入地查看该函数，并利用 HLIL（高层中间语言）进行分析，似乎这个特定的函数解析某种 HTTP 请求。通过使用strchr()  
查找换行符（0x0a）和strncasecmp()  
语句，可以推测这个函数负责处理 GENA 订阅请求。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aV1Cr7yY2smuvBT5smuXNtJ8FE0YHlLZ3RAiaAQ02GTqdEzpFkEQ6M2Fg/640?wx_fmt=jpeg&from=appmsg "")  
  
看到这个函数处理传入的订阅请求，而且该函数唯一的修改就是一个局部变量被初始化，我认为这是一个很好的起点，因为这一小的修改让我立刻联想到“未初始化的栈变量”！下一步是看看这个变量是怎么使用的，以及它的具体作用。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVZoTVk8CnWeUWX33A7dHticZ3nJboB6bo3XrSd4BqOCzdTICFqicp1zlA/640?wx_fmt=jpeg&from=appmsg "")  
  
在 MIPS 中，寄存器$a0  
通常用作调用函数时的第一个参数（相当于 ARMv7 中的$r0  
）。因此，寻找从栈中加载数据到$a0  
寄存器的指令是一个很好的起点。从 0x00441250 开始，下面是显示的指令。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aV4MFxZv8L1ialsYFibBthv4abtS6j3OEz6qBy43tlMBMOkibianO0iaEmTjQ/640?wx_fmt=jpeg&from=appmsg "")  
  
逐行分析，以下是发生的情况：  
- $a0  
被栈上偏移量 0x20 处的一个 DWORD 填充。  
  
- 如果$a0  
为 NULL，则跳过调用freeaddrinfo()  
的分支。  
  
- 如果$a0  
不为 NULL，则将值传递给freeaddrinfo()  
。  
  
- 这是一个典型的未初始化栈指针漏洞。如果可以将任意值写入该栈帧，那么一个未定义的值可能会被传递给freeaddrinfo()  
。  
  
第二次对$a0  
的引用看起来像是用于另一个函数，这个函数可能会初始化这个指针，但反汇编显示，在调用free()  
之前，$a0  
寄存器被$s4  
寄存器的值覆盖。（注意：分析这段代码时，不要忘记考虑延迟槽 ;)）  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVIHiciaRqzZWKq1h8LZhM0Dt1lXQkcoXFL68kXLuwXA0NN2R6aaRBqnww/640?wx_fmt=jpeg&from=appmsg "")  
  
下一步是找到一个与这个栈帧重叠的函数，以便我们能够实现任意释放原语（arbitrary free primitive）。  
## 寻找重叠函数  
  
向后分析，我们发现有两个函数在调用sub_4409d4  
之前被调用：  
1. sub_443124 addiu $sp, $sp, -0x20  
  
1. sub_4419f4 addiu $sp, $sp, -0xa0  
  
1. sub_4409d4 addiu $sp, $sp, -0xa0  
  
当sub_4409d4  
被调用时，栈被调整了总计 -0x160。因此，我们需要找到一个函数，它的栈空间重叠区域位于-0x160 + 0x20  
，以便访问未初始化的区域。  
  
我首先想要分析的是这个二进制中的 SSDP 栈如何工作，因为它是 UPnP 协议的一部分。通过查找字符串M-SEARCH  
，发现了函数sub_444d84  
。再次通过利用 HLIL（High-Level Intermediate Language），明显看出这个函数负责解析通过 UDP 多播发送的 SSDP 请求。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVGd4JNIBa3cIzjvR4jwia9pr1b7PibwqosgOjpKFSmACXmFGcoiaUHrpyQ/640?wx_fmt=jpeg&from=appmsg "")  
  
真正引起我注意的是目标缓冲区（buf）的大小，特别是因为它遵循了在长度参数中使用宏sizeof(buf)-1  
的模式，这让我觉得一个 1600 字节的基于栈的变量正被用来接收 SSDP 数据。当查看反汇编代码时，可以看到栈被调整了-0x6a8  
，而且buf  
参数确实是一个 1600 字节的栈缓冲区。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVicFMiaRqITsKX9H5g5OH8UBJ7o8xjaoIHN6nE6ycII390v7e0WGViaMJA/640?wx_fmt=jpeg&from=appmsg "")  
  
SSDP 函数的栈帧位于-0x6a8  
，用于recvfrom()  
的buf  
位于-0x6a8 + 0x34  
，而我们的易受攻击的缓冲区位于-0x160 + 0x20  
。recvfrom()  
函数会从 UDP 套接字中读取最多 1599 字节，即 0x63f（十六进制）。如果我们填满整个char buf[1600]  
缓冲区，那么我们将从-0x674  
写到-0x35  
，这意味着这个基于栈的缓冲区与我们易受攻击的函数重叠。由于recvfrom()  
没有字符限制，因此它是一个很好的起点！  
## 触发漏洞  
  
从之前的分析中可以确定，易受攻击的函数位于 GENA 栈中，并且可以提供一个强大的原语，但我们需要从易受攻击的freeaddrinfo()  
调用开始倒推，弄清楚如何到达那里。我最初使用 HLIL 来加速这个过程，以下是触发漏洞区域的调用流程。  
  
从函数入口开始，似乎有一个检查字符串wps_event  
的过程，按照推测，这可能是订阅的 URI。（例如：SUBSCRIBE /wps_event HTTP/1.1  
）。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVuXUlj0eial5dKE26CV5tPYEEjibvIeOiaImiaomOQkkIeYa4eCVY48Fv4Q/640?wx_fmt=jpeg&from=appmsg "")  
  
为了简化这篇博客文章，接下来的检查会查找CALLBACK:  
和NT:  
这两个头部，它们必须出现在原始订阅请求中（这在 UPnP 协议 PDF 中定义）。在解析CALLBACK:  
之前，NT:  
的值必须被设置为upnp:event  
。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVPk9Jd1Ts9vvdxmazhvPubBojxNXGX4yk1vpcdyVqwt7eCJ7xdyo8pg/640?wx_fmt=jpeg&from=appmsg "")  
  
一旦通过了对子字符串upnp:event  
的检查（即strncasecmp()  
返回 0），接下来就会解析CALLBACK  
HTTP 头部的值。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVEaFBaVt1gBRtC55fYMdWH9H4BKibRlY4IsXyRKEXficHHhUCmE6nXtkQ/640?wx_fmt=jpeg&from=appmsg "")  
CALLBACK  
头部的格式如下：  
```
CALLBACK: <http://{host}:{port}/{path}>

```  
  
但是如果strncasecmp()  
返回非 NULL 呢？这意味着可以将CALLBACK  
头部中的http://  
字符串更改为其他任何内容。（例如：0wl://  
）。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVCCqswA3O7eyGBs8lUNAY3ZicSatmAQM9WsMvFGSH06K6gWP1icK9Sp0w/640?wx_fmt=jpeg&from=appmsg "")  
  
哇！根据 HLIL 输出，似乎有一个需要避免的值被设置了？但当切换回反汇编模式时，很明显 HLIL 可能有点误导。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVRF9ALHuu8IdAyQlsjCeMZosqB2v1GzOIPhekH0Qqx8r2AnHs6b2JAw/640?wx_fmt=jpeg&from=appmsg "")  
  
  
寄存器 $a0 似乎被加载了一个位于偏移量 0x68 的局部变量，但如果分支没有被执行，那么 $a0 将被 0x20 的值覆盖，这就是易受攻击的偏移量。  
  
根据所有这些信息，看起来如果我们发送以下请求，我们应该会导致崩溃：  
  
SSDP: （填充重叠的堆栈框架）  
  
(M-SEARCH * HTTP/1.1) + ("A" * 偏移量) + (DWORD for freeaddrinfo())  
GENA: （触发对 freeaddrinfo() 的调用）  
  
SUBSCRIBE /wps_event HTTP/1.1  
NT: upnp:event  
CALLBACK: <0wl://>  
注意：在 UPnP 规范中，SUBSCRIBE 请求必须将 HTTP 头部 TIMEOUT 设置为 Second-{int}，但解析器会删除此要求并将超时时间设置为 1801 秒。我个人在其他 UPnP 实现中从未见过这种异常。  
  
在 GDB 附加到 hostapd 后，发送了两个请求，导致以下结果：  
  
程序接收到信号 SIGSEGV，段错误。  
0x2ab734cc 中的 ?? ()  
(gdb) x/1i $pc  
=> 0x2ab734cc: jalr t9  
0x2ab734d0: lw s0,28(s0)  
(gdb) i r $s0 $a0  
s0: 0x41424344  
a0: 0x41424344  
  
这看起来就是我们所追求的崩溃！但我们在哪里呢？！通过调出内存映射，$PC 的值位于 /lib/libuClibc-0.9.30.so 中，地址范围为 0x2ab2d000-0x2ab8a000。  
  
起始地址 结束地址 大小 偏移量 对象文件  
0x400000 0x45d000 0x5d000 0x0 /sbin/hostapd  
0x46d000 0x46e000 0x1000 0x5d000 /sbin/hostapd  
0x46e000 0x47c000 0xe000 0x0 [堆]  
0x2aaa8000 0x2aaad000 0x5000 0x0 /lib/ld-uClibc-0.9.30.so  
0x2aaad000 0x2aaae000 0x1000 0x0  
0x2aabc000 0x2aabd000 0x1000 0x4000 /lib/ld-uClibc-0.9.30.so  
0x2aabd000 0x2aabe000 0x1000 0x5000 /lib/ld-uClibc-0.9.30.so  
0x2aabe000 0x2aae2000 0x24000 0x0 /lib/libwpa_common.so  
0x2aae2000 0x2aaf1000 0xf000 0x0  
0x2aaf1000 0x2aaf2000 0x1000 0x23000 /lib/libwpa_common.so  
0x2aaf2000 0x2ab1c000 0x2a000 0x0 /lib/libgcc_s.so.1  
0x2ab1c000 0x2ab2c000 0x10000 0x0  
0x2ab2c000 0x2ab2d000 0x1000 0x2a000 /lib/libgcc_s.so.1  
0x2ab2d000 0x2ab8a000 0x5d000 0x0 /lib/libuClibc-0.9.30.so  
0x2ab8a000 0x2ab99000 0xf000 0x0  
0x2ab99000 0x2ab9a000 0x1000 0x5c000 /lib/libuClibc-0.9.30.so  
0x2ab9a000 0x2ab9b000 0x1000 0x5d000 /lib/libuClibc-0.9.30.so  
0x2ab9b000 0x2aba0000 0x5000 0x0  
0x7fd5b000 0x7fd70000 0x15000 0x0 [栈]  
  
通过将基地址减去 $PC 的值（0x2ab734cc - 0x2ab2d000），我们得到了偏移量 0x464cc。将 /lib/libuClibc-0.9.30.so 加载到 Binary Ninja 中并跳转到偏移量 0x464cc，可以看到 $PC 当前指向的函数是 freeaddrinfo()。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVXw7CKtu8lbTvqjyWU3c5FCgdUVCNrBxXJ99813v2ZaoskgvwIxxOWQ/640?wx_fmt=jpeg&from=appmsg "")  
  
这是为那些喜欢图形视图的人提供的另一种视角。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVURIfgq1dVFibT92M3wX6N27HKamM1AMmLTicib2vHibElibGCRwpsxf8yzw/640?wx_fmt=jpeg&from=appmsg "")  
  
进入freeaddrinfo()  
时，$a0 的值被复制到寄存器 $s0 中，然后它不断循环，直到 $s0 等于 0x00000000。这意味着该函数释放了一个链表，该链表的next  
指针位于偏移量 0x1c。如果解引用的值 0x1c($s0) 被设置为 NULL，那么将执行返回分支。  
  
从这段代码片段来看，我们似乎可以释放任意分配的内存，但偏移量 0x1c 的值必须设置为 NULL 或设置为可以释放的分配。这实际上限制了可以释放的结构体数量。因此，如果尝试利用此漏洞来引发 UaF（使用后释放）受限，还有什么可以做的呢？  
## 利用  
  
在讨论如何实现 $PC 控制之前，首先需要回顾一些失败的思路：  
  
每个 TCP 连接都包含一个分配，其中有一个结构体，结构体内包含函数指针！不幸的是，在偏移量 0x1c 处有一个整数，它并不指向已分配的内存  
订阅也在堆中分配！但不幸的是，这些分配在偏移量 0x1C 处也有一些值，指向的不是有效的内存地址。  
回到那个 TCP 连接的结构体。与其尝试执行 UaF（使用后释放），不如尝试在我们控制的分配中进行嵌套分配？在走这条曲折的路之前，重要的是要检查环境中的安全防护措施（例如 ASLR、NX、CFG 等）。  
## 环境  
  
检查进程的映射会立即显示是否启用了 NX。  
```
# cat /proc/465/maps
00400000-0045d000 r-xp 00000000 1f:02 239        /sbin/hostapd
0046d000-0046e000 rw-p 0005d000 1f:02 239        /sbin/hostapd
0046e000-00473000 rwxp 00000000 00:00 0          [heap]
[removed]
7fa06000-7fa1b000 rwxp 00000000 00:00 0          [stack]

```  
  
栈和堆是可执行的！这就像 Windows XP SP2 之前的时代！下一个检查是查找 ASLR，可以通过查看randomize_va_space  
的值来找到。  
```
# cat /proc/sys/kernel/randomize_va_space 
1

```  
  
值为 1 表示“随机化栈、虚拟动态共享对象（VDSO）页和共享内存区域的位置”。数据段的基地址位于可执行代码段的末尾之后。但有一个问题。经过多次重启和重刷后，似乎这个路由器上的 1 表示 ASLR 完全关闭！为那些处理网络流量的设备庆祝，它们的安全性比 Windows Vista 还差。  
## 利用（续）  
  
回顾一下，已经确定以下几点：  
- ASLR 和 NX 都关闭了！  
  
- 堆的地址起始位置始终相同。  
  
- 漏洞没有字符限制。  
  
- 这是完美的暴风雨  
之前提出的制作假忙碌堆分配的想法似乎可以实现，但如何做，在哪里做呢？！  
  
通过纯黑盒测试发现，POST 消息的主体总是位于堆地址 0x46e100，并且也没有字符限制！  
  
为了制作假堆分配，需要查看free()  
函数，看看它需要什么才能成功释放一个分配。但为了加快这篇博客的进度，以下是必需的：  
- 分配 -4 必须包含大小和使用标志（LSB）。  
  
- 分配 -8 必须设置为 NULL，以简化操作。  
  
- 分配必须通过 unlink 检查。  
  
如果满足这些条件，则该分配将被释放并放入空闲列表中进行分桶。由于可以完全控制假分配，下一步是查找包含函数指针的结构体，这些指针可能被篡改。之前提到过，TCP 连接在堆中分配包含函数指针的结构体，但它们是如何触发的呢？  
  
进入的 TCP 连接由hostapd  
中的sub_44004c  
函数处理。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVnnQEL7KSePxDynicjTib6pFeKlE2usjwltURFqL9MNXo8nmUqIkGqUqg/640?wx_fmt=jpeg&from=appmsg "")  
  
如果调用accept()  
成功，则会调用httpread_create()  
。  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVybcIUepgpaMrlLJs5lichgQS37icSMnDnvrTOnzz6CZcicwDuON86CF4A/640?wx_fmt=jpeg&from=appmsg "")  
  
eloop_register_timeout()  
函数是分配包含函数指针的结构体的函数，如下所示。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVOm0dd1NPnPAKgoIs2o0PMWFmz8TEp42Lh6iblKv1lhwUAfcYNOs7g3g/640?wx_fmt=jpeg&from=appmsg "")  
  
函数指针保存在偏移量 0x10 处，而超时时间值位于偏移量 0x00 处。eloop_run()  
函数负责处理这些分配，以确保 TCP 连接在 30 秒无活动后被销毁。一旦超时发生，偏移量 0x10 处的函数指针将被调用。  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVbC0fC4MjXgpYGicW4bbN6JuZAX5VGhic7NuDmocOibddgF0W5Ha998ZaA/640?wx_fmt=jpeg&from=appmsg "")  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXdl8nia4BicSevibNs0mk13aVbC0fC4MjXgpYGicW4bbN6JuZAX5VGhic7NuDmocOibddgF0W5Ha998ZaA/640?wx_fmt=jpeg&from=appmsg "")  
  
  
  
  
**内部小圈子详情介绍**  
  
  
  
我们是  
神农安全  
，点赞 + 在看  
 铁铁们点起来，最后祝大家都能心想事成、发大财、行大运。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mngWTkJEOYJDOsevNTXW8ERI6DU2dZSH3Wd1AqGpw29ibCuYsmdMhUraS4MsYwyjuoB8eIFIicvoVuazwCV79t8A/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap086iau0Y0jfCXicYKq3CCX9qSib3Xlb2CWzYLOn4icaWruKmYMvqSgk1I0Aw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**内部圈子介绍**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap08Z60FsVfKEBeQVmcSg1YS1uop1o9V1uibicy1tXCD6tMvzTjeGt34qr3g/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
**圈子专注于更新src/红蓝攻防相关：**  
  
```
1、维护更新src专项漏洞知识库，包含原理、挖掘技巧、实战案例
2、知识星球专属微信“小圈子交流群”
3、微信小群一起挖洞
4、内部团队专属EDUSRC证书站漏洞报告
5、分享src优质视频课程（企业src/EDUSRC/红蓝队攻防）
6、分享src挖掘技巧tips
7、不定期有众测、渗透测试项目（一起挣钱）
8、不定期有工作招聘内推（工作/护网内推）
9、送全国职业技能大赛环境+WP解析（比赛拿奖）
```  
  
  
  
  
**内部圈子**  
**专栏介绍**  
  
知识星球内部共享资料截屏详情如下  
  
（只要没有特殊情况，每天都保持更新）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibRSekfPpgmzg6Pn4yH440wEZhQZaJaxJds7olZp5H8Ma4PicQFclzGbQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibgpeLSDuggy2U7TJWF3h7Af8JibBG0jA5fIyaYNUa2ODeG1r5DoOibAXA/640?wx_fmt=png&from=appmsg "")  
  
  
**知识星球——**  
**神农安全**  
  
星球现价   
￥45元  
  
如果你觉得应该加入，就不要犹豫，价格只会上涨，不会下跌  
  
星球人数少于1000人 45元/年  
  
星球人数少于1200人 65元/年  
  
（新人优惠卷20，扫码或者私信我即可领取）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXWwRdxNQnWo5Eq7BzgsIKogHTNRKIZQVcM0QQE3wbFrFciafzrEaRcia7gkRFb4vujBubqic3sPIN1g/640?wx_fmt=png&from=appmsg "")  
  
欢迎加入星球一起交流，券后价仅45元！！！ 即将满1000人涨价  
  
长期  
更新，更多的0day/1day漏洞POC/EXP  
  
  
  
**内部知识库--**  
**（持续更新中）**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFu12KTxgSfI69k7BChztff43VObUMsvvLyqsCRYoQnRKg1ibD7A0U3bQ/640?wx_fmt=png&from=appmsg "")  
  
  
**知识库部分大纲目录如下：**  
  
知识库跟  
知识星球联动，基本上每天保持  
更新，满足圈友的需求  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFhXF33IuCNWh4QOXjMyjshticibyeTV3ZmhJeGias5J14egV36UGXvwGSA/640?wx_fmt=png&from=appmsg "")  
  
  
知识库和知识星球有师傅们关注的  
EDUSRC  
和  
CNVD相关内容（内部资料）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFKDNucibvibBty5UMNwpjeq1ToHpicPxpNwvRNj3JzWlz4QT1kbFqEdnaA/640?wx_fmt=png&from=appmsg "")  
  
  
还有网上流出来的各种  
SRC/CTF等课程视频  
  
量大管饱，扫描下面的知识星球二维码加入即可  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFxYMxoc1ViciafayxiaK0Z26g1kfbVDybCO8R88lqYQvOiaFgQ8fjOJEjxA/640?wx_fmt=png&from=appmsg "")  
  
  
  
不会挖CNVD？不会挖EDURC？不会挖企业SRC？不会打nday和通杀漏洞？  
  
直接加入我们小圈子：  
知识星球+内部圈子交流群+知识库  
  
快来吧！！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUMULI8zm64NrH1pNBpf6yJ5wUOL9GnsxoXibKezHTjL6Yvuw6y8nm5ibyL388DdDFvuAtGypahRevg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWUMULI8zm64NrH1pNBpf6yJO0FHgdr6ach2iaibDRwicrB3Ct1WWhg9PA0fPw2J1icGjQgKENYDozpVJg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
  
神农安全知识库内部配置很多  
内部工具和资料💾，  
玄机靶场邀请码+EDUSRC邀请码等等  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXjm2h60OalGLbwrsEO8gJDNtEt0PfMwXQRzn9EDBdibLWNDZXVVjog7wDlAUK1h3Y7OicPQCYaw2eA/640?wx_fmt=png&from=appmsg "")  
  
  
快要护网来临，是不是需要  
护网面试题汇总  
？  
问题+答案（超级详细🔎）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXjm2h60OalGLbwrsEO8gJDbLia1oCDxSyuY4j0ooxgqOibabZUDCibIzicM6SL2CMuAAa1Qe4UIRdq1g/640?wx_fmt=png&from=appmsg "")  
  
  
最后，师傅们也是希望找个  
好工作，那么常见的  
渗透测试/安服工程师/驻场面试题目，你值得拥有！！！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXjm2h60OalGLbwrsEO8gJDicYew8gfSB3nicq9RFgJIKFG1UWyC6ibgpialR2UZlicW3mOBqVib7SLyDtQ/640?wx_fmt=png&from=appmsg "")  
  
  
内部小圈子——  
圈友反馈  
（  
良心价格  
）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWW0s5638ehXF2YQEqibt8Hviaqs0Uv6F4NTNkTKDictgOV445RLkia2rFg6s6eYTSaDunVaRF41qBibY1A/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWW0s5638ehXF2YQEqibt8HviaRhLXFayW3gyfu2eQDCicyctmplJfuMicVibquicNB3Bjdt0Ukhp8ib1G5aQ/640?wx_fmt=png&from=appmsg "")  
  
  
****  
**神农安全公开交流群**  
  
有需要的师傅们直接扫描文章二维码加入，然后要是后面群聊二维码扫描加入不了的师傅们，直接扫描文章开头的二维码加我（备注加群）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXWwRdxNQnWo5Eq7BzgsIKovBgx57dc6Ql2yRSPBJGA5fde4sQJzOomD1GURVibZeCNzXM6iaGrSe8Q/640?wx_fmt=jpeg&from=appmsg "")  
  
    
```
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/b7iaH1LtiaKWW8vxK39q53Q3oictKW3VAXz4Qht144X0wjJcOMqPwhnh3ptlbTtxDvNMF8NJA6XbDcljZBsibalsVQ/640?wx_fmt=gif "")  
  
  
  
