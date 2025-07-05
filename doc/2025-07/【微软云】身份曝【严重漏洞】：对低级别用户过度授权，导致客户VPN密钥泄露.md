#  【微软云】身份曝【严重漏洞】：对低级别用户过度授权，导致客户VPN密钥泄露  
安全内参编译  安小圈   2025-07-05 00:45  
  
**安小圈**  
  
  
第702期  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/FzZb53e8g7vI437xjqWcNyOqqwqLp91PIWOUkeSJhzu6HT2QVMuiaugoC6icsQ5RLzAzjicgywHzBGad8lIpR6w1g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
![](http://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbOicgoB4iaTMSrN8wJWQ6zA8wp7eayt7VT0AYyCP4QZp5TvoaxVyKRXvUzLc4MRAon231C3egjn9ZpQ/640?wx_fmt=jpeg "")  
  
Azure通过HTTP方法区分权限控制。只读操作使用GET，而访问敏感数据则需通过POST请求，但由于设计疏忽，VPN连接的共享密钥竟被设置为通过GET请求获取，从而绕过了应有的安全防护机制，使得  
被过度授权的  
最低级别读取权限也能获取VPN PSK。  
  
安全内参7月4日消息，以色列身份安全厂商Token Security安全专家近期开展了一项深入调查，揭示微软Azure云平台RBAC（基于角色的访问控制）架构中存在严重安全漏洞。  
  
Azure RBAC是该云平台权限管理的核心机制，允许管理员根据不同的范围（从整个订阅到具体资源）为用户、用户组或服务主体分配具有预定义权限的角色。  
  
然而调查发现，多个原本设计用于提供受限、特定服务访问权限的内置角色存在配置错误，其实际权限远远超出设定范围。  
  
包括“托管应用读取”和“日志分析读取”在内，有10个角色被错误地授予了过于宽泛的*/read权限，实际上等同于通用的“读取者”（Reader）角色。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/FzZb53e8g7vE38iaEkrUaY3vMGkJI1iabwMttu1IrcgmicLfacxeF77dYncjSMG7ic9wDuPwWicXbhHVYqmvcgLichfg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
图：角色分配  
  
这一问题导致用户能够访问所有Azure资源的敏感元数据，权限范围远超这些角色描述所设定的界限。  
  
权限的过度授予可能使攻击者得以从自动化账户中提取凭证、绘制网络配置图以辅助后续攻击，并在存储账户或备份保管库中发现关键数据，为权限提升和攻击部署创造有利条件。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/FzZb53e8g7vE38iaEkrUaY3vMGkJI1iabwzib8oITZOgofiaUHeSL79zg60gK2f1nicibibln4FQeqJldJlJCawaluhJg/640?wx_fmt=webp&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
图：过度授权的三类滥用方式，包括敏感数据发现、凭据窃取、攻击规划  
  
  
**利用Azure API泄露VPN预共享密钥**  
  
更严重的是，研究人员还发现Azure API中存在一个关键漏洞，仅凭读取权限即可泄露VPN网关的预共享密钥（PSK）。  
  
通常，Azure通过HTTP方法区分权限控制。只读操作使用GET，而访问敏感数据则需通过POST请求，以防止未经授权的访问。  
  
然而，由于API设计上的疏漏，VPN连接的共享密钥竟被设置为通过GET请求获取，从而绕过了应有的安全防护机制。  
  
这一漏洞使得攻击者即便仅拥有最低级别的读取权限（通常由上述过度授权的角色授予），也能够获取站点到站点（S2S）VPN连接的PSK。  
  
一旦获取该密钥，恶意行为者便可建立恶意连接，进而未经授权地访问内部云资源、虚拟私有云（VPC）乃至通过Azure VPN网关连接的本地网络。  
  
这一漏洞将本应无害的读取权限转变为入侵网络的入口。在云与本地系统深度融合的混合环境中，其后果尤为严重。  
  
  
**微软回应**  
  
漏洞披露后，微软将这些过度授权的内置角色定性为“低严重性”问题，仅选择更新相关文档，而未限制其权限设置，致使组织仍面临角色被滥用的风险。  
  
相比之下，VPN PSK泄露问题被认定为“严重”漏洞并迅速修复。现在访问密钥必须具备特定权限  
（Microsoft.Network/connections/sharedKey/action），同时微软还向漏洞发现者支付了7500美元的漏洞赏金。  
  
为防范类似威胁，组织应主动审计并限制上述已识别的过度授权角色的使用，改为基于最小必要权限原则创建自定义角色。  
  
同时，应将角色权限范围限制在具体资源或资源组内，而非整个订阅，从而进一步降低潜在风险。  
  
云安全是服务提供商与客户共同承担的责任。此次事件再次提醒我们：对平台工具的盲目信任可能酿成严重安全后果。  
  
要实现稳健的安全防护，必须持续监控和验证权限配置，防止基于身份的攻击在Azure环境中发生。  
  
  
**参考资料：gbhackers.com**  
  
  
  
  
END  
  
  
  
**【内容**  
**来源：安全内参】**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/BWicoRISLtbMSrNYPzeZSs4X316kGV7UeeR4VInT56J0KCLD3HkiaRxjMLLV6rricOadHohJB1sOtPT02fETAxr4g/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/0YKrGhCM6DbI5sicoDspb3HUwMHQe6dGezfswja0iaLicSyzCoK5KITRFqkPyKJibbhkNOlZ3VpQVxZJcfKQvwqNLg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546898&idx=1&sn=2f16da5665014b4c07bcbd53e3d1c03e&scene=21#wechat_redirect)  
- [【HW】8个因护网被开除的网安人](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546898&idx=1&sn=2f16da5665014b4c07bcbd53e3d1c03e&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546898&idx=2&sn=e9578e62a475ac5c46b95ac81066d2a7&scene=21#wechat_redirect)  
- [HW应急溯源：50个高级命令实战指南](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546898&idx=2&sn=e9578e62a475ac5c46b95ac81066d2a7&scene=21#wechat_redirect)  
  
  
- [震惊全球！中国团队攻破RSA加密！RSA加密告急？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546856&idx=1&sn=11b36f6fabde860e889e4ac2f4797bba&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbOugegrykhydnkHibcSWjpibTBZoK6jjGxJiax1BcwwctpA5SBric9aPdQFXsxFnn4LQJWdkYwbtPN0gg/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546815&idx=1&sn=99a4f3228f322ef92c93d23cee01f071&scene=21#wechat_redirect)  
- [2025年【护网】攻防演练时间已定！](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546815&idx=1&sn=99a4f3228f322ef92c93d23cee01f071&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546578&idx=1&sn=87cdf84e6fd7d35986b29acd90954c65&scene=21#wechat_redirect)  
  
[突发！小红书惊现后门......](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546578&idx=1&sn=87cdf84e6fd7d35986b29acd90954c65&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbPwt82pEdc2YwCDz6n3H3c2C0ibcMl4Tea8hM59iaZoR1FDMTCUswDiclc1icLoSywpkWbdqyb6uBNcnA/640?wx_fmt=jpeg "")  
- [2025年“净网”“护网”专项工作部署会在京召开，看看都说了哪些与你我相关的关键内容？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546673&idx=1&sn=53fe0365785465d4ff6193a9ca639119&scene=21#wechat_redirect)  
  
  
- # 护网即将来临，这场网安盛会带给了我们打工人什么......  
  
- [护网在即，企业还有什么新思路可以应对吗？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546698&idx=1&sn=55ab4ac8dffb5f7ccf02a4f759537acf&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbPwt82pEdc2YwCDz6n3H3c2Noz3ibYqNZ52uicBtuVVlFRg6vSuF8YFjPvCVma1ADrT1ViaKVE9URNOA/640?wx_fmt=jpeg "")  
- [HW必备：50个应急响应常用命令速查手册一（实战收藏）](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546304&idx=2&sn=45ef99e528ded7ff2e65e4d70e6d5181&scene=21#wechat_redirect)  
  
  
- [HW必备：50个应急响应常用命令速查手册二（实战收藏）](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546327&idx=2&sn=cf1ebbd2b511524ec965a3672b6fc3dd&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546530&idx=1&sn=4a2820b60102e538e87c956375f6fcdb&scene=21#wechat_redirect)  
- [网安同行们，你们焦虑了吗？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546530&idx=1&sn=4a2820b60102e538e87c956375f6fcdb&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247546466&idx=1&sn=a9d55d0b430dbf61dc219fd71ce25ae1&scene=21#wechat_redirect)  
- # 网安公司最后那点体面，还剩下多少？  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545577&idx=1&sn=76a4dbd28d9c0e006b7790d89c2b1354&scene=21#wechat_redirect)  
- [2024年网安上市公司营收、毛利、净利润排行](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545577&idx=1&sn=76a4dbd28d9c0e006b7790d89c2b1354&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545311&idx=2&sn=bb8ff7cd42079bae40ab0a2e05ff37c1&scene=21#wechat_redirect)  
- [突发！数万台 Windows 蓝屏。。。。广联达。。。惹的祸。。。](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545311&idx=2&sn=bb8ff7cd42079bae40ab0a2e05ff37c1&scene=21#wechat_redirect)  
  
  
#   
- # 权威解答 | 国家网信办就：【数据出境】安全管理相关问题进行答复  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247539649&idx=1&sn=8858b449c89d21240e1f522e92be4fbd&scene=21#wechat_redirect)  
- # 全国首位！上海通过数据出境安全评估91个，合同备案443个  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544405&idx=2&sn=a961d43ca4a9ed667fccbbab758d9196&scene=21#wechat_redirect)  
- # 沈传宁：落实《网络数据安全管理条例》，提升全员数据安全意识  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545156&idx=1&sn=ee5292e9838b2a2112a94a9c7c683925&scene=21#wechat_redirect)  
  
- [频繁跳槽，只为投毒](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545156&idx=1&sn=ee5292e9838b2a2112a94a9c7c683925&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545017&idx=1&sn=b513c15f91d5de7a8fa33c4b3725706a&scene=21#wechat_redirect)  
- [【高危漏洞】Windows 11：300毫秒即可提权至管理员](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247545017&idx=1&sn=b513c15f91d5de7a8fa33c4b3725706a&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544601&idx=1&sn=e230574b0535e6005b830d086cdcf867&scene=21#wechat_redirect)  
- [针对网安一哥专门的钓鱼网站](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544601&idx=1&sn=e230574b0535e6005b830d086cdcf867&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544347&idx=1&sn=06311aa3f8aeba492f83224c652fe4a1&scene=21#wechat_redirect)  
  
- [为什么【驻场】网络安全服务已成为大多数网络安全厂商乙方不愿再触碰的逆鳞？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544347&idx=1&sn=06311aa3f8aeba492f83224c652fe4a1&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbOYPldtHVUmKQJ2WtL12GUnHRyzBiaKosLNicTZ2QkDFSRPUha2Eiaqk8R5fPdXc75zxprkTRB0ib5hUw/640?wx_fmt=jpeg "")  
- [HW流程以及岗位职责](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247544347&idx=3&sn=97e6083dbfbdd896680e24770a10d319&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247543989&idx=1&sn=2821b91efdd626e1a38ec6b2b439186b&scene=21#wechat_redirect)  
  
- # 网络安全【重保】| 实战指南：企业如何应对国家级护网行动？  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247542929&idx=1&sn=8cf6f15ddca44e343a494eea0fa619b2&scene=21#wechat_redirect)  
- **DeepSeek安全：AI网络安全评估与防护策略**  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247542701&idx=1&sn=567674aa12d861c3561d453268badb91&scene=21#wechat_redirect)  
- **虚拟机逃逸！VMware【高危漏洞】正被积极利用，国内公网暴露面最大******  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247542458&idx=1&sn=d81d049331d175a2176f0978d7f032a8&scene=21#wechat_redirect)  
- **挖矿病毒【应急响应】处置手册******  
  
****- **用Deepseek实现Web渗透自动化**  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247542225&idx=2&sn=244a465fab183f4fa91a284b92a920e6&scene=21#wechat_redirect)  
- **【风险】DeepSeek等大模型私有化服务器部署近九成在“裸奔”，已知漏洞赶紧处理！**  
  
****- **关于各大网安厂商推广「DeepSeek一体机」现象的深度分析**  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247541264&idx=1&sn=887bf392ba73e7c2c833a410e7168818&scene=21#wechat_redirect)  
- [Deepseek真的能搞定【安全运营】？](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247541264&idx=1&sn=887bf392ba73e7c2c833a410e7168818&scene=21#wechat_redirect)  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247540432&idx=1&sn=b9e7e6103e86b9966f29d7eacf8e3d1e&scene=21#wechat_redirect)  
- **【热点】哪些网络安全厂商接入了DeepSeek？**  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247540206&idx=2&sn=300737ad84f684e622fdde03da0fc1a7&scene=21#wechat_redirect)  
- **【2025】常见的网络安全服务大全（汇总详解）**  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg2MDg0ODg1NQ==&mid=2247540343&idx=1&sn=59d6f592f71a7f1e3a18fd082aa3de40&scene=21#wechat_redirect)  
- **AI 安全 |《人工智能安全标准体系(V1.0)》(征求意见稿)，附下载**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/BWicoRISLtbMbfUY7RtO1t6ZAxjoibZoZ8DSVPU0yI9v2nXpiat0oN8eLia5jiaoWOhlib5GiaPWQJeCsUmShI4QOqaGg/640?wx_fmt=png "")  
- **2025年 · 网络威胁趋势【预测】**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbM09kF5tXEb8PRXicFibPic4un6rwDI2CBUxrVaDINuM8ChyotgWiag4icErAHniaYNYiccQiaVkyyJUTX13w/640?wx_fmt=jpeg "")  
- **【实操】常见的安全事件及应急响应处**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbMASB7RibZ1nezrias4SvtcqzjvsJJPXhFiceJPEoVHVLhI2Soolaf8OhWQOVafycOibiaclJkT7NgG4Nw/640?wx_fmt=jpeg "")  
- **2024 网络安全人才实战能力白皮书安全测试评估篇**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/BWicoRISLtbMSrNYPzeZSs4X316kGV7UeOsnl5ayrQXc0wPVutL1dQXg7BugT7vAe8qkpfszTrlhUAq4DQZFaVA/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/BWicoRISLtbP7Bh21K85KEkXX7ibWmLdM2eafpPicoTqk37LEVMUKD1JuAic4FF4KB7jP4oFTricyMwvj5VUZZ824ww/640?wx_fmt=gif "")  
![](https://mmbiz.qpic.cn/mmbiz_jpg/BWicoRISLtbNzlia8CP45sjgLJgia5Y22hx8khBeShnAzCPwsfqeIVKkpFDhUoMUWMicq6toR2TSUmgBpgzZQHEAHw/640?wx_fmt=jpeg "")  
![](https://mmbiz.qpic.cn/mmbiz_png/BWicoRISLtbPFKyibwduMibC35MsIhibgZEAibwSyVRz7FKt3xa1UK61fXXCCUKllCXFrLdnBqcmgiaKeSxGrWT0RtYw/640?wx_fmt=png "")  
  
