#  聊热点｜AI本地部署的天坑？RAG曝出重大安全漏洞、AI开发工具Langflow高危漏洞遭活跃利用，可导致服务器任意代码执行……   
 中国电信安全   2025-05-09 07:38  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Dh3fqSPAOWco1mfGDsichHErDfemryHfWvicHaa6kKT6muuDJJhI46ckTic04hgyqvKglMEtwLCqZZwqibiaIItUibTw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1 "")  
  
**01**  
  
**行业动态**  
  
**AI本地部署的天坑？RAG曝出重大安全漏洞**  
  
  
  
在AI技术的快速发展中，检索增强生成（RAG）是当下公认的可提高企业AI准确性和可靠性的关键技术。RAG的初衷是通过从外部（通常是本地的知识库）数据库中检索相关内容，为大语言模型提供更接地气的上下文，从而减少模型幻觉并提升生成质量。然而，研究发现：RAG不仅未能增强AI的安全性，反而可能使大模型变得不安全，甚至导致模型“越狱”，这对企业AI的本地部署构成了全新的重大安全挑战。  
  
参考来源：GoUpSec  
  
**https://mp.weixin.qq.com/s/4PjyQZvGLS6MN0bc4acqWg**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dh3fqSPAOWf7o0q27xAQGukO7sMGpfm06jsLiaThh2ZiayEjdGqjRiaVAZqDJXQEX2eZBeqOFUxWLTzUic4Lars8yQ/640?wx_fmt=png "微信图片_20230601160940.png")  
  
**热评时刻：**  
  
RAG技术虽旨在提升AI准确性和可靠性，但彭博研究显示，当使用RAG时，大模型可能会出现不安全响应，甚至导致模型“越狱”。研究发现，RAG提供额外上下文或会绕过模型内置安全防护机制，使原本会拒绝有害查询的模型给出不安全回答。这或与大模型训练时未充分考虑长输入安全对齐有关。此外，现有通用AI安全框架在应对特定领域如金融服务的风险时存在局限性。企业需重新思考安全架构，将防护措施与RAG视为整体，设计能预见检索内容与模型安全机制互动的集成安全系统。同时，行业需开发特定领域风险分类法，以应对RAG带来的全新安全挑战。  
  
**—— 电信安全专家 王玮琪**  
  
  
**02**  
  
**国内外安全事件**  
  
**AI开发工具Langflow高危漏洞遭活跃利用，可导致服务器任意代码执行**  
  
  
#   
  
  
  
  
Langflow是一款基于Python的开源工具，用户可通过可视化界面和API服务器构建部署AI代理。在AI代理技术兴起的当下，该工具因能帮助企业利用大语言模型（LLMs）实现工作流自动化而广受欢迎，GitHub星标数近6万。该工具本身允许认证用户执行任意代码——用户通过可视化组件构建代理时，可直接修改底层Python代码。但其高危漏洞（CVE-2025-3248）被发现可使未认证用户也获得同等权限。更严重的是，目前互联网上暴露着500多个Langflow实例，还有更多部署在内网环境中。  
  
参考来源：  
FreeBuf  
  
**https://www.freebuf.com/articles/ai-security/430068.html**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dh3fqSPAOWf7o0q27xAQGukO7sMGpfm06jsLiaThh2ZiayEjdGqjRiaVAZqDJXQEX2eZBeqOFUxWLTzUic4Lars8yQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1 "微信图片_20230601160940.png")  
  
**热评时刻：**  
  
AI开发工具Langflow的高危漏洞（CVE-2025-3248）正被广泛利用，攻击者可通过未受保护的API端点执行任意Python代码。该漏洞源于/api/v1/validate/code端点缺失认证检查，且将代码传给exec函数执行。研究人员已通过Python装饰器特性实现远程代码执行，且利用代码已集成到Metasploit框架。目前，互联网上暴露的Langflow实例超500个，更多存在于内网。Langflow用户应立即升级至1.3.0或14.0版本。此事件凸显了AI工具部署时需加强访问控制与隔离措施，以降低安全风险。  
  
