#  绿盟科技披露2024年报，营收增长超40%，亏损收窄；漏洞研究新突破：利用ChatGPT快速创建利用代码 | 牛览   
 安全牛   2025-04-23 08:49  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/kuIKKC9tNkBjwIRcuxcIx3fvDBydiaqkxw4o55dLPMJBKVsRbYl7ULkJDmFtatY9fWSIcZttiaeQm76cm0TXSBCA/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp "")  
  
**新闻速览**  
  
  
  
•英国Ofcom禁止"全球标题码"租赁，遏制移动网络犯罪  
  
•漏洞研究新突破：利用ChatGPT快速创建利用代码  
  
•从边缘设备入侵：黑客新战术瞄准中小企业网络薄弱环节  
  
•美国德州阿比林市遭遇网络攻击，多个系统被迫下线  
  
•韩国电信巨头SK遭恶意软件攻击，3400万客户敏感数据或被泄露  
  
•英国零售巨头玛莎百货确认遭网络攻击，客户订单系统出现延迟  
  
•"Cookie-Bite"攻击利用Chrome扩展窃取会话令牌，长期非法访问Microsoft云服务  
  
•SSL.com域名验证漏洞曝光：攻击者可轻松获取知名网站SSL证书  
  
•绿盟科技披露2024年年报，营收增长超40%，亏损收窄  
  
   
  
  
**热点观察**  
  
  
  
**英国Ofcom禁止"全球标题码"租赁，遏制移动网络犯罪**  
  
  
英国通信监管机构Ofcom宣布禁止"全球标题码"租赁，以遏制犯罪分子滥用英国移动网络。这一里程碑式的决定旨在应对网络犯罪和外国情报机构日益增长的威胁。  
  
  
全球标题码是移动网络信令中使用的特殊电话号码类型，用于路由确保通话和短信到达预定目的地的信令消息。然而，由于监管不力和租赁安排提供的匿名性，犯罪分子利用全球标题码拦截双因素认证码、追踪用户位置和转移短信或通话流量，对个人、金融机构和国家安全基础设施构成重大风险。  
  
  
新的租赁禁令即刻生效，现有租赁将分阶段淘汰。所有当前安排必须在2026年4月22日前结束，特定用例可延期至2026年10月22日。Ofcom还发布了更新的指南，概述了移动网络运营商如何监控和保护其信令资产。这一决定使英国成为移动网络保护的全球领导者，并呼吁国际监管机构采取类似措施。  
  
  
原文链接：  
  
https://thecyberexpress.com/ofcom-bans-global-titles-leasing/  
  
  
**漏洞研究新突破：利用ChatGPT快速创建利用代码**  
  
  
安全研究人员Matt Keeley近日展示了人工智能如何在公开概念验证(PoC)利用代码发布前，成功创建关键漏洞的工作利用程序。这一发展可能彻底改变漏洞研究领域。  
  
  
Keeley利用GPT-4为CVSS评分高达10的Erlang/OTP SSH关键漏洞（CVE-2025-32433）开发了功能完整的利用代码。该漏洞于2025年4月16日披露，影响Erlang/OTP的SSH服务器实现，允许未经身份验证的远程代码执行。  
  
  
在注意到Horizon3.ai研究人员提及他们已创建但未发布PoC的推文后，Keeley开始了他的研究。他引导GPT-4分析该漏洞，AI系统地完成了以下步骤：  
- 定位不同版本的代码；  
  
- 创建工具比较易受攻击和已修补的代码；  
  
- 识别漏洞的确切原因；  
  
- 生成利用代码；  
  
- 调试并修复代码直至其正常工作。  
  
  
Keeley表示，几年前这个过程需要专业的Erlang知识和数小时的手动调试；而现在只需一个下午和正确的提示就能完成。  
  
  
原文链接：  
  
https://cybersecuritynews.com/chatgpt-creates-working-exploit-for-cves/  
  
  
**从边缘设备入侵：黑客新战术瞄准中小企业网络薄弱环节**  
  
  
近期研究发现，黑客正在加大对中小企业(SMBs)网络边缘设备的攻击力度。这些设备，包括防火墙、虚拟专用网络设备和远程访问系统，已成为超过四分之一已确认企业数据泄露事件的初始攻击点。  
  
  
Sophos的年度威胁报告指出，勒索软件攻击仍是中小企业面临的主要网络威胁。2024年，勒索软件案例占小型企业客户事件响应业务的70%，而对于拥有500至5000名员工的中型组织，这一比例高达90%以上。  
  
  
攻击者正在利用网络边缘设备的漏洞获取未经授权的访问权限、部署恶意软件并发起勒索软件攻击。这种被称为"数字碎片"的现象突显了过时和未修补的硬件和软件构成了日益增长的安全漏洞来源。值得注意的是，攻击者正在不断演变其技术，将加密攻击与数据窃取和勒索相结合。在某些情况下，攻击者甚至不再加密文件，而是将重点放在数据窃取上。  
  
  
安全专家建议优先修补边缘设备，为所有远程访问实施多因素身份验证，更换生命周期结束的设备，并考虑寻求外部帮助定期审核和监控外部攻击面，以防止机会主义攻击者利用漏洞。  
  
  
原文链接：  
  
