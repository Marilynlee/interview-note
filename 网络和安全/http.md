# http面试题

### 网络模型
- 应用层（HTTP超文本传输协议、FTP文件传输协议、DNS域名系统的协议层）：规定了向用户提供应用服务时通信的协议
- 传输层（TCP传输控制协议、UDP用户数据报协议、SCTP协议层）：对接上层应用层，提供处于网络连接中两台计算机之间的数据传输所使用的协议
- 网络层（数据在节点之间传输创建逻辑链路）：规定了数据通过怎样的传输路线到达对方计算机传送给对方（IP协议等）
- 数据链路层（在通信实体间建立数据链路连接）：用来处理连接网络的硬件部分，包括控制操作系统、硬件的设备驱动、NIC（Network Interface Card网卡）
- 物理层（定义物理设备如何传输数据）：规定传输数据如何传输信号。

### http协议及组成
HTTP协议定义Web客户端如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。http是无状态协议，不保存请求或相应之间的通信状态，连接双方不能知晓对方当前的身份和状态。HTTP传输的数据都是明文的。

用于HTTP协议交互的信息被称为HTTP报文。客户端的HTTP报文叫请求报文，服务端的HTTP报文叫响应报文。由以下部分组成：
 - 起始行：请求行（请求方法和协议版本）； 状态行（协议版本、状态码）
 - 首部： 请求首部（请求URI、客户端信息等）； 响应首部（服务器名称、资源标识等）
 - 主体： 请求体（用户信息和资源信息等，可为空）； 响应主体（服务端返回的资源信息）
  HTTP方法:用来定义对于资源的操作；常见有GET、POST、DELATE、PUT等，从定义上有各自的语义

HTTPS基于HTTP协议，通过SSL或TLS（可以看作SSL3.0）提供加密处理数据、验证对方身份以及数据完整性保护。特点如下：
 - 内容加密：采用混合加密技术，中间者无法直接查看明文内容
 - 验证身份：通过证书认证客户端访问的是自己的服务器
 - 保护数据完整性：防止传输的内容被中间人冒充或者篡改

### http和https的区别
   - HTTPS协议需要到CA（证书颁发机构）申请证书，一般免费证书很少，需要交费。
   - HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
   - HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
   - http的连接很简单，是无状态的；HTTPS协议是由HTTP+SSL协议构建的可进行加密传输、身份认证的网络协议，可以有效的防止运营商劫持，解决了防劫持的一个大问题，比http协议安全。

### http各版本
- HTTP/0.9
      - 只有GET命令
      - 没有HEADER等描述数据信息
      - 服务器发送完毕即会关闭TCP连接
      - 1个TCP中可以发送多个HTTP连接。
- HTTP/1.0
      - 增加了POST、PUT、DELETE等命令
      - 增加status code状态码和header相关内容
      - 增加多字符集支持，多部分发送，权限，缓存等内容
      -**串行连接**：每次连接只能处理一个请求，收到响应后立即断开连接，每个新的HTTP请求都需要建立一个新的TCP连接）
- HTTP/1.1：
      - 引入了更多的缓存控制策略，如Entity tag，If-Unmodified-Since, If-Match, If-None-Match等
      - 支持**持久连接**（一定时间内同一域名下的HTTP请求，只要两端都没有提出断开连接，则持久保持TCP连接状态，其他请求可以复用这个连接通道）
      - 增加了pipeline客户端可以并发请求，但服务端还是串行执行
      - 增加host（区分同一个物理主机中的不同虚拟主机的域名）和一些其他命令，提高物理服务器使用率
- HTTP2：
      - 数据以二进制传输，可以进行服务端推送
      - **多路复用**（同一个连接里可以并发多个请求，每个HTTP请求都有一个序列标识符，服务器接收到数据后，根据序列标识符重新排序成不同的请求报文，不会导致数据错乱）
      - 头信息压缩以及推送等提高效率

### http状态码
- 1**(信息类)：表示接收到请求并且继续处理
   - 100  Continue   继续，一般在发送post请求时，已发送了http header之后服务端将返回此信息，
