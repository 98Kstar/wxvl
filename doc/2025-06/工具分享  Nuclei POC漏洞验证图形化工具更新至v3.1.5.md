#  工具分享 | Nuclei POC漏洞验证图形化工具更新至v3.1.5  
Perlh  不秃头的安全   2025-06-30 01:38  
  
# Nuclei POC漏洞验证图形化工具更新至v3.1.5  
```
前言：本文中涉及到的相关技术或工具仅限技术研究与讨论，严禁用于非法用途，否则产生的一切后果自行承担，如有侵权请私聊删除。需要2025hvv情报群私聊我拉，需要加交流群在最下方。知识星球在最下方，续费也有优惠私聊~~考安全证书请联系vx咨询
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fXxuZbZEfTBicH3Oz8kNP1iaeLVjzy7UrFNFXVZ5Hz3FpavGqOV1zkqp3ICDCAfvXiacLiaUKtNJk0oEA/640?wx_fmt=png&from=appmsg "")  
  
  
  
### 1.工具介绍  
  
轻量便捷的 Nuclei POC 漏洞验证可视化管理工具  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fXxuZbZEfTBicH3Oz8kNP1iaeAdoULdEeico17n2knPuswacO1GUZqGIW7uDW8icP0eal1Uk9IDJsQNTQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fXxuZbZEfTBicH3Oz8kNP1iaeyTf0LILlKEV9p6sqwxfjKdx1pRmKQXziamzictcnbE3DR3s74ibVS0ZEQ/640?wx_fmt=png&from=appmsg "")  
### 2. 主要功能  
```
POC 模板管理：支持对 nuclei POC 模板的增删查改操作跨平台兼容：已支持 MacOS 和 Windows 系统，Linux 版本测试中多任务扫描：支持多 POC、多目标批量扫描高级配置：支持自定义 DNSLOG 服务器、扫描速率控制及多协议网络代理（http/https/socks5）请求分析：支持查看 POC 匹配时的完整请求/响应数据包编辑器优化：POC 编辑器支持主题切换和字体大小调整模板导入：支持一键导入 nuclei 模板，基于 template id 自动去重任务控制：支持手动停止扫描任务，灵活掌控测试流程配置持久化：自动保存用户配置，下次启动无需重复设置参数API 测试：支持对 API 接口及带目录路径的目标进行扫描POC 生成：提供图形化界面辅助生成简单 POC扫描进度实时显示：提供可视化进度条展示当前扫描状态扫描结果导出：支持将扫描结果导出为Excel格式文件
```  
### 3.最新更新  
  
【修复】图形化生成POC时，添加多个请求时生成错误POC的Bug  
  
【新增】图形化生成POC支持匹配DNSLOG  
  
【优化】优化应用细节和日志系统  
  
### 4.安装及使用  
#### 安装  
```
1.1 MacOS 安装步骤将Wavely.app拖移至Applications文件夹中。在终端执行：sudo xattr -d com.apple.quarantine /Applications/Wavely.app 1.2 Windows 安装步骤下载对应压缩包并解压，执行Wavely-xxx-installer.exe安装程序1.3 DNSLOG 设置说明系统默认采用 Nuclei 默认 DNSLOG 服务。如需搭建个人 Nuclei DNSLOG 服务器，可参考：安装使用
```  
#### 使用  
  
2.1 注册 依次点击设置 -> 注册，在注册页面按提示获取设备 ID，完成证书申请后上传证书，即可注册成功。 操作详情：https://github.com/perlh/Wavely/wiki  
  
2.2 导入 POC 点击从文件夹中导入POC按钮，选择存放nuclei poc文件的目录。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fXxuZbZEfTBicH3Oz8kNP1iaePJF3kXfGDv3iaXWbuhRBN4C3tKVferMfJ4icW4chyzfWZEC4CfyACcFA/640?wx_fmt=png&from=appmsg "")  
  
2.3 添加/测试 POC 编辑poc  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fXxuZbZEfTBicH3Oz8kNP1iaecBGzuz7q23AWggl0CRhu8djJogL5mL6qXU5HwW83t31exjibqEcqD3w/640?wx_fmt=png&from=appmsg "")  
### 常见问题  
  
Windows 启动时闪现命令框 此为正常现象，不会对 App 功能产生任何影响，可放心使用。  
  
Macos 无法打开App 因未使用 Apple 证书签名 App，可能出现解除安全验证提示，如软件显示禁止符号 、 无法验证软件身份 或 提示已损坏故不能正常打开 ，可参考以下方案解决：  
  
方案1 在终端执行命令：  
```
sudo xattr -d com.apple.quarantine /Applications/Wavely.app
```  
  
方案2 执行命令：  
```
chmod 755 /Applications/Wavely.app/Contents/MacOS/Wavely
```  
### 获取方式  
  
🔄 公众号回复“  
20250630”即可获取链接  
  
往期推荐：  
  
[工具分享 | BP插件 SMS Bomb Fuzzer更新 V3.1](https://mp.weixin.qq.com/s?__biz=Mzg3NzkwMTYyOQ==&mid=2247489591&idx=1&sn=87c83760491e128ef0b86581936b0d8b&scene=21#wechat_redirect)  
  
  
[渗透思路 | js泄露用的好，洞洞少不了](https://mp.weixin.qq.com/s?__biz=Mzg3NzkwMTYyOQ==&mid=2247489557&idx=1&sn=2cd9a830834313961a0cda59ea8a5007&scene=21#wechat_redirect)  
  
  
[渗透测试｜某单位从敏感三要素泄露到接管管理员的漏洞挖掘之旅](https://mp.weixin.qq.com/s?__biz=Mzg3NzkwMTYyOQ==&mid=2247489523&idx=1&sn=7a64c5b7c5c623d232ed0a4980cf9f10&scene=21#wechat_redirect)  
  
  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zupaBiaPWkUm2fqacE7p1F1eDuraCVnicQ4V8j5pkfcSaYhCwkh18hALtLg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1&watermark=1&tp=webp "")  
  
**关于我们:**  
  
感谢各位大佬们关注-不秃头的安全，后续会坚持更新渗透漏洞思路分享、安全测试、好用工具分享以及挖掘SRC思路等文章，同时会组织不定期抽奖，希望能得到各位的关注与支持，考证请加联系vx咨询。  
  
  
## 1. 需要考以下各类安全证书的可以联系  
  
学生pte超低价，绝对低价绝对优惠，CISP、PTE/PTS、DSG、IRE/IRS、NISP、PMP、CCSK、CISSP、ISO27001、IT服务项目经理等等巨优惠，想加群下方链接：  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zupVPEKz3x5FwvzLJgg6dQE7tgvTicCvtjVqVr7Lw0ibyMyVEkAibFVmfJcw/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zup8ickFpruPOBKWYyMtOicrGT9gYBOfONoc6iaqic25uPx9u6HDp3ibPVVWhg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&watermark=1&tp=webp "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zupPbOhhm2NYBO78AOuKk1jjBWQRQDgVJhM4VDgn385996LRgLCJJzfSw/640?wx_fmt=jpeg&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&watermark=1&tp=webp "")  
## 2. 需要入星球的可以私聊优惠  
  
星球里有什么？  
```
1、维护更新src、cnxd、cnnxd专项漏洞知识库，包含原理、挖掘技巧、实战案例2、fafo/零零信安/QUAKE 高级会员key3、POC及CXXD及CNNXD通用报告详情分享思路4、知识星球专属微信“内部圈子交流群”5、分享src挖掘技巧tips6、最新新鲜工具分享7、不定期有工作招聘内推（工作/护网内推）8、攻防演练资源分享(免杀，溯源，钓鱼等)9、19个专栏会持续更新~提前续费有优惠，好用不贵很实惠
```  
  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zuplUM4kXdQXaiajALtrepc4jRfkZGZRvvc2zqSJYv1QsxyvLsCIEsKG6g/640?wx_fmt=jpeg&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp "")  
## 3、其他合作（合法合规）  
  
1、承接各种安全项目，需要攻防团队或岗位招聘都可代发、代招（灰黑勿扰）；  
  
2、各位安全老板需要文章推广的请私聊，承接合法合规推广文章发布，可直发、可按产品编辑推广；  
合作、推广代发、安全项目、岗位代招均可发布  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/DicRqXXQJ6fULicFkAJQReuebnZV6ib0zupz0YyfHXpyfHiaiagPLKwIPNmdZ8yUV9z4JYjXm3YBCJ0ibTLx3f0ovSjA/640?wx_fmt=other&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp "")  
  
  
  
  
