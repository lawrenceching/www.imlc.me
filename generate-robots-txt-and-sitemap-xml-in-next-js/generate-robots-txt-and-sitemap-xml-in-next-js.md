# Generate robots.txt and sitemap.xml in Next.js

> For the SEO optimization, it’s necessary to support robots.txt and sitemap so that search engineer can know your website easily. Next.js does not support robots.txt and sitemap.xml so that we need to implement by ourselves.

## Server Side Render(SSR)
### sitemap.xml.js

Create sitemap.xml.js under `pages/` folder. And browser accesses `http://<your-domain>/sitemap.xml`, Next.js will route the request to sitemap.xml.js. Now you can return whatever content your want in sitemap.xml.

In my case, I ask my backend API for sitemap data.

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

Similar with creating sitemap.xml, create `robots.txt` under `pages` folder with below content.

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
If you use static export feature, thing is much much more easier.
Your static assets are generated under `out/` folder.
So you can write a script to generate robots.txt and sitemap.xml under `out/`. I believe I don’t need to explain how to do that : ).

## References
[https://github.com/zeit/next.js/issues/751#issuecomment-526303138](https://github.com/zeit/next.js/issues/751#issuecomment-526303138)  
[next.js/examples/with-sitemap-and-robots-express-server at canary · zeit/next.js · GitHub](https://github.com/zeit/next.js/tree/canary/examples/with-sitemap-and-robots-express-server)
[Sitemaps - Wikipedia](https://en.wikipedia.org/wiki/Sitemaps)