表示确认，之后发送具体参数信息
- 2**(响应成功)：表示动作被成功接收、理解和接受
   - 200  OK         正常返回信息
   - 201  Created    请求成功并且服务器创建了新的资源
   - 202  Accepted   服务器已接受请求，但尚未处理
- 3**(重定向类)：为了完成指定的动作，必须接受进一步处理
   - 301  Moved Permanently  请求的网页已永久移动到新位置。
   - 302 Found       临时性重定向。
   - 303 See Other   临时性重定向，且总是使用 GET 请求新的 URI。
   - 304  Not Modified 自从上次请求后，请求的网页未修改过。
- 4**(客户端错误类)：请求包含错误语法或不能正确执行
   - 400 Bad Request  服务器无法理解请求的格式，客户端不应当尝试再次使用相同的内容发起请求。
   - 401 Unauthorized 请求未授权。
   - 403 Forbidden   禁止访问。
   - 404 Not Found   找不到如何与 URI 相匹配的资源。
- 5**(服务端错误类)：服务器不能正确执行一个正确的请求
   - 500 Internal Server Error  最常见的服务器端错误。
   - 502 - 网关错误
   - 503 Service Unavailable 服务器端暂时无法处理请求（可能是过载或维护）
   - 504 Service Time out 服务器响应超时

### 协商缓存和强缓存
浏览器缓存(Brower Caching)是浏览器对之前请求过的文件进行缓存，以便下一次访问时重复使用，节省带宽，提高访问速度，降低服务器压力，分为：强缓存和协商缓存。http缓存机制主要在http响应头中设定，响应头中相关字段为`Expires、Cache-Control、Last-Modified、Etag`。
   - **强缓存**：通过Expires（HTTP1.0）和 Cache-Control（HTTP1.1）的响应头来控制。同时存在时Cache-Control优先于Expires。 浏览器不会向服务器发送任何请求，直接从本地缓存中读取文件并返回200状态码
      - `Expires`是一个GMT时间格式个字符串，浏览器进行第一次请求时，服务器会在返回头部加上Expires，下次请求，如果在这个时间之前则命中缓存
      - `Cache-Control`是利用max-age判断缓存的生命周期，是以秒为单位，参照客户端第一次请求时间计算，如在生命周期时间内，则命中缓存

   强缓存有两种：
      - memory cache : 不访问服务器，缓存在内存中，直接从内存中读取缓存。浏览器关闭后，资源被释放。下次打开不会出现from memory cache
      - disk cache：不访问服务器，直接从读取缓存在硬盘中，关闭浏览器后，数据依然存在，下次打开仍然会是from disk cache
   - **协商缓存**：通过Last-Modified/If-Modified-Since， ETag/If-None-Match来实现。 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源
     - `Last-Modified`： 服务器在第一次响应请求时，会设置资源的Last-Modified。客户端第二次请求时，请求头会加上If-Modified-Since，值为上次请求资源的Last-Modified。 服务器收到If-Modified-Since与服务器文件的Last-Modified比对：无变化（命中缓存）则返回304不返回资源，客户端使用本地缓存，不更新Last-Modified；有变化(没命中)返回200和资源，更新Last-Modified
     - `ETag`： Etag 是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)。服务器在第一次响应请求时，会设置资源的ETag。客户端第二次请求时，请求头会加上If-None-Match，值为上次请求资源的ETag。 服务器收到If-None-Match与服务器文件的ETag比对：无变化（命中缓存）则返回304不返回资源，客户端使用本地缓存，更新ETag；有变化(没命中)返回200和资源，更新ETag

   Last-Modified 与 ETag 是可以一起使用的（见下图），服务器会优先验证 ETag ，一致的情况下，才会继续比对 Last-Modified，最后才决定是否返回 304 Not Modified。

