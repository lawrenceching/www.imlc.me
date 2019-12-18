#  用 Wireshark 监控 HTTP/2 流量

相比 HTTP/1.1，HTTP/2 支持多路复用、支持服务端、支持 HTTP Header 压缩等特性极大地优化了网页的性能。
越来越多网站开始支持 HTTP/2。
需要查看 HTTP/2 流量的机会也肯定越来越多。
本文将简单地介绍如果用 Wireshark 查看 HTTP/2 的每个数据帧。

本文假设：
你已经装了 Wireshark
你已经装了 Node.js
你已经装了 Chrome

## 准备一个 HTTP/2 服务器程序

首先，先准备一个 HTTP/2 服务。
TLS 虽然不是标准中必需的。但是由于所有浏览器都只支持 TLS 下的 HTTP/2 通信，TLS 成为了一个事实上的标准。
也因此，我们需要先生成一张自签名证书，以便开启 TLS。

运行如下命令，该命令会在当前目录下生成一对证书文件。

```bash
openssl req -x509 -newkey rsa:2048 -nodes -sha256 -subj '/CN=localhost' -keyout localhost-privkey.pem -out localhost-cert.pem
```

接着，参考 [HTTP/2 | Node.js v12.12.0 Documentation](https://nodejs.org/api/http2.html#http2_server_side_example), 我们新建一个 `server.js` 文件，并输入下方 JavaScript 代码。

```javascript
const http2 = require(‘http2’);
const fs = require(‘fs’);

const server = http2.createSecureServer({
  key: fs.readFileSync(‘localhost-privkey.pem’),
  cert: fs.readFileSync(‘localhost-cert.pem’)
});
server.on(‘error’, (err) => console.error(err));

server.on(‘stream’, (stream, headers) => {
  // stream is a Duplex
  stream.respond({
    ‘content-type’: ‘text/html’,
    ':status': 200
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8443);
```

运行 `node server.js`，服务端就准备好了。
为了验证成果，运行 `curl -k -v https://localhost:8443` 你应该可以得到相应并且看到 `> GET / HTTP/2`，这意味着你确实跑在 HTTP/2 协议上。

## 导出 SSL Key Log File
由于开启了 TLS，Wireshark 捕捉到的流量是加密后的数据。你只能看到 TCP 层的报文而看不到 HTTP/2 的数据。
Chrome 和 Firefox 支持导出 TLS 的秘钥信息。Wireshark 需要此秘钥信息来解密 TLS 流量。

首先，你需要添加 SSLKEYLOGFILE 环境变量，并指向秘钥文件的保存地址。
然后，你还要以 `--ignore-certificate-errors` 启动 Chrome，否则 Chrome 会禁止你访问不安全的网站。

```
export SSLKEYLOGFILE=/<path-to-somewhere>/sslkeylogfile
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --ignore-certificate-errors
```

打开 https://localhost:8443/，点击高级 -> 继续访问网站，页面应该能正常打开，并且 sslkeylogfile 生成在你指定的目录下。

## 导入 TLS 秘钥信息

Protocols -> TLS, and configure the field -> (Pre)-Master-Secret log filename

![](/static/A03463B7-48F3-44D1-B7CA-60D793ADEF01.png)

## Inspect HTTP/2 Request

现在一切都准备就绪。

回到 Wireshark 主页，选择 Loopback 接口。

![](/static/5FEE5BF0-2499-4749-AEF6-98C971596CF7.png)

输入 `tcp.port == 8443` 过滤器，避免捕捉到太多无关的流量。

刷新一下 https://localhost:8443/
HTTP/2 流量跃然屏幕上。

![](/static/810F0EE9-0154-41B0-B6EB-88B6B446D092.png)

如果你看到不 TCP 的初始的几个报文（例如三次握手），你可能需要重启一下服务器程序以便已有断开 TCP 链接。

---

#HTTP/2 #Wireshark 