# http请求头和响应头

## 常用http header介绍

1. accept: text/html, text/plain
  * 告诉服务端请求方想接收什么类型的响应体，服务器可以忽略这个头，但如果服务器支持，则会优先返回这个头所列的类型
2. accept-encoding: gzip, deflate, br, zstd
  * 请求头，告诉服务器客户端/浏览器能接收什么样的压缩算法。
  * http协议可以将请求体/响应体压缩后传送，可以减少网络流量，但是要跟对方说明内容是用什么压缩算法压缩的
3. content-encoding: gzip
  * 响应/请求头，告诉对方请求/响应体是用什么压缩算法压缩的。一般还是用在响应头上。一般请求体比较小，没必要压缩。
4. accept-language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
  * 请求头，告诉服务器客户端（的用户）希望什么（自然）语言
  * 用逗号分隔不同的语言，q表示对这种语言的兴趣度
5. host: eloquentjavascript.net
  * 请求头，告诉服务器浏览器是用什么域名请求的这个资源
  * 因为一个服务器可以有多个域名指向它的IP地址，所以就可以用多个域名来访问到这个服务器
  * 服务器可以根据这个请求头来扮演不同的网站
  * 并且如果不发送这个头，服务器其实不知道我们用的是什么域名在访问它，因为解析域名发生在客户端与dns服务器，与想要连接的服务器无关。
  * github pages就是这样的
    * xieranmaya.github.io
    * zhangsan.github.io
  * 难道github会为这每个域名分配一台服务器吗？
    * 当然是不会的。这些站对应的服务器可能是相同的。ping一个以上几个域名就会发现ip大概率是相同的。
6. referer: https://eloquentjavascript.net/
   * 请求头，告诉服务器是谁/哪个页面在请求当前资源。或者：告诉服务器，当前资源请求到之后，会用在哪个页面中。或者：哪个页面在引用这个资源。它总是拿地址栏里的地址的。注意这个额头的单词拼写错误了，但也就这么保留了。
   * **一般用处：**
     * 存在的问题：它可能泄露隐私。当访问A页面时，A页面使用了某个其它网站b资源，在请求b资源的时候，A页面的完整地址会被放到b资源的Referrer请求头里，被b资源的网站知道。当然这个问题现在可以解决，可以通过设置页面的响应头Referrer-Policy来控制浏览器让它不发送referer或不发送完整url到referer头中。
     * 这个头可以用来防   盗链。什么是防盗链：一个网站的资源，只希望自己的网站使用。当一个服务器接收到一个请求时，可以查看这个请求的referer是哪个页面（即这个资源将会被哪个页面使用），如果不是自己网站的页面，则不返回内容。
7. Referrer-Policy: strict-origin-when-cross-origin
  * 针对html页面的响应头（非页面资源不需要这个响应头）。设置这个页面其它请求是否带referer头或者不带完整url,只带网站名
8. content-type: image/jpeg
  * 请求头、响应头，告诉对方请求体或响应体的媒体类型（mime Type）
  * 常见文件格式的媒体类型：
   html: text/html
   html: text/html; charset=utf-8
   css: text/css
   json: application/json
   js: application/javascript
   png: image/png
   svg: image/xml+svg

9. date: Tue, 24 Sep 2024 12:23:05 GMT
  * 响应头，告诉客户端服务器的时间。也可以理解为响应的时间。
10. Content-Length: 234234
  * 请求头、响应头，告诉对方请求体、响应体的长度，以字节为单位
11. transfer-encoding: chunked
  * 请求头/响应头。有些时候，我们无法预知请求体或响应的长度，比如因为body是实时生成的，所以就不能用content-length来告诉对方body有多长。就可以用这个头来说明，body是一点点传的，直到连接断开。
12. last-modified: Tue, 06 Aug 2024 05:54:55 GMT
  * 响应头，告诉浏览器这个资源的最后修改时间
  * 在下一次浏览器请求这个资源时，可以告诉服务器自己这边这个资源的最后修改时间，服务器如果看到这个资源自那个时间之后没有修改过，则告诉浏览器“就用你那边的版本”，不用发送响应体了。以此实现了缓存，或者更精确的说，是协商缓存。
