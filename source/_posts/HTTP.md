---
title: HTTP
date: 2020-07-01 12:01:34
tags:
---

# HTTP

---

# 基础概念



---

- URI(统一资源标识符): 包含 URL(统一资源定位符) 和 URN(统一资源名称)
- 请求和响应报文：

![](https://camo.githubusercontent.com/f48540e5efe054841d1aa19097666c4b35500c10/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f485454505f526571756573744d6573736167654578616d706c652e706e67#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&originHeight=221&originWidth=656&status=done&style=none&width=656)
![](https://camo.githubusercontent.com/6ee5dc048dd39a9c4f9a180cee34c66b196a7c45/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f485454505f526573706f6e73654d6573736167654578616d706c652e706e67#align=left&display=inline&height=267&margin=%5Bobject%20Object%5D&originHeight=267&originWidth=704&status=done&style=none&width=704)

- HTTP方法
   - GET 获取资源 安全 幂等
   - HEAD 获取报文头部 安全 幂等
   - POST 上次资源 不安全 不幂等
   - PUT 修改资源 不安全 幂等
   - PATCH 部分修改资源 不安全 幂等
   - DELETE 删除资源 不安全 幂等
   - OPTIONS 查看资源支持的方法类型 安全 幂等
- HTTP状态码
   - 1XX 信息
      - 100 Continue ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应
   - 2XX成功
      - 200 Success：OK
      - 204 No Content：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用
   - 3XX重定向
      - 301 Moved Permanently ：永久性重定向
      - 302 Found ：临时性重定向
   - 4XX客户端错误
      - 400 Bad Request：请求报文中存在语法错误
      - 401 Unauthorized：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）
      - 403 Forbidden ：请求被拒绝
      - 404 Not Found
   - 5XX服务端错误
      - 500 Internal Server Error ：服务器正在执行请求时发生错误
      - 503 Service Unavailable ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求
- 连接管理
   - 短连接和长连接(只需要建立一次 TCP 连接就能进行多次 HTTP 通信)：
      - 从 HTTP/1.1 开始默认是长连接的，如果要断开连接，需要由客户端或者服务器端提出断开，使用 Connection : close；
      - 在 HTTP/1.1 之前默认是短连接的，如果需要使用长连接，则使用 Connection : Keep-Alive。
   - 流水线：
      - 默认情况下，HTTP 请求是按顺序发出的，下一个请求只有在当前请求收到响应之后才会被发出。由于受到网络延迟和带宽的限制，在下一个请求被发送到服务器之前，可能需要等待很长时间。流水线是在同一条长连接上连续发出请求，而不用等待响应返回，这样可以减少延迟。
- Cookie
   - HTTP 协议是无状态的，主要是为了让 HTTP 协议尽可能简单，使得它能够处理大量事务。HTTP/1.1 引入 Cookie 来保存状态信息
   - 用途：
      - 会话状态管理
      - 个性化设置
      - 浏览器行为跟踪
   - 创建过程
      - 服务器发送的响应报文包含 Set-Cookie 首部字段，客户端得到响应报文后把 Cookie 内容保存到浏览器中
      - 客户端之后对同一个服务器发送请求时，会从浏览器中取出 Cookie 信息并通过 Cookie 请求首部字段发送给服务器
- Session
   - 除了可以将用户信息通过 Cookie 存储在用户浏览器中，也可以利用 Session 存储在服务器端，存储在服务器端的信息更加安全。Session 可以存储在服务器上的文件、数据库或者内存中。也可以将 Session 存储在 Redis 这种内存型数据库中，效率会更高。使用 Session 维护用户登录状态的过程如下：
      - 用户进行登录时，用户提交包含用户名和密码的表单，放入 HTTP 请求报文中
      - 服务器验证该用户名和密码，如果正确则把用户信息存储到 Redis 中，它在 Redis 中的 Key 称为 Session ID
      - 服务器返回的响应报文的 Set-Cookie 首部字段包含了这个 Session ID，客户端收到响应报文之后将该 Cookie 值存入浏览器中
      - 客户端之后对同一个服务器进行请求时会包含该 Cookie 值，服务器收到之后提取出 Session ID，从 Redis 中取出用户信息，继续之前的业务操作
   - 应该注意 Session ID 的安全性问题，不能让它被恶意攻击者轻易获取，那么就不能产生一个容易被猜到的 Session ID 值。此外，还需要经常重新生成 Session ID。在对安全性要求极高的场景下，例如转账等操作，除了使用 Session 管理用户状态之外，还需要对用户进行重新验证，比如重新输入密码，或者使用短信验证码等方式
- Cookie与Session的选择
   - Cookie 只能存储 ASCII 码字符串，而 Session 则可以存储任何类型的数据，因此在考虑数据复杂性时首选 Session
   - Cookie 存储在浏览器中，容易被恶意查看。如果非要将一些隐私数据存在 Cookie 中，可以将 Cookie 值进行加密，然后在服务器进行解密
   - 对于大型网站，如果用户所有的信息都存储在 Session 中，那么开销是非常大的，因此不建议将所有的用户信息都存储到 Session 中
- 代理
   - 代理服务器接受客户端的请求，并且转发给其它服务器
   - 使用代理的主要目的：
      - 缓存
      - 负载均衡
      - 网络访问控制
      - 访问日志记录
   - 代理服务器分为正向代理和反向代理两种：
      - 用户察觉得到正向代理的存在
      - 而反向代理一般位于内部网络中，用户察觉不到

---

# HTTPS

---

- HTTP 有以下安全性问题：
   - 使用明文进行通信，内容可能会被窃听
   - 不验证通信方的身份，通信方的身份有可能遭遇伪装
   - 无法证明报文的完整性，报文有可能遭篡改
- HTTPS 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。通过使用 SSL，HTTPS 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）

---

# 加密

---

1. 对称密钥加密：加密和解密使用同一密钥
1. 非对称密钥加密：加密使用公钥，解密使用私钥
