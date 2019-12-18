#  Inspect HTTP/2 in Wireshark

This page will demonstrate how to inspect HTTP/2 request in WireShark.

## Start Up and HTTP/2 Server
First of all, we need to create a HTTP/2 server in order to serve the HTTP/2 API.

Refer to [HTTP/2 | Node.js v12.12.0 Documentation](https://nodejs.org/api/http2.html#http2_server_side_example), generate a self signed certificate:
```
openssl req -x509 -newkey rsa:2048 -nodes -sha256 -subj '/CN=localhost' -keyout localhost-privkey.pem -out localhost-cert.pem
```

Create `server.js` with below content and run `node server.js` to start up the server.

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

Now you can access https://localhost:8443.
For example, you can do `curl -k -v https://localhost:8443` and you will find `> GET / HTTP/2` in the output.

## Export SSL Key Log File in Chrome
Given that SSL in HTTP/2 is  de facto mandatory, you can see nothing in Wireshark if you don’t tell Wireshark SSL secure file. 

To do so, you need to add SSLKEYLOGFILE env variable. Chrome( or Firefox) will save the SSL session to that file when it loads a HTTPS website.

```
export SSLKEYLOGFILE=/<path-to-somewhere>/sslkeylogfile
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --ignore-certificate-errors
```

Open https://localhost:8443/ in Chrome, now you can see sslkeylogfile  is generated under the path you specified.

## Import SSL Key Log File in Wireshark
Next step, you need to tell Wireshark where the file is.
Open the Preference panel, on the left side menu, go to Protocols -> TLS, and configure the field “(Pre)-Master-Secret log filename”. 

![](/static/A03463B7-48F3-44D1-B7CA-60D793ADEF01.png)

## Inspect HTTP/2 Request
Now get back to the home page, select the loopback interface to capture.
![](/static/5FEE5BF0-2499-4749-AEF6-98C971596CF7.png)

Set a `tcp.port == 8443` filter  to prevent noise requests flow in.

**Stop and start your HTTP/2 server** to clean up the TCP connection. Because browser keeps the TCP connection alive, you may not able to see the initial packets in Wireshark.

Ok, now you’re ready to inspect the HTTP/2 Request. Access https://localhost:8443/ again.

Several HTTP/2 requests show up in the screen. You can see the features only in HTTP2, such as SETTINGS, HEADERS, and DATA stream. You can see the Stream ID which is the key for multiplexing.
![](/static/810F0EE9-0154-41B0-B6EB-88B6B446D092.png)

---

#HTTP/2 #Wireshark 