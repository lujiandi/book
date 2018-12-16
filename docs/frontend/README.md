# 浏览器的同源策略

## 概述

浏览器同源策略简单来说，即要求只有同源的网页才能够使用共享的网络资源或行为。所谓的"同源"，是指协议相同，域名相同，端口相同。
同源策略的目的是为了保证用户信息安全，防止恶意的网站窃取数据。
随着互联网的发展，受到限制的网络资源或行为主要包括以下三种

- Cookie，LocalStorage 和 IndexDB
- DOM
- Ajax 请求

## Cookie

非同源的网页不能共享 Cookie。
但是如果页面一级域名相同，可以通过对页面设置相同的 document.domain 共享 cookie。
也可以在设置 Cookie 时指定 Cookie 的一级域名，实现二级三级域名页面的访问。

## LocalStorage

非同源的网页不能共享 LocalStorage，如果需要共享 LocalStorage，可以使用 Html5 新增的 postMessage 方法进行数据传输。

## iframe

非同源网页不能获取对方的 DOM。
如果页面一级域名相同，可以通过对页面设置相同的 document.domain。

1. 片段识别符

   片段识别符是 URL #后面的部分，片段识别符改变，页面不会重新刷新。

   父子窗口可以通过写入片段识别符，并通过监听 hashchange 事件得到通知。

2. window.postMessage

   HTML5 为了解决这个问题，引入了一个全新的 API：跨文档通信 API（Cross-document messaging）。
   这个 API 为 window 对象新增了一个 window.postMessage 方法，允许跨窗口通信，不论这两个窗口是否同源。父窗口和子窗口都可以通过 message 事件，监听对方的消息。

   ```
   var popup = window.open('http://bbb.com', 'title');
   popup.postMessage('Hello World!', 'http://bbb.com');
   ```

## AJAX

非同源网站不能调用对方 API

1. 代理服务器

   通过代理服务器转发 http 请求

2. JSONP

   JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。
   它的基本思想是，网页通过添加一个`<script>`元素，向服务器请求 JSON 数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

3. CORS

   CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，是跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发 GET 请求，CORS 允许任何类型的请求

## CORS

CORS 是一个 W3C 标准，全称是"跨域资源共享"（Cross-origin resource sharing）。
它允许浏览器向跨源服务器，发出 XMLHttpRequest 请求，从而克服了 AJAX 只能同源使用的限制。
整个 CORS 通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS 通信与同源的 AJAX 通信没有差别，代码完全一样。浏览器一旦发现 AJAX 请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
浏览器将 CORS 请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

1. 简单请求与非简单请求

   只要同时满足以下两大条件，就属于简单请求。

   > 请求方法是以下三种方法之一：

   - HEAD
   - GET
   - POST
     > HTTP 的头信息不超出以下几种字段：
   - Accept
   - Accept-Language
   - Content-Language
   - Last-Event-ID
   - Content-Type：只限于三个值 application/x-www-form-urlencoded、 multipart/form-data、text/plain

   不满足简单请求条件的请求，就是非简单请求。

2. 简单请求 CORS 流程

   对于简单请求，浏览器直接发出 CORS 请求。具体来说，就是在头信息之中，增加一个 Origin 字段。

   ```
   GET /cors HTTP/1.1
   Origin: http://api.bob.com
   Host: api.alice.com
   Accept-Language: en-US
   Connection: keep-alive
   User-Agent: Mozilla/5.0...
   ```

   Origin 字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据这个值，决定 是否同意这次请求。

   如果 Origin 指定的源，不在许可范围内，服务器会返回一个正常的 HTTP 回应。浏览器发现， 这个回应的头信息没有包含 Access-Control-Allow-Origin 字段（详见下文），就知道出错 了，从而抛出一个错误，被 XMLHttpRequest 的 onerror 回调函数捕获。

   如果 Origin 指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。

   ```
   Access-Control-Allow-Origin: http://api.bob.com
   Access-Control-Allow-Credentials: true
   Access-Control-Expose-Headers: FooBar
   Content-Type: text/html; charset=utf-8
   ```

   **Access-Control-Allow-Origin**

   该字段是必须的。它的值要么是请求时 Origin 字段的值，要么是一个\*，表示接受任意域名的请求。

   **Access-Control-Allow-Credentials**

   该字段可选。它的值是一个布尔值，表示是否允许发送 Cookie。默认情况下，Cookie 不包括在 CORS 请求之中。设为 true，即表示服务器明确许可，Cookie 可以包含在请求中，一起发给服务器。这个值也只能设为 true，如果服务器不要浏览器发送 Cookie，删除该字段即可。
   另一方面，开发者必须在 AJAX 请求中打开 withCredentials 属性。

   ```
   var xhr = new XMLHttpRequest();
   xhr.withCredentials = true;
   ```

   需要注意的是，如果要发送 Cookie，Access-Control-Allow-Origin 就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie 依然遵循同源政策，只有用服务器域名设置的 Cookie 才会上传，其他域名的 Cookie 并不会上传，且（跨源）原网页代码中的 document.cookie 也无法读取服务器域名下的 Cookie。

   **Access-Control-Expose-Headers**

   该字段可选。CORS 请求时，XMLHttpRequest 对象的 getResponseHeader()方法只能拿到 6 个基本字段：Cache-Control、Content-Language、Content-Type、Expires、 Last-Modified、Pragma。如果想拿到其他字段，就必须在 Access-Control-Expose-Headers 里面指定。上面的例子指定，getResponseHeader ('FooBar')可以返回 FooBar 字段的值。

