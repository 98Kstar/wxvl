#  Ghostscript 库中存在 RCE 漏洞，现已被积极利用   
 网络安全应急技术国家工程中心   2024-07-29 16:02  
  
Linux 系统上广泛使用的 Ghostscript 文档转换工具包中存在一个远程代码执行漏洞，目前正在遭受攻击。  
  
Ghostscript 预装在许多 Linux 发行版上，并被各种文档转换软件使用，包括 ImageMagick、LibreOffice、GIMP、Inkscape、Scribus 和 CUPS 打印系统。  
  
此格式字符串漏洞的编号为 CVE-2024-29510，影响所有 Ghostscript 10.03.0 及更早版本。它使攻击者能够逃离 -dSAFER 沙盒（默认启用），因为未修补的 Ghostscript 版本无法在激活沙盒后阻止对 uniprint 设备参数字符串的更改。  
  
这种安全绕过尤其危险，因为它允许他们使用沙箱通常会阻止的 Ghostscript Postscript 解释器执行高风险操作，例如命令执行和文件 I/O。  
  
发现并报告此安全漏洞的 Codean Labs 安全研究人员警告称：“此漏洞对提供文档转换和预览功能的 Web 应用程序和其他服务有重大影响，因为这些服务通常在后台使用 Ghostscript。”  
  
安全研究人员建议用户验证解决方案是否（间接）使用了 Ghostscript，如果是，请将其更新到最新版本。  
  
Codean Labs 还分享了 Postscript 文件，可以通过以下命令运行它来帮助防御者检测他们的系统是否容易受到 CVE-2023-36664 攻击：  
  
ghostscript -q -dNODISPLAY -dBATCH CVE-2024-29510_testkit.ps  
# 在攻击中被积极利用  
  
Ghostscript 开发团队在 5 月份修补了该安全漏洞，而 Codean Labs 则在两个月后发布了包含技术细节和概念验证漏洞代码的说明。攻击者已经在利用 CVE-2024-29510 Ghostscript 漏洞，使用伪装成 JPG（图像）文件的 EPS（PostScript）文件获取易受攻击系统的 shell 访问权限。  
  
开发人员警告称，如果生产服务中的任何地方有 ghostscript，则会受到令人震惊的远程 shell 执行攻击，应该升级它或将其从生产系统中删除。  
  
Codean Labs 补充道：“针对此漏洞的最佳缓解措施是将 Ghostscript 安装更新至 v10.03.1。如果用户的发行版未提供最新的 Ghostscript 版本，则可能仍发布了包含此漏洞修复程序的补丁版本（例如，Debian、Ubuntu、Fedora）。”  
  
一年前，Ghostscript 开发人员修补了另一个严重的 RCE 漏洞 (CVE-2023-36664)，该漏洞也是由未修补的系统上打开恶意制作的文件引发的。  
  
**参考及来源：**  
  
https://www.bleepingcomputer.com/news/security/rce-bug-in-widely-used-ghostscript-library-now-exploited-in-attacks/  
  
  
  
原文来源：嘶吼专业版  
  
“投稿联系方式：010-82992251   sunzhonghao@cert.org.cn”  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/GoUrACT176n1NvL0JsVSB8lNDX2FCGZjW0HGfDVnFao65ic4fx6Rv4qylYEAbia4AU3V2Zz801UlicBcLeZ6gS6tg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