13. if-modified-since: Tue, 06 Aug 2024 05:54:55 GMT
  * 请求头，浏览器告诉服务器当前请求的这个资源最后修改时间是什么时候，这个时间就是在上一次请求这个资源时，响应头中用last-modified告诉浏览器的。
  * 这个头的可以理解为：如果这个资源自那个时间之后修改过，就请给我发送新的版本，如果没有，就告诉我。
  * 如果真的没有修改过，服务器会响应304状态码，对应的状态文字是not modifed
  * **协商缓存的通信过程**
```
GET /foo.png HTTP/1.1
=======================
HTTP/1.1 200 OK
Last-Modified: xxxxxx

(响应体)
=======================
GET /foo.png HTTP/1.1
If-Modified-Since: xxxxxx
=======================
HTTP/1.1 304 Not Modified(无响应体)
```
14. ETag: hashcode
  * 响应头，服务器告诉浏览器这个资源的hash
15. If-None-Match: hashcode
  * 请求头，浏览器告诉服务器，如果这个服务器上这个资源的hash依然是这个值，则就不用返回给响应体了，即服务器可以返回304。如果变了，则返回新的响应体，状态码200
  * **协商缓存的通信过程**
  ```
  GET /foo.png HTTP/1.1
  =======================
  HTTP/1.1 200 OK
  ETag: "76Z5H6Tc6BMBASQJikyOAS5p"

  (响应体)
  =======================
  GET /foo.png HTTP/1.1
  If-None-Match: "76Z5H6Tc6BMBASQJikyOAS5p"
  =======================
  HTTP/1.1 304 Not Modified(无响应体)
  ```
16. expires: Thu, 01 Jan 1970 00:00:01 GMT
  * 响应头，表示资源的过期时间。在某些浏览器里，资源过期之间，浏览器可以不发请求，直接使用本地缓存的版本
17. user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/128.0.0.0 Safari/537.36
  * 请求头，告诉服务器是什么样的浏览器在请求。这其中包含浏览器的品牌，版本，操作系统的品牌，版本。但是现在出现隐私考虑，浏览器的版本号只会精确到主版本。
  * 页面中的js中通过navigator.userAgent也可以读到相同的内容
18. server: Tengine/Aserver
  * 响应头。有点对应于User-Agent，是服务器自报家门。
