#  漏洞预警 | IBM WebSphere远程代码执行漏洞  
浅安  浅安安全   2025-07-08 00:00  
  
**0x00 漏洞编号**  
- CVE-2025-36038  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
IBM WebSphere是IBM提供的一套企业级中间件平台，主要用于构建、部署和管理基于Java的应用程序。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SVGBiaficDylnXhWUEY2CkYiavEGWnwgvVtvWlnU7pwBVGQpeh2h06W0Xibiciaa2KOacLZ7uOAS8P5gR9A/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
**0x03 漏洞详情**  
###   
  
**CVE-2025-36038**  
  
**漏洞类型：**  
远程代码执行  
  
**影响：**  
执行任意代码  
  
**简述：**  
IBM WebSphere Application Server存在远程代码执行漏洞，由于系统对不受信任数据反序列化处理不当，攻击者可通过构造特定序列化对象，在无需认证和用户交互的情况下远程执行任意代码，进而完全控制受影响系统。  
  
**0x04 影响版本**  
- 8.5.0.0 <= IBM WebSphere Application Server <= 8.5.5.27  
  
- 9.0.0.0 <= IBM WebSphere Application Server <= 9.0.5.24  
  
**0x05 POC状态**  
- 未公开  
  
**0x06****修复建议**  
  
******目前官方已发布漏洞修复版本，建议用户升级到安全版本****：******  
  
https://www.ibm.com/  
  
  
  