https://cybersecuritynews.com/hackers-attacking-network-edge-devices/  
  
  
  
**网络攻击**  
  
  
  
**美国德州阿比林市遭遇网络攻击，多个系统被迫下线**  
  
  
德克萨斯州阿比林市政府近日因网络攻击被迫关闭部分系统。市政官员表示，他们于上周五（4月18日）收到市内部网络服务器无响应的报告后发现了这一事件。IT人员立即开始断开受影响的系统，并聘请网络安全专家进行调查。  
  
  
由于系统中断，政府办公室的所有卡支付系统均无法使用，迫使民众使用现金或支票付款。市政府承诺不会因账单逾期而切断公用事业服务，并表示也可以在线使用银行卡支付。IT团队正在监控市政系统以观察任何异常活动，并努力恢复服务。官员们承认事件恢复是一个"耗时的过程"，未来几周将了解到更多关于攻击范围的信息。  
  
  
阿比林市拥有超过13万居民，位于达拉斯和沃思堡以西约三小时车程，这两个城市最近也经历了影响服务的网络攻击。自2023年达拉斯市遭受勒索软件攻击以来，德克萨斯州多个政府机构都处理过网络事件。  
  
  
原文链接：  
  
https://therecord.media/texas-abilene-offline-cyberattack-systems  
  
  
**韩国电信巨头SK遭恶意软件攻击，3400万客户敏感数据或被泄露**  
  
  
韩国最大移动运营商SK Telecom近日发布安全通告，警告用户一次恶意软件攻击导致客户敏感USIM相关信息被泄露。USIM数据包含国际移动用户识别码(IMSI)、移动站ISDN号码(MSISDN)、认证密钥、网络使用数据等信息，这些数据可能被用于定向监控、跟踪和SIM卡交换攻击。  
  
  
SK Telecom在韩国移动电话服务市场占有约48.4%的份额，拥有3400万用户。该公司表示，他们于2025年4月19日（周六）晚11点检测到系统中的恶意软件，并在发现可能的泄露时立即删除了恶意软件并隔离了疑似被黑的设备。该事件已于次日报告给韩国互联网与安全局(KISA)，并于今日通知了韩国个人信息保护委员会。目前调查正在进行中，尚未确定入侵的确切原因、规模或范围。  
  
  
SK Telecom已加强对USIM交换和异常认证尝试的阻断，并将立即暂停与可疑活动相关的账户服务。该公司建议用户通过其门户网站注册USIM保护服务，启用后可阻止手机号码被转移到其他SIM卡。目前尚无威胁行为者对此次攻击负责。  
  
  
原文链接：  
  
https://www.bleepingcomputer.com/news/security/sk-telecom-warns-customer-usim-data-exposed-in-malware-attack/  
  
  
**英国零售巨头玛莎百货确认遭网络攻击，客户订单系统出现延迟**  
  
  
英国跨国零售商玛莎百货（Marks & Spencer）近日确认正在应对一起网络攻击事件，该事件已持续数天并影响了包括"点击和收取"（Click and Collect）在内的多项业务运营。  
  
  
玛莎百货在全球拥有超过1400家商店和6.4万名员工，主要销售服装、食品和家居用品。该公司在伦敦证券交易所发布的新闻稿中确认了这一网络安全事件，表示公司正与网络安全专家合作管理和解决问题。事件发生后，玛莎百货对店铺运营进行了一些临时微调以保护客户和业务，目前其商店仍正常营业，网站和应用程序也在正常运行。虽然玛莎百货未提供网络事件的具体性质，但表示已通知数据保护监管机构和国家网络安全中心。  
  
  
此次网络攻击导致玛莎百货的"点击和收取"订单系统出现延迟，公司建议客户在收到订单准备就绪的电子邮件后再前往商店取货。目前尚无勒索软件团伙或其他威胁行为者宣称对此次攻击负责。  
  
  
原文链接：  
  