19. content-security-policy: xxx
  * 针对html页面的响应头（无需给其它类型的资源返回）。
  * 内容安全策略。它用于设置这个页面的内容安全策略。
  * 具体的策略解释如下：
  ```
    default-src 'none'
    默认的源
  base-uri 'self'
    基准uri
  child-src github.com/assets-cdn/worker/ github.com/webpack/ github.com/assets/ gist.github.com/assets-cdn/worker/

  connect-src 'self' uploads.github.com www.githubstatus.com collector.github.com raw.githubusercontent.com api.github.com github-cloud.s3.amazonaws.com github-production-repository-file-5c1aeb.s3.amazonaws.com github-production-upload-manifest-file-7fdce7.s3.amazonaws.com github-production-user-asset-6210df.s3.amazonaws.com *.rel.tunnels.api.visualstudio.com wss://*.rel.tunnels.api.visualstudio.com objects-origin.githubusercontent.com copilot-proxy.githubusercontent.com api.githubcopilot.com api.individual.githubcopilot.com api.business.githubcopilot.com api.enterprise.githubcopilot.com proxy.individual.githubcopilot.com proxy.business.githubcopilot.com proxy.enterprise.githubcopilot.com *.actions.githubusercontent.com wss://*.actions.githubusercontent.com productionresultssa0.blob.core.windows.net/ productionresultssa1.blob.core.windows.net/ productionresultssa2.blob.core.windows.net/ productionresultssa3.blob.core.windows.net/ productionresultssa4.blob.core.windows.net/ productionresultssa5.blob.core.windows.net/ productionresultssa6.blob.core.windows.net/ productionresultssa7.blob.core.windows.net/ productionresultssa8.blob.core.windows.net/ productionresultssa9.blob.core.windows.net/ productionresultssa10.blob.core.windows.net/ productionresultssa11.blob.core.windows.net/ productionresultssa12.blob.core.windows.net/ productionresultssa13.blob.core.windows.net/ productionresultssa14.blob.core.windows.net/ productionresultssa15.blob.core.windows.net/ productionresultssa16.blob.core.windows.net/ productionresultssa17.blob.core.windows.net/ productionresultssa18.blob.core.windows.net/ productionresultssa19.blob.core.windows.net/ github-production-repository-image-32fea6.s3.amazonaws.com github-production-release-asset-2e65be.s3.amazonaws.com insights.github.com wss://alive.github.com github.githubassets.com
    这个页面能够连接哪些服务器
  font-src github.githubassets.com
    这个页面能加载来自哪里的字体文件
  form-action 'self' github.com gist.github.com copilot-workspace.githubnext.com objects-origin.githubusercontent.com
    这个页面的表单能提交到哪些地址
  frame-ancestors 'none'
    这个页面能有哪些页面做为它的iframe祖先
  frame-src viewscreen.githubusercontent.com notebooks.githubusercontent.com
    这个页面能够嵌套来自哪里的页面
  img-src 'self' data: blob: github.githubassets.com media.githubusercontent.com camo.githubusercontent.com identicons.github.com avatars.githubusercontent.com private-avatars.githubusercontent.com github-cloud.s3.amazonaws.com objects.githubusercontent.com secured-user-images.githubusercontent.com/ user-images.githubusercontent.com/ private-user-images.githubusercontent.com opengraph.githubassets.com github-production-user-asset-6210df.s3.amazonaws.com customer-stories-feed.github.com spotlights-feed.github.com objects-origin.githubusercontent.com *.githubusercontent.com
    这个页面能够加载来自哪里在的图片
  manifest-src 'self'
    这个页面能够加载来自哪里在的manifest
  media-src github.com user-images.githubusercontent.com/ secured-user-images.githubusercontent.com/ private-user-images.githubusercontent.com github-production-user-asset-6210df.s3.amazonaws.com gist.github.com github.githubassets.com
    这个页面能够加载来自哪里在的媒体（视频音频）
  script-src github.githubassets.com
    这个页面能够加载来自哪里在的js
  style-src 'unsafe-inline' github.githubassets.com
    这个页面能够加载来自哪里在的css，'unsafe-inline'意思是内联样式也可以
  upgrade-insecure-requests

  worker-src github.com/assets-cdn/worker/ github.com/webpack/ github.com/assets/ gist.github.com/assets-cdn/worker/
    这个页面能够加载来自哪里在的worker
```
20. cache-control: no-cache
  * 请求头、响应头。用来控制缓存相关的功能。但这个头相对来说比较复杂。基于针对这个头专门有一个rfc文档。
21. connection: keep-alive
  * 请求头、响应头。是用来设置承载这个http的tcp连接是否要在http通信完成后关闭连接的。
22. cors相关头
```
简单请求:
https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS
  get、post、head，且请求头没有
  （head请求：类似于get请求，但不需要响应体）

非简单请求：
  不是简单请求即为非简单请求。

预检请求：非简单请求都会事先发送预检请求来检验一下请求应不应该发。
但是简单请求不需要预检请求，而是直接发送正式的请求。

为什么简单请求不发预检请求？因为在没有cors的时代，浏览器就是可以发送跨域请求的。而那个时候，虽然浏览器还是接收不到响应，请求是能到达服务器的。

而现在的非简单请求，如果预检请求没通过，则正式请求根本就不会发，那么请求根本就不会到达服务器。

请求到达服务器的话，是可能被服务器执行的。

Access-Control-Allow-Origin: 

以下三个均为预检请求的响应头
Access-Control-Allow-Methods: DELETE, PUT
  预检请求告诉浏览器这个跨域的正式请求可以使用什么请求方法
Access-Control-Max-Age:
  如果每个非简单请求都要发送预检请求，会浪费一些资源，所以预检的请求的响应可以设置一个时间限制，在这个时间范围内，符合之前预检请求要求的非简单请求就不用发送预检请求了。这个头的目的是设置这个预检请求响应的有效期，在有效期内容，满足这个响应的非简单请求都不需要再触发预检请求了。
Access-Control-Allow-Headers: X-Foo, X-Bar
  预检请求告诉浏览器这个跨域的正式请求可以携带哪些额外的头部
Access-Control-Allow-Credentials: xxxx
  是否允许正式的请求带上凭据信息（即登陆信息）
```
