# 为 Next.js 应用生成 robots.txt 和 sitemap.xml

> 为了优化 SEO，生成 robots.txt 和 sitemap.xml 是必不可少的功能。
> Next.js 自身并不提供生成 robots.txt 和 sitemap.xml 的特性，所以需要自己实现。


## Server Side Render(SSR)

### sitemap.xml.js

在 `pages/` 目录下创建 sitemap.xml.js。当浏览器访问 `http://<your-domain>/sitemap.xml` 路径时， Next.js 会调用 sitemap.xml.js 处理请求并响应。
在下面的例子中，我请求后台 API 获取 sitemap 数据，并返回给浏览器。

```javascript
import React, { Component } from "react";
import fetch from 'isomorphic-unfetch';

export default class SiteMap extends Component {
    static async getInitialProps({req, res}) {
        if(res) {
            const response = await fetch(`http://${req.headers['host']}/api/sitemap`);
            const text = await response.text();
            res.setHeader("Content-Type", "text/xml");
            res.write(text);
            res.end();
        }

    }
}
```

### robots.txt

同样地，在 `pages/` 目录下创建 `robots.txt` 文件。

```javascript
import React, { Component } from "react";

export default class Robots extends Component {
    static getInitialProps({ res }) {
        res.setHeader("Content-Type", "text/plain");
        res.write(`User-agent: *
Disallow:
Sitemap: https://www.imlc.me/sitemap.xml`);
        res.end();
    }
}
```

## Static Export

如果你是以生成静态文件的方式部署你的网站，那么事情就相当简单了。
因为生成的静态文件都在 `out/` 目录下，你只需要写个简单的脚本生成 robots.txt 和 sitemap.xml，并置于 `out/` 目录下。
具体方法我想不必多言。

## References
[https://github.com/zeit/next.js/issues/751#issuecomment-526303138](https://github.com/zeit/next.js/issues/751#issuecomment-526303138)  
[next.js/examples/with-sitemap-and-robots-express-server at canary · zeit/next.js · GitHub](https://github.com/zeit/next.js/tree/canary/examples/with-sitemap-and-robots-express-server)
[Sitemaps - Wikipedia](https://en.wikipedia.org/wiki/Sitemaps)