https://www.bleepingcomputer.com/news/security/marks-and-spencer-confirms-a-cyberattack-as-customers-face-delayed-orders/  
  
  
**"Cookie-Bite"攻击利用Chrome扩展窃取会话令牌，长期非法访问Microsoft云服务**  
  
  
安全研究人员近日披露了"Cookie-Bite"概念验证攻击。该攻击利用浏览器扩展从Azure Entra ID窃取浏览器会话Cookie，从而绕过多因素认证(MFA)保护，并维持对Microsoft 365、Outlook和Teams等云服务的非法访问。  
  
  
这种攻击主要针对Azure Entra ID中的‘ESTAUTH’和‘ESTSAUTHPERSISTNT’两种Cookie。ESTAUTH是一种临时会话令牌，表明用户已通过认证并完成MFA，在浏览器会话中最长有效期为24小时；而ESTSAUTHPERSISTENT是持久版本的会话Cookie，当用户选择"保持登录状态"时创建，有效期长达90天。  
  
  
攻击者的恶意Chrome扩展会监控受害者的登录事件，监听与Microsoft登录URL匹配的标签页更新。当登录发生时，它会读取所有范围为'login.microsoftonline.com'的Cookie，提取上述两个令牌，并通过Google表单将Cookie数据发送给攻击者。值得注意的是，将该扩展上传到VirusTotal后，结果显示目前没有安全厂商将其检测为恶意软件。一旦Cookie被窃取，攻击者可以使用合法的Cookie-Editor Chrome扩展等工具将其注入浏览器。注入后，Azure会将攻击者的会话视为完全认证状态，绕过MFA并获得与受害者相同的访问权限。  
  
  
防御措施包括监控异常登录、实施条件访问策略(CAP)限制登录到特定IP范围和设备，以及强制执行Chrome ADMX策略，只允许预先批准的扩展运行并完全阻止用户使用浏览器的开发者模式。  
  
  
原文链接：  
  
https://www.bleepingcomputer.com/news/security/cookie-bite-attack-poc-uses-chrome-extension-to-steal-session-tokens/  
  
  
  
**安全漏洞**  
  
  
  
**SSL.com域名验证漏洞曝光：攻击者可轻松获取知名网站SSL证书**  
  
  
近日，安全研究人员发现SSL.com存在一个严重漏洞，攻击者可通过利用其基于电子邮件的域名验证方法中的缺陷，为主要域名颁发有效的SSL证书。  
  
  
这个漏洞涉及域名控制验证(DCV)过程，特别是"Email to DNS TXT Contact"验证方法。SSL.com允许用户通过创建一个包含联系人电子邮件地址作为值的记录来验证域名控制并获取TLS证书。SSL.com会发送代码和URL以确认用户对域名的控制权。然而，由于这个漏洞，SSL.com错误地将用户视为用于联系电子邮件的域名的所有者。  
  
  
这一缺陷源于使用电子邮件验证控制权的方式，特别是与MX记录（指示哪些服务器接收该域名的电子邮件）相关。它允许任何人接收与域名关联的任何电子邮件地址的邮件，从而可能获取整个域名的有效SSL证书。一位使用Sec Reporter别名的安全研究人员通过使用@aliyun.com电子邮件地址（阿里巴巴运营的网络邮件服务）获取aliyun.com和www.aliyun.com的证书，证明了这一点。  
  
  
SSL.com已确认该问题，并解释说除了研究人员获得的测试证书外，他们还错误地以相同方式颁发了十个其他证书。该公司已禁用"Email to DNS TXT Contact"验证方法，并澄清"这不影响Entrust使用的系统和API"。  
  
  
原文链接：  
  
https://hackread.com/ssl-com-vulnerability-fraud-ssl-certificates-domains/  
  
  
  
**行业动态**  
  
  
  
**绿盟科技披露2024年年报，营收增长超40%，亏损收窄**  
  
  
4月22日晚间，绿盟科技披露2024年年报。年报显示，公司2024年实现营收23.58亿元，同比增长40.29%；归属于上市公司股东的净利润为-3.65亿元，同比减少亏损 62.66%；经营活动产生的现金净流量1.36亿元，由负转正。绿盟科技年报各项指标稳步向好，呈现持续改善的运营趋势。  
  
  
根据年报，报告期内，公司所处网络安全行业市场规模虽增速放缓但仍保持增长态势。信息安全行业下游客户复苏尚需时日，信息安全投入节奏恢复偏慢。面对诸多挑战，公司立足自身业务，紧密结合行业发展趋势和市场动向，持续落实聚焦战略，公司经营企稳回升。  
  
  
公司重点聚焦运营商、金融、能源等需求稳定且公司长期投入的行业和客户。从2024年收入结构上看，公司电信运营商客户收入同比增长31.34%，金融客户收入同比增长24.23%，毛利率分别同比增加13.18%和10.09%；能源与企业行业收入同比增长52.74%。  
  
  
原文链接：  
  
http://www.cninfo.com.cn/new/disclosure/detail?plate=szse&orgId=9900022545&stockCode=300369&announcementId=1223213501&announcementTime=2025-04-23  
  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/kuIKKC9tNkAibeib6HUSIXJ4IhpazTYic3uwicySgIEk8ZeMC7X5evYXoNPHxoUlibqgo6Ilq0dRkGrMKibWtfcibYwsg/640?wx_fmt=jpeg "")  
  
合作电话：18311333376  
  
合作微信：aqniu001  
  
投稿邮箱：editor@aqniu.com  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/kuIKKC9tNkAfZibz9TQ8KWj4voxxxNSGMAGiauAWicdDiaVl8fUJYtSgichibSzDUJvsic9HUfC38aPH9ia3sopypYW8ew/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp "")  
  
  
  
