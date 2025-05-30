#  十年罕见的高危漏洞“狂躁许可”来袭：青藤THP快速发现、监控受影响资产   
原创 让云更安全  青藤云安全   2024-08-09 19:04  
  
****  
8月9日，微软披露了Windows操作系统一个高危漏洞（CVE-2024-38077）的详情和概念验证代码。经分析确认，该漏洞获得CVSS 9.8的评分，是Windows平台近十年来罕见的可以稳定利用、影响广泛的远程零点击(Zero-Click)/认证前（Pre-Auth) 漏洞。该漏洞影响Windows Server 2000到Windows Server 2025所有版本，已存在近30年。  
  
  
这一漏洞存在于Windows远程桌面许可管理服务（RDL）中，该服务被广泛部署于开启Windows远程桌面（3389端口）的服务器，用于管理远程桌面连接许可。一旦漏洞被恶意攻击者或APT组织利用，将快速蔓延，或波及全球所有使用微软服务器的用户。这是自“永恒之蓝”后，Windows首次出现影响全版本且能高稳定利用的认证前RCE漏洞。  
  
  
**青藤猎鹰THP通过其独特的技术，能够快速清点和识别受"狂躁许可"漏洞影响的资产，帮助用户在第一时间内采取应对措施。**  
  
  
**快速识别**  
****  
：THP能够迅速检索网络中的所有资产，识别出可能受到"狂躁许可"漏洞影响的系统  
  
  
**精准筛选**  
****  
： 利用先进的算法，THP可以精确地筛选出易受攻击的资产，减少误报和漏报  
  
  
**重点监控**  
****  
：THP可以直接监控存在漏洞的资产是否存在内网异常行为，直观展示攻击详情  
  
  
鉴于该漏洞破坏力极大，建议用户  
利用青藤猎鹰THP进行网络资产的检索，识别出所有可能受到"狂躁许可"漏洞影响的系统。 然后根据微软发布的安全补丁，及时更新受影响的系统。  
  
  
**1.受影响资产排查：查询受到漏洞影响的资产**  
  
狩猎与追溯--日志分析--输入语句：  
  
select displayIp,hostname,_id from wx_3_mongo.win_host where _id in (select agentId from wx_3_mongo.win_service where name = "TermServLicensing" )  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/7EpcyTBK4P10gRPRS8e6ExiarV5zjgKIt8YEU9icWemYY6n34oAGbetDKylPFGa4SNn62xYLsnE8UWnib4SUOa2Wg/640?wx_fmt=jpeg&from=appmsg "")  
  
   
  
**2、漏洞资产告警查询：查询受高危漏洞影响的设备最近1小时的异常行为告警**  
  
狩猎与追溯--日志分析--输入语句：  
  
select * from alert where datatime > "now-1h" and agent_id in (select agentId from wx_3_mongo.win_service where name = "TermServLicensing" )  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/7EpcyTBK4P10gRPRS8e6ExiarV5zjgKItQe0sGfeIHOk2R4dpGVBqaqSiaCHPTNfKs5XyaibkhZlZ6YDhep1Dl3Lg/640?wx_fmt=jpeg&from=appmsg "")  
  
****  
**3.利用行为监控：监控漏洞利用成功后有创建子进程的行为。有数据则代表被利用，无数据则尚未被利用**  
  
狩猎与追溯--日志分析--输入语句：  
  
select serv.name, serv.cmd, serv.pid,proc.ppname,proc.pname, proc.cmd, proc.path, proc.ppid from wx_3_mongo.win_service as serv left join qtevent_proc_create as proc on proc.agent_id = serv.agentId and proc.ppid = serv.pid and proc.pppath = serv.path where proc.os = "windows" and proc.uname = "SYSTEM" and proc.ppname = "svchost.exe" and serv.name = "TermServLicensing"|`proc.pname` is not null  
  
   
  
**-****完****-**  
****  
  
**热门动态推荐**  
  
  
  
  
  
[](http://mp.weixin.qq.com/s?__biz=MzAwNDE4Mzc1NA==&mid=2650849224&idx=1&sn=507cda9effd95cdef2551a0f257051e0&chksm=80dbdc6db7ac557bc1c036ec47ef4b5fba4db08de8c084beb392c81e70f5e38112c9e935fb7f&scene=21#wechat_redirect)  
  
[](http://mp.weixin.qq.com/s?__biz=MzAwNDE4Mzc1NA==&mid=2650849209&idx=1&sn=6fe1d89d26c10a3567cede608ee2d6bb&chksm=80dbdc1cb7ac550acaa02d650aa5d2a014a97c6d91c490ad66132845f812a260bd970b297090&scene=21#wechat_redirect)  
  
[](http://mp.weixin.qq.com/s?__biz=MzAwNDE4Mzc1NA==&mid=2650849183&idx=1&sn=6511ada1f1b4e9b31f9cb304ceb5ff97&chksm=80dbdc3ab7ac552c8c1f594a88e888aebc203439df0f90487599afeba5181c84ca093295d713&scene=21#wechat_redirect)  
  
![](https://mmbiz.qpic.cn/mmbiz_png/7EpcyTBK4P2a96mDib8UNh5iatSRpDyzpnRAmTSIwYf0UpEQ7ict24MBsOoCwstVYAMTsTnibPWciagggdql3Y0BHzw/640?wx_fmt=png "")  
  
  
