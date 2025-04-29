#  攻击者利用Craft CMS零日漏洞在野外发起链式攻击   
鹏鹏同学  黑猫安全   2025-04-29 01:29  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8dBEfDPEce9CKoqLh9Guwat7ACs5OCHA29XsVJyhsd7x9DeBtiaEDtNLiaFkqDkJ6mhmXXqRfUmbaBEZmygSIPHg/640?wx_fmt=png&from=appmsg "")  
  
Orange Cyberdefense旗下CSIRT团队警告称，威胁分子在近期攻击中将两个Craft CMS漏洞串联利用。Orange专家在调查一起服务器入侵事件时发现了这些漏洞。  
  
2025年4月25日，CERT Orange Cyberdefense官方推特发布公告称：  
  
"今日Craft官方通报影响CMS的远程代码执行漏洞（标记为#CVE-2025-32432）。该漏洞由Orange Cyberdefense在一个月前报告，当时我们的CSIRT团队调查了某起同时利用两个零日漏洞的攻击事件 1/6（链接）"  
  
这两个编号为CVE-2025-32432和CVE-2024-58136的漏洞，分别是Craft CMS的远程代码执行（RCE）漏洞和其所用Yii框架的输入验证缺陷。  
  
根据Orange Cyberdefense道德黑客团队SensePost发布的报告，攻击者通过组合利用这两个漏洞入侵服务器并上传PHP文件管理器。攻击始于利用CVE-2025-32432漏洞，通过发送携带"返回URL"的特制请求，该URL会被保存到PHP会话文件中。  
  
随后攻击者进一步利用Craft CMS采用的Yii框架中的CVE-2024-58136漏洞，发送恶意JSON载荷执行会话文件中的PHP代码，从而安装基于PHP的文件管理器实现持续控制。  
  
目前两个漏洞均已修复：CVE-2025-32432漏洞在Craft CMS 3.9.15/4.14.15/5.6.17版本中修复，Yii开发团队则于4月9日发布的2.0.52版本修复相关缺陷。  
  
调查显示Onyphe资产数据库中存在近3.5万个Craft CMS实例。通过nuclei模板检测，研究人员发现约1.3万个存在漏洞的实例关联约6300个IP地址，其中大部分位于美国。进一步分析基于特定文件特征识别出约300个可能已遭入侵的实例。  
  
Orange Cyberdefense CSIRT在报告中指出："基于Onyphe资产数据库，我们识别出近3.5万个托管CraftCMS实例的独立域名。使用nuclei模板检测到约1.3万个易受攻击实例，这些实例关联约6300个IP地址，主要分布在美国。"  
  
Craft CMS  
  
Orange Cyberdefense CSIRT同时发布了与这两个漏洞攻击相关的入侵指标(IoCs)。  
  
  
