#  苹果修复了iPhone和iPad中被“极其复杂的攻击”利用的漏洞   
鹏鹏同学  黑猫安全   2025-02-11 01:35  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8dBEfDPEceibCW7uylu3MScpHH38JKTupgJeqlvMyv6TNoFDP4m4yAw5ico3WJoaFTUEoq5mHWb3ibk0o9aVLlprQ/640?wx_fmt=png&from=appmsg "")  
  
苹果发布了紧急安全更新，以解决一个被追踪为CVE-2025-24200的零日漏洞，该漏洞被认为在“极其复杂”的针对性攻击中被利用。攻击者可能利用该漏洞在锁定设备上禁用USB受限模式。苹果的USB受限模式是iOS 11.4.1中引入的安全功能，旨在通过Lightning端口保护设备免受未经授权的访问。USB受限模式在特定时间间隔后禁用iPhone的Lightning端口的数据连接，但不会中断充电过程。任何其他数据传输都需要用户提供密码。苹果通过改进状态管理修复了该漏洞。  
  
iOS 18.3.1和iPadOS 18.3.1的发布说明中写道：“物理攻击可能会在锁定设备上禁用USB受限模式。”苹果已经了解到有报告称，这个问题可能在针对特定目标个人的极其复杂的攻击中被利用。多伦多大学Munk学院Citizen Lab的Bill Marczak向苹果报告了这个漏洞。  
  
受影响的设备包括iPhone XS及更高版本、iPad Pro 13英寸、iPad Pro 12.9英寸第三代及更高版本、iPad Pro 11英寸第一代及更高版本、iPad Air第三代及更高版本、iPad第七代及更高版本以及iPad mini第五代及更高版本。苹果还发布了17.7.5版本，以解决iPad Pro 12.9英寸第二代、iPad Pro 10.5英寸和iPad第六代中的问题。  
  
苹果通常不会公开披露利用漏洞进行攻击的细节或相关威胁行为者。然而，Citizen Lab研究人员发现的攻击情况表明，威胁行为者可能使用了零日漏洞来传递商业间谍软件，进行高度针对性的攻击。这种类型的攻击通常依赖零日漏洞，针对记者、异见人士和反对派政治家进行间谍软件攻击。  
  
另一种可能性是，苹果意识到一些设备可能遭受了物理访问攻击，可能涉及像Cellebrite这样的取证工具来解锁和提取数据。2023年9月，Citizen Lab的研究人员报告称，苹果修复的两个被积极利用的零日漏洞（CVE-2023-41064和CVE-2023-41061）被用于感染设备，安装NSO Group的Pegasus间谍软件。  
  
根据研究人员的说法，这两个漏洞被链接在一个名为BLASTPASS的零点击漏洞中，用于攻击运行最新版iOS（16.6）的iPhone。Citizen Lab报告称，该漏洞被用于在隶属于华盛顿特区一家拥有国际办事处的民间社会组织的个人设备上安装Pegasus间谍软件。专家报告称，该漏洞涉及包含恶意图像的PassKit附件，这些附件是从攻击者的iMessage账户发送给受害者的。  
  
今年1月，苹果发布了安全更新，以解决2025年首个零日漏洞，追踪为CVE-2025-24085，该漏洞在针对iPhone用户的攻击中被积极利用。该漏洞是一个影响Core Media框架的特权升级漏洞。“恶意应用程序可能能够提升特权。苹果已经了解到有报告称，这个问题可能在iOS版本低于iOS 17.2的攻击中被积极利用。”苹果发布的公告中写道。  
  
苹果的Core Media框架支持iOS和macOS设备上的多媒体任务，如音频和视频的播放、录制和操作。公司通过改进内存管理解决了使用后释放问题。威胁行为者利用该漏洞攻击运行低于iOS 17.2的设备。受影响的设备包括iPhone XS及更高版本、iPad Pro 13英寸、iPad Pro 12.9英寸第三代及更高版本、iPad Pro 11英寸第一代及更高版本、iPad Air第三代及更高版本、iPad第七代及更高版本以及iPad mini第五代及更高版本。苹果通过发布iOS 18.3、iPadOS 18.3、macOS Sequoia 15.3、watchOS 11.3、visionOS 2.3和tvOS 18.3解决了这个问题。  
  
  