**—— 电信安全专家 王玮琪**  
  
  
**03**  
  
**前沿技术**  
  
**新型越狱攻击可突破ChatGPT、DeepSeek等主流AI服务防护**  
  
  
#   
  
  
  
  
研究人员最新发现的两项越狱技术暴露了当前主流生成式AI服务的安全防护存在系统性漏洞，受影响平台包括OpenAI的ChatGPT、谷歌的Gemini、微软的Copilot、深度求索（DeepSeek）、Anthropic的Claude、X平台的Grok、MetaAI以及MistralAI。这些越狱攻击可通过几乎相同的提示词在不同平台上执行，使攻击者能够绕过内置的内容审核和安全协议，生成非法或危险内容。其中名为"Inception"的技术利用嵌套虚构场景侵蚀AI的伦理边界，另一种技术则诱导AI透露其禁止响应内容后转向非法请求。  
  
参考来  
源：FreeBuf  
  
**https://www.freebuf.com/news/428850.html**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dh3fqSPAOWf7o0q27xAQGukO7sMGpfm06jsLiaThh2ZiayEjdGqjRiaVAZqDJXQEX2eZBeqOFUxWLTzUic4Lars8yQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1 "微信图片_20230601160940.png")  
  
**热评时刻：**  
  
文章指出有两项新的越狱技术可突破 ChatGPT、DeepSeek 等主流 AI 服务防护，暴露出当前主流生成式 AI 安全防护存在系统性漏洞。其中 “Inception” 技术通过嵌套虚构场景诱导 AI 生成违规内容，而另一种上下文绕过技术则利用 AI 上下文记忆绕过安全检查。攻击者借此能让 AI 生成非法或危险内容，危害极大。DeepSeek 认为这是传统越狱并非架构缺陷，其他厂商在内部调查更新。当前针对大模型的防护手段有局限，AI 开发者与攻击者的对抗将加剧，行业急需建立更强大的防御机制。**中国电信安全公司已推出星辰-见微大模型，可帮助广大用户构建安全大模型围栏，用AI红队构筑新时代人工智能安全边界。**  
  
  
**—— 电信安全专家 陈海龙**  
  
版权声明：转载及点评的所有文章、图片、音视频文件等资料的版权归版权所有权人所有。此篇采用的文章及图片等内容无法一一联系确认版权者。如涉及作品内容、版权和其它问题，请及时通过电子邮件通知我们以便迅速采取适当措施避免风险。  
联系邮箱：market@damddos.com  
  
**排版：**  
武云龙  
  
**校对：**  
李雪 陈师慧****  
  
**执行主编：**  
田金英  
  
**主编：**  
冯晓冬  
  
**内容整理：**  
市场经营部  
  
**推荐阅读**  
  
  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247534083&idx=3&sn=547aeb5e88542d8ce0ec2a9ed25fb7f0&scene=21#wechat_redirect)  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247534182&idx=2&sn=997c6de7a6e0bd226ef0acfce44006ea&scene=21#wechat_redirect)  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247534182&idx=2&sn=997c6de7a6e0bd226ef0acfce44006ea&scene=21#wechat_redirect)  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247535145&idx=2&sn=d6debef51876b8f9e2254636a8f297c3&scene=21#wechat_redirect)  
  
**聊热点｜工信部、国家标准委联合印发《国家智能制造标准体系建设指南 (2024版)》、科技界的萨拉热窝事件？CVE面临全球分裂……**  
  
**‍**  
  
  
****  
**聊热点｜国家发展改革委、国家数据局印发2025年数字经济工作要点、利用OpenAI GPT-4o绕过验证码，AI垃圾广告……**  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/z7xPqlc0GbB7ShHeKEtRTfattkj7T3bsyPnz68QvJ6lPVg7SFgrY5pYtEibJiao9WGNWEfmGicicWib041MYnu64GOA/640?wx_fmt=gif&from=appmsg "")  
  
  
  
  
  
  
  
  
  
