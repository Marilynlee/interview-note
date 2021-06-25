# web安全
分为： 代码层面、架构层面、运维层面: 常见的web攻击：
## 1.跨站脚本攻击XSS
> XSS(Cross-Site Scripting)的原理是恶意攻击者往 Web 页面里插入恶意可执行网页脚本代码，当用户浏览该页之时，嵌入其中 Web 里面的脚本代码会被执行，从而可以达到攻击者盗取用户信息或其他侵犯用户安全隐私的目的。

防御方法：
- CSP(Content-Security-Policy)本质上就是建立白名单，开发者明确告诉浏览器哪些外部资源可以加载和执行。 设置 HTTP Header 中的 Content-Security-Policy;设置 meta 标签的方式

- 转义字符: 对于引号、尖括号、斜杠进行转义，特殊字符script进行过滤

- HttpOnly Cookie: 设置cookie时，将其属性设为HttpOnly，就可以避免该网页的cookie被客户端恶意JavaScript窃取，保护用户cookie信息
#### 1.反射型XSS： 即时性，不经过服务器存储，需要诱骗点击才能发起
**攻击方法：** 攻击者构造出特殊的 URL，当URL被打开特有的恶意代码参数被 HTML 解析、执行，恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

**防御方法：** 前端渲染的时候对任何的字段都需要做 escape 转义编码。  Web 页面渲染的所有内容或者渲染的数据都必须来自于服务端；  

#### 2. 存储型XSS：植入在数据库中，别的用户也会被攻击，危害较大
**攻击方法：** 黑客利用的 XSS 漏洞，将内容经正常功能提交进入数据库持久保存，当前端页面获得后端从数据库中读出的注入代码时，恰好将其渲染执行。

**防御方法：** 前端服务器数据传递过程中和前端渲染数据时进行转义/过滤

## 2.跨站请求伪造CSRF
> CSRF(Cross Site Request Forgery)，即跨站请求伪造，是一种常见的Web攻击，它利用用户已登录的身份，在用户毫不知情的情况下，以用户的名义完成非法操作。

**攻击方法：**
    1. 用户登录站点 A，并在本地记录了 cookie
    2. 在用户没有登出站点 A 的情况下（也就是 cookie 生效的情况下），访问了恶意攻击者提供的引诱危险站点 B (B 站点要求访问站点A)。
    3. 站点 A 没有做任何 CSRF 防御

**防御方法：**
    - Get请求不对数据进行修改
    - 判断请求的来源：检测Referer(并不安全，Referer可以被更改)
    - Samesite Cookie属性： Cookie设置SameSite 属性（不随着跨域请求发送）可以很大程度减少CSRF的攻击，存在兼容性问题。
    - 添加验证码(主流,体验不好)：在重要交易等操作时，增加验证码，确保我们的账户安全。
    - 使用Token(主流)： 发送请求时在HTTP 请求中以参数的形式加入一个随机产生的token，并在服务器建立一个拦截器来验证这个token

## 3.点击劫持攻击
> 点击劫持是一种视觉欺骗的攻击手段。攻击者将需要攻击的网站通过 iframe 嵌套的方式嵌入自己的网页中，并将 iframe 设置为透明，在页面中透出一个按钮诱导用户点击。

**攻击方法：**
1. 用户登录站点 A，被攻击者诱惑打开第三方网站
2. 第三方网站通过 iframe 引入了 A 网站的页面内容，用户在第三方网站中点击某个按钮（被装饰的按钮），实际上是点击了 A 网站的按钮。
3. 例子： 当你点击了一个不明链接之后，自动关注了某一个人的博客或者订阅了视频

**防御方法：**
    - X-Frame-Options（HTTP 响应头）：是为了防御用 iframe 嵌套的点击劫持攻击。DENY: 拒绝任何域加载 SAMEORIGIN: 允许同源域下加载 ALLOW-FROM: 可以定义允许frame加载的页面地址
    - FrameBusting 代码： 使用JS阻止恶意网站载入网页。如检测到网页被非法网页载入，就自动跳转。如果用户浏览器禁用JS，FrameBusting失效
    `if ( top.location != window.location ){
            top.location = window.location
     }`

## 4.SQL注入攻击
SQL注入是一种常见的Web安全漏洞，攻击者利用这个漏洞，可以访问或修改数据，或者利用潜在的数据库漏洞进行攻击。

**攻击方法：**
1. 获取用户请求参数,拼接到代码当中
2. SQL语句按照攻击者构造参数的语义执行成功
   
注：SQL注入的必备条件： 1.可以控制输入的数据 2.服务器要执行的代码拼接了控制的数据
本质:_数据和代码未分离，即数据当做了代码来执行。_

**防御方法：**
- 严格限制Web应用的数据库的操作权限
- 后端代码检查输入的数据是否符合预期，严格限制变量的类型
- 对进入数据库的特殊字符（'，"，\，<，>，&，*，; 等）进行转义处理，或编码转换
- 所有的查询语句建议使用数据库提供的参数化查询接口，参数化的语句使用参数而不是将用户输入变量嵌入到 SQL 语句中，即不要直接拼接 SQL 语句

```textmate
恶意攻击者输入的用户名是 admin' --
原来的sql语句： SELECT * FROM user WHERE username='admin' AND psw='password'
攻击者用奇怪用户名将SQL变成了如下形式
SELECT * FROM user WHERE username='admin' --' AND psw='xxxx'
SQL 中,' --是闭合和注释的意思，-- 是注释后面的内容的意思
sql变成SELECT * FROM user WHERE username='admin'
所谓的万能密码,本质上就是SQL注入的一种利用方式
```

# 安全扫描工具
#### 1.  [Arachni](https://github.com/Arachni/arachni) 
基于Ruby的开源，功能全面，高性能的漏洞扫描框架

可以实施 代码注入、CSRF、文件包含检测、SQL注入、命令行注入、路径遍历等各种攻击。同时，它还提供了各种插件，可以实现表单爆破、HTTP爆破、防火墙探测等功能。针对大型网站，该工具支持会话保持、浏览器集群、快照等功能，帮助用户更好实施渗透测试。

#### 2.  [Mozilla HTTP Observatory](https://github.com/mozilla/http-observatory/) 
Mozilla最近发布的一款名为Observatory的网站安全分析工具，意在鼓励开发者和系统管理员增强自己网站的安全配置

检查的主要范围包括： Cookie   跨源资源共享（CORS）  内容安全策略（CSP）    HTTP公钥固定（Public Key Pinning）  HTTP严格安全传输（HSTS）状态    是否存在HTTP到HTTPs的自动重定向  子资源完整性（Subresource Integrity）  X-Frame-Options    X-XSS-Protection

#### 3.  [W3af](https://github.com/andresriancho/w3af) 
一个基于Python的Web应用安全扫描器

能够识别200多个漏洞，包括跨站点脚本、SQL注入和操作系统命令。

---------------
[返回主页](https://github.com/Marilynlee/interview-note)
