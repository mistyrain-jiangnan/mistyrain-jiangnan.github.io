---
title: 详解 cookie、session、storage、token
urlname: glbqxu
date: '2021-12-22 13:17:38 +0800'
tags: 缓存
categories: 缓存
---

### 参考文档

[https://blog.csdn.net/zhangquan_zone/article/details/77627899](https://blog.csdn.net/zhangquan_zone/article/details/77627899)
[https://www.sohu.com/a/207801015_114819](https://www.sohu.com/a/207801015_114819)
[https://www.cnblogs.com/lufeiludaima/p/pz20190203.html](https://www.cnblogs.com/lufeiludaima/p/pz20190203.html)
[https://segmentfault.com/a/1190000019457890](https://segmentfault.com/a/1190000019457890)
[https://www.cnblogs.com/andy-zhou/p/5360107.html](https://www.cnblogs.com/andy-zhou/p/5360107.html)
[https://www.zhihu.com/question/359508734/answer/1591403693](https://www.zhihu.com/question/359508734/answer/1591403693)

---

## **cookie**

> cookie 是由服务器端生成，发送给 User-Agent（一般是浏览器），（服务器告诉浏览器设置一下 cookie）,浏览器会将 cookie 的 key/value 保存到某个目录下的文本文件。

### **cookie 的诞生**

由于 HTTP 协议是无状态的，而服务器端的业务必须是要有状态的。cookie 诞生的最初目的是为了存储 web 中的状态信息，以方便服务器端使用。比如判断用户是否是第一次访问网站。目前最新的规范是 RFC 6265，它是一个由浏览器服务器共同协作实现的规范。

### **如何设置 cookie**

#### 客户端设置

```javascript
document.cookie = "name=value; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/";
```

> - [x] **name**:一个唯一确定的 cookie 名称。通常来讲 cookie 的名称是不区分大小写的。

- [x] **value**:存储在 cookie 中的字符串值。最好为 cookie 的 name 和 value 进行 url 编码
- [x] **path**: 表示这个 cookie 影响到的路径，浏览器跟会根据这项配置，像指定域中匹配的路径发送 cookie。
- [x] **expires**:失效时间，表示 cookie 何时应该被删除的时间戳(也就是，何时应该停止向服务器发送这个 cookie)。如果不设置这个时间戳，浏览器会在页面关闭时即将删除所有 cookie；不过也可以自己设置删除时间。这个值是 GMT 时间格式，如果客户端和服务器端时间不一致，使用 expires 就会存在偏差。
- [ ] **domain**: 属性定义可访问该 cookie 的域名，对一些大的网站，如果希望 cookie 可以在子网站中共享，可以使用该属性。例如设置 Domain 为 .bigsite.com ,则 sub1.bigsite.com 和 sub2.bigsite.com 都可以访问已保存在客户端的 cookie，这时还需要将 Path 设置为/。
- [ ] **max-age**: 属性定义 cookie 的有效时间，用秒计数，当超过有效期后，cookie 的信息不会从客户端附加在 HTTP 消息头中发送到服务端。
- [ ] **secure**:属性值定义 cookie 的安全性，当该值为 true 时必须是 HTTPS 状态下 cookie 才从客户端附加在 HTTP 消息中发送到服务端，在 HTTP 时 cookie 是不发送的；Secure 为 false 时则可在 HTTP 状态下传递 cookie，Secure 缺省为 false。

#### 服务端设置

> 不管你是请求一个资源文件(如 html/js/css/图片), 还是发送一个 ajax 请求, 服务端都会返回 response.而 response header 中有一项叫**set-cookie**, 是服务端专门用来设置 cookie 的; 一个 set-cookie 只能设置一个 cookie,当你想设置多个, 需要添加同样多的 set-cookie 服务端可以设置 cookie 的所有选项: expires, domain, path,secure, HttpOnly

### **cookie 的处理分为：**

- 服务器向客户端发送 cookie

  - 服务器端向客户端发送 cookie 是通过 HTTP 响应报文实现的，在 set-cookie 中设置需要向客户端发送的 cookie，cookie 格式如下：
    > set-cookie: "name=value;domain=.domain.com;path=/;expires=Sat, 11 Jun 2016 11:29:42     GMT;HttpOnly;secure"

- 浏览器将 cookie 保存
  - 浏览器将后台传递过来的 cookie 进行管理，并且允许开发者在 javaScript 中使用 document.cookie 来存取 cookie。但是这个接口使用起来非常蹩脚。它会因为使用它的方式不同而表现出不同的行为。
- 当用来获取属性值时，document.cookie 返回当前页面可用的（根据 cookie 的域、路径、失效时间和安全设置）所有的字符串，字符串的格式如下：

```javascript
"name1=value1;name2=value2;name3=value3";
```

- 当用来设置值的时候，document.cookie 属性可设置为一个新的 cookie 字符串。这个字符串会被解释并添加到现有的 cookie 集合中。如：

```javascript
document.cookie =
  "username=John Doe; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/";
```

- 之后每次 http 请求浏览器都会将 cookie 发送给服务器端
  - cookie 可以设置不同的域与路径，所以对于同一个 name value，在不同域不同路径下是可以重复的，浏览器会按照与当前请求 url 或页面地址最佳匹配的顺序来排定先后顺序
- 所以当前端传递到服务器端的 cookie 有多个重复 name value 时，我们只需要最匹配的那个，也就是第一个。

## **session**

session 是另一种记录客户状态的机制，不同的是 cookie 保存在客户端浏览器中，而 session 保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上，这就是 session。客户端浏览器再次访问时只需要从该 session 中查找该客户的状态就可以了。**session 相当于程序在服务器上建立的一份用户的档案，用户来访的时候只需要查询用户档案表就可以了。**

> 1. 用户向服务器发送用户名和密码
> 1. 服务器验证通过后,在当前对话(session)里面保存相关数据,比如用户角色, 登陆时间等;
> 1. 服务器向用户返回一个 session_id, 写入用户的 cookie
> 1. 用户随后的每一次请求, 都会通过 cookie, 将 session_id 传回服务器
> 1. 服务端收到 session_id, 找到前期保存的数据, 由此得知用户的身份

### **session 的生命周期与有效期**

- 为了获得更高的存取速度，服务器一般把 session 放在内存里。每个用户都会有一个独立的 session。如果 session 内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。**session 的使用虽然比 cookie 方便，但是过多的 session 存储在服务器内存中，会对服务器造成压力。因此，session 里的信息应该尽量精简。**
- session 在用户第一次访问服务器的时候自动创建。session 生成后，只要用户继续访问，服务器就会更新 session 的最后访问时间，并维护该 session。
- 由于有越来越多的用户访问服务器，因此 session 也会越来越多。为防止内存溢出，服务器会把长时间内没有活跃的 session 从内存中删除。这个时间就是 session 的超时时间。如果超过了超时时间没访问过服务器，session 就自动失效了

### **session 与 cookie**

- **由于 HTTP 协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是 session。**典型的场景比如购物车，当你点击下单按钮时，由于 HTTP 协议无状态，所以并不知道是哪个用户操作的，所以服务端要为特定的用户创建了特定的 session，用用于标识这个用户，并且**跟踪用户**，这样才知道购物车里面有几本书。这个 session 是保存在服务端的，有一个唯一标识。在服务端保存 session 的方法很多，内存、数据库、文件都有。
- **思考一下服务端如何识别特定的客户？**这个时候 cookie 就登场了。每次 HTTP 请求的时候，客户端都会发送相应的 cookie 信息到服务端。实际上大多数的应用都是用 cookie 来实现 session 跟踪的，
  第一次创建 session 的时候，服务端会在 HTTP 协议中告诉客户端，需要在 cookie 里面记录一个 session ID，以后每次请求把这个会话 ID 发送到服务器，我就知道你是谁了。有人问，如果客户端的浏览器禁用了 cookie 怎么办？一般这种情况下，会使用一种叫做 URL 重写的技术来进行会话跟踪，即每次 HTTP 交互，URL 后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。
- **cookie 其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？**这个信息可以写到 cookie 里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是 cookie 名称的由来，给用户的一点甜头。
- 总结一下：session 是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；cookie 是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现 session 的一种方式。

> 1. **安全性**：sesson 比 cookie 安全，sesson 是存储在服务器端的，cookie 是存储在客户端
> 1. **储存的类型不同**：cookie 只支持字符串数据。想要设置其他类型的数据需要转成字符串 sesson 可以储存任意类型
> 1. **有效期不同**：cookie 可以设置长时间保存比如长使用的默认登陆功能，sesson 一般失效时间较短，客户端关闭或者 sesoon 超时都会失效
> 1. **储存大小不同**：单个 cookie 储存的数据不能超过 4k，sesson 可储存的数据远高于 cookie，但是访问量过多，会占用过多的服务器资源
> 1. cookie 和 session 都会有时间限制，即都会过期。

## **cookie, sessionstorage,localstorage**

> - HTML5 提供了两种在客户端存储数据的新方法：localstorage 和 sessionstorage，挂载在 window 对象下。

- webStorage 是本地存储，数据不是由服务器请求传递的。从而它可以存储大量的数据，而不影响网站的性能。
- webStorage 的目的是为了克服由 cookie 带来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。比如客户端需要保存的一些用户行为或数据，或从接口获取的一些短期内不会更新的数据，我们就可以利用 Web Storage 来存储。
  | 分类 | 生命周期 | 储存 | 储存位置 |
  | --- | --- | --- | --- |
  | cookie | 默认保存在内存中，随浏览器关闭失效，如设置过期时间，在时间过期后失效 | 4kb | 保存在客户端每次请求都会带上 |
  | localstorage | 理论上永久有效，除非手动清除 | 4.98mb | 存在客户端不与服务器交互节省网络流量 |
  | sessionstorage | 仅在当前页面有效，关闭页面后浏览器失效 | 4.98mb 部分浏览器没限制 | 存在客户端不与服务器交互节省网络流量 |

## **token**

> 我们关于如上的几个概念已经说了很多，但是都没有涉及到安全性。如果每次 cookie 和 session 互相传递的都是明文数据（比如每次发送的密码都是未经加密的），就会非常不安全。我们可以考虑在服务端对客户端第一次登陆传递过来的用户名和密码进行一次加密，之后再把结果当做 cookie 传递给客户端。

### 如何使用 Token？

> - 使用设备号/设备 mac 地址作为 token

- 客户端：客户端在登录的时候获取设备的设备号/mac 地址，并将其作为参数传递到服务器端
- 服务器：服务器接收到该参数之后，使用一个变量接收同时将其作为 token 保存数据库，并将该 token 设置在 session 中，客户端每次请求的时候都要统一拦截，并将客户端传递的 token 和服务器 session 中的 token 对比，如果相同则放下，不同则拒绝

> 优点：客户端不需要重新登录，只要登录一次后就能一直使用
> 缺点：客户端需要带设备号/mac 地址作为参数传递

- 用 session 值作为 token
- 客户端：客户端只需要携带用户名和密码即可登录
- 服务端：客户端接收到用户名和密码后并判断，如果正确就将本地获取 sessionId 作为 token 返回给客户端，客户端以后只需要带上请求数据即可