3. 非简单请求 CORS 流程

   非简单请求的 CORS 请求，会在正式通信之前，增加一次 HTTP 查询请求，称为"预检"请求（preflight）。

   浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些 HTTP 动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的 XMLHttpRequest 请求，否则就报错。

   "预检"请求用的请求方法是 OPTIONS，表示这个请求是用来询问的。头信息里面，关键字段是 Origin，表示请求来自哪个源。

   ```
   Origin: http://api.bob.com
   Access-Control-Request-Method: PUT
   Access-Control-Request-Headers: X-Custom-Header
   Host: api.alice.com
   Accept-Language: en-US
   Connection: keep-alive
   User-Agent: Mozilla/5.0...
   ```

   除了 Origin 字段，"预检"请求的头信息包括两个特殊字段。

   **Access-Control-Request-Method**

   该字段是必须的，用来列出浏览器的 CORS 请求会用到哪些 HTTP 方法，上例是 PUT。

   **Access-Control-Request-Headers**
   该字段是一个逗号分隔的字符串，指定浏览器 CORS 请求会额外发送的头信息字段，上例是 X-Custom-Header。

   服务器收到"预检"请求以后，检查了 Origin、Access-Control-Request-Method 和 Access-Control-Request-Headers 字段以后，确认允许跨源请求，就可以做出回应。

   ```
   HTTP/1.1 200 OK
   Date: Mon, 01 Dec 2008 01:15:39 GMT
   Server: Apache/2.0.61 (Unix)
   Access-Control-Allow-Origin: http://api.bob.com
   Access-Control-Allow-Methods: GET, POST, PUT
   Access-Control-Allow-Headers: X-Custom-Header
   Content-Type: text/html; charset=utf-8
   Content-Encoding: gzip
   Content-Length: 0
   Keep-Alive: timeout=2, max=100
   Connection: Keep-Alive
   Content-Type: text/plain
   ```

   关键的是 Access-Control-Allow-Origin 字段，表示可以请求数据。该字段也可以设为星号， 表示同意任意跨源请求。

   如果浏览器否定了"预检"请求，会返回一个正常的 HTTP 回应，但是没有任何 CORS 相关的头信 息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被 XMLHttpRequest 对象的 onerror 回调函数捕获。

   服务器回应的 Header 字段包括：

   **Access-Control-Allow-Methods**

   该字段必需，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返 回的是所有支持的方法，而不单是浏览器请求的那个方法。这是为了避免多次"预检"请求。

   **Access-Control-Allow-Headers**

   如果浏览器请求包括 Access-Control-Request-Headers 字段，则 Access-Control-Allow-Headers 字段是必需的。它也是一个逗号分隔的字符串，表明服务器支 持的所有头信息字段，不限于浏览器在"预检"中请求的字段。

   **Access-Control-Allow-Credentials**

   该字段与简单请求时的含义相同。

   **Access-Control-Max-Age**

   该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是 20 天 （1728000 秒），即允许缓存该条回应 1728000 秒（即 20 天），在此期间，不用发出另一条 预检请求。

   一旦服务器通过了"预检"请求，以后每次浏览器正常的 CORS 请求，就都跟简单请求一样，会有一 个 Origin 头信息字段。服务器的回应，也都会有一个 Access-Control-Allow-Origin 头信息 字段。