### cookie、session、token、JWT
HTTP 是无状态的协议，每次客户端和服务端会话完成时，服务端不会保存任何会话信息，无法确认当前访问者的身份信息，所以服务器与浏览器为了进行会话跟踪，就必须维护一个会话状态，需要通过 cookie 或者 session 去实现
- cookie： 是服务器发送到用户浏览器并**存储在客户端**的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。**cookie不可跨域**， 每个cookie都会绑定单一的域名，无法在别的域名下获取使用，一级域名和二级域名之间可以通过domain实现共享
- session： 客户端请求服务端，服务端会为这次请求开辟一块内存空间(Session对象), 是基于cookie实现的，**session存储在服务器端**，sessionId会被存储到客户端的cookie中

    Cookie 和 Session 的区别：
    - 安全性： Session 比 Cookie 安全，Session 是存储在服务器端的，Cookie 是存储在客户端的。
    - 存取值的类型不同：Cookie 只支持存字符串数据，Session 可以存任意数据类型。
    - 有效期不同： Cookie 可设置为长时间保持，Session 一般失效时间较短，客户端关闭（默认情况下）或者 Session 超时都会失效。
    - 存储大小不同： 单个Cookie 保存的数据不能超过4K，Session可存储数据远高于Cookie，但是当访问量过多，会占用过多的服务器资源。

- Token：访问资源接口（API）时所需要的资源凭证，token的组成： 头部+负载+签名,经过加密处理的一个字符串.
  
特点：服务端无状态化、可扩展性好；支持移动端设备；安全；支持跨程序调用

### 跨页面通信
- 同源的跨页面通信
    - BroadcastChannel:使用简单，但兼容性比较差;
    - sharedWorker:使用复杂，需要手工去写一个worker进行链接;
    - 监听localStorage事件: 通过监听window下的storage事件，localStorage修改时才会触发事件，而且不同的chrome实例也能触发storage事件;
    - window.opener：可以找到指定的浏览器窗口进行消息传递;
    - 还有其他的通过浏览器中的WebSql 和IndexDb也可以进行跨页面的信息的传递;
- 跨域的跨页面通信
    - iframe：通过父页面的iframe传递消息
    - WebSocket：通过建立一条持续的TCP链接与服务端进行通信，如果需要长时间建立链接，需要发送心跳包保持TCP链接
    - visibilityState: 页面进入时触发,在去手工调用后端信息，以达到数据更新的效果

### 代理服务器
   代理服务器就是客户端和服务端之间的“中间商”，即HTTP请求通过代理服务器转发给服务器，再将服务器的响应返回给客户端的行为。  代理服务器可以用来作为缓存服务器，也可以用来隐藏用户身份（正向代理）或者服务器身份（反向代理）增加安全性。
   - **正向代理**（代理服务器作为客户端）：由客户端向代理服务器发出请求，并指定目标访问服务器，然后，代理服务器向源服务器转交需求，并将获得的内容返回给客户端。正向代理隐藏了真正的请求的客户端，即服务端不知道正式请求客户是谁
   - **反向代理**（代理服务器作为服务端）：是客户端向反向代理发出请求，反向代理服务器收到需求后判断请求哪个服务器，然后再将结果反馈给客户端。 反向代理隐藏了服务器的信息，只知道反向代理服务器，甚至可以把反向代理服务器当做真正服务器看待。反向代理常实现负载均衡

### 负载平衡的两种实现方式
   - 使用反向代理的方式，用户的请求都发送到反向代理服务上，然后由反向代理服务器来转发请求到真实的服务器上，以此来实现集群的负载平衡。
   - 通过DNS用于冗余的服务器上实现负载平衡。一般的大型网站使用多台服务器提供服务，因此一个域名可能会对应多个服务器地址。当用户请求时，DNS服务器返回域名对应的IP地址集合，在每个回答中循环IP地址的顺序，用户一般会选择排在前面的地址发送请求。以此将用户的请求均衡的分配到各个不同的服务器上，这样来实现负载均衡。这种方式有一个缺点就是，由于DNS服务器中存在缓存，所以有可能一个服务器出现故障后，域名解析仍然返回的是那个IP地址，造成访问的问题。

### DNS缓存
DNS存在着多级缓存，从离浏览器的距离排序的话，有以下几种: 浏览器缓存，系统缓存（host文件），路由器缓存，IPS服务器缓存（本地区的域名服务器缓存），根域名服务器缓存，顶级域名服务器缓存，主域名服务器缓存。

---------------
[返回主页](https://github.com/Marilynlee/interview-note)
