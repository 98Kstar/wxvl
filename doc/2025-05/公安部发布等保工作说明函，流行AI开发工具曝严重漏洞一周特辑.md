#  公安部发布等保工作说明函，流行AI开发工具曝严重漏洞|一周特辑   
 威努特安全网络   2025-05-09 23:59  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/vEkwp3V9Utv2FibG3uJsseaUm7CUdAwnOhWIhbCst7qqNbCic5ib6TZOcxZePOz1dDuG3X2mdc8f4wib6mzoibicFibkQ/640?wx_fmt=gif&from=appmsg "")  
  
  
  
  
**公安部针对等保工作发布说明函**  
  
  
  
  
近期，公安部发布公网安〔2025〕1846号文件，对3月8日部署的网络安全等级保护工作进行进一步说明，要求各级单位深化系统备案更新、数据资源摸底及风险整改，并对  
“如何执行系统备案动态更新工作”等部分关键问题进行了指导说明。[点击此处](https://mp.weixin.qq.com/s?__biz=MzU5OTQ0NzY3Ng==&mid=2247499500&idx=1&sn=6ee4bf940851d1ad89d98e7e2bb79a59&scene=21#wechat_redirect)  
可跳转官方通知，查看说明函原文，了解更多详细信息。  
  
  
  
  
**网信办通报15款涉及侵权APP**  
  
  
  
  
根据中央网信办、工业和信息化部、公安部、市场监管总局联合发布的《关于开展2025年个人信息保护系列专项行动的公告》，依据法律法规和有关规定，中央网信办组织对App、SDK收集使用个人信息行为进行检测，通报了包括  
墨迹天气tv版、  
医家等15款App存在未逐一列出收集使用个人信息的SDK，未准确列出SDK收集使用个人信息的目的、方式、范围等问题。  
  
另外，CTP穿透采集、金仕达穿透采集等16款SDK收集使用个人信息，存在未提供个人信息收集使用规则，未说明自行或协助App响应用户个人信息权利请求的措施，未及时响应用户个人信息投诉举报等权利请求等问题。  
  
详细的通报信息可[点击此处](https://mp.weixin.qq.com/s?__biz=MzAwMjU0MjIyNw==&mid=2651507970&idx=1&sn=8039f0d7d0324f52fc342e9c5adf0bf7&scene=21#wechat_redirect)  
查看。  
  
  
  
  
**流行AI开发工具曝严重漏洞**  
  
**服务器可被完全控制**  
  
  
  
  
开源的AI工具Langflow（用于快速构建聊天机器人等应用）存在无需密码即可远程执行代码的漏洞（CVE-2025-3248）。黑客能通过API接口直接接管服务器，全球至少500台服务器暴露在风险中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vEkwp3V9Utv2FibG3uJsseaUm7CUdAwnOFrYiafKJJmUI5ibdQu06AicgC4Ote6eZtMc5iamDPH557xianOaVJEYlPqQ/640?wx_fmt=png&from=appmsg "")  
  
Horizon3 的 PoC 漏洞利用正在行动（来源：Horizon3）  
  
Langflow用户多为开发者，服务器可能存储AI模型和敏感数据，因此该漏洞存在非常严重的安全隐患。美国网络安全局（CISA）已要求联邦机构紧急修复漏洞，并建议用户升级至Langflow 1.3.0以上版本，或通过防火墙限制访问，以降低风险。    
  
  
  
  
**三星数字广告牌系统遭黑客攻击**  
  
**漏洞已被广泛利用**  
  
  
  
  
三星MagicINFO系统（用于管理商场、机场等场所的广告显示屏）存在高危漏洞（CVE-2024-7399），黑客无需密码即可远程入侵设备并安装恶意软件。该漏洞在去年即被曝光，但由于部分用户并未及时更新，导致近期攻击激增。    
  
攻击者可能通过该漏洞篡改广告内容或窃取连接设备的数据，目前已发现存在僵尸网络利用该漏洞控制设备，因此管理员需尽快升级至MagicINFO 21.1050及以上版本。  
  
  
  
  
**Windows日志漏洞被勒索软件利用**  
  
**多国企业中招**  
  
  
  
  
微软近期确认Windows通用日志文件系统（CLFS）中的高危漏洞（CVE-2025-29824）正被Play勒索软件团伙积极利用。该漏洞允许攻击者获得SYSTEM最高权限，已造成美国IT公司、西班牙软件企业、沙特零售集团等跨国组织受害。  
  
利用该漏洞，攻击者通过钓鱼邮件投放PipeMagic后门，利用0day漏洞突破权限隔离，使用Grixba信息窃取工具扫描内网，并最终部署PlayCrypt勒索软件加密文件。  
  
目前微软已发布补丁，但未及时更新的系统仍面临风险，建议用户立即安装微软2025年4月安全更新以降低风险。  
  
  
  
  
**英国Co-op超市遭黑客攻击**  
  
**客户信息泄露**  
  
  
  
  
英国零售巨头Co-op确认遭遇勒索软件攻击，黑客窃取了大量现有及历史客户的个人信息（如姓名、联系方式），但密码、银行卡等敏感数据未受影响。  
  
上月底，黑客通过伪造员工密码重置入侵系统，并窃取了Windows账户的密码数据库文件，该手法与近期其他大型企业攻击类似（如社会工程欺骗员工）。最初公司低估了此次攻击的严重性，该事件的实际影响范围远远超出预期。  
  
目前Co-op正与微软合作进行系统重建，并加强安全防护。Co-op建议客户需警惕钓鱼邮件或诈骗电话，但无需恐慌银行卡信息泄露。    
  
  
  
  
**电商平台Magento扩展被植入后门**  
  
**超500家店铺受影响**  
  
  
  
  
近日，安全公司发现，21款Magento电商插件扩展程序被黑客植入了恶意代码，导致全球500-1000家在线商店遭到入侵，包括一家市值400亿美元的跨国企业。这些后门最早可追溯至2019年，但它们直到近期才被激活。  
  
黑客通过受污染的插件，可以远程控制商家网站，可能窃取商家的订单和支付数据。部分插件已存在后门多年，说明供应链攻击隐蔽性极强。商家应立即检查使用的Magento插件来源，并将他们更新至官方最新版本。    
  
  
  
  
**医疗设备商Masimo遭网络攻击**  
  
**生产交付延迟**  
  
  
  
  
近日，知名医疗监测设备制造商Masimo因网络攻击关闭了部分系统，导致其生产停滞、订单延误。黑客在此次攻击中入侵了该公司的本地网络，其云端服务未受影响。    
  
该攻击可能影响医院的血氧仪等设备供应，但公司尚未确认是否泄露了患者数据。Masimo正与网络安全公司及执法部门合作调查该事件。    
  
  
  
  
**风投公司Insight Partners数据泄露**  
  
**投资人信息被盗**  
  
  
  
  
管理900亿美元资产的风投巨头Insight Partners披露，它们遭受到了一次社会工程攻击，员工及投资人敏感信息（如银行账户、税务记录）被盗。受影响个人应启用双重验证、监控账户异常，并警惕钓鱼邮件。    
  
  
  
  
**教育巨头培生遭黑客入侵**  
  
**部分历史数据泄露**  
  
  
  
  
全球最大教育出版公司培生（Pearson）确认遭到网络攻击，部分“遗留数据”被盗，被盗数据包含1900-2010年间学术期刊投稿记录、已停用的MyLab线上平台用户资料及绝版教科书数字底稿等，但称不涉及员工或近期客户信息。事件可能波及使用培生在线教育平台的学校。    
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vEkwp3V9Utv2FibG3uJsseaUm7CUdAwnOjkia6I2tib0Nb1eF0Olib2D4CXAWDkAOQ5RxjY77kgQH0MQicagxK0bfYw/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/vEkwp3V9Utv2FibG3uJsseaUm7CUdAwnO2icDc4ictOr2Qgrj8fdkdLaPzrKonMzj5Bb4ec8ic7w1ucJQes5peNluA/640?wx_fmt=jpeg&from=appmsg "")  
  
渠道合作咨询   田先生 15611262709  
  
稿件合作   微信:shushu12121  
  
