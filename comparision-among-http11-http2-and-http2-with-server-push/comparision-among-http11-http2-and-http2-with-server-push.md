# Comparison among HTTP/1.1, HTTP/2 and HTTP/2 Server Push
This page aims to explore the theoretical improvement among HTTP/1.1, HTTP/2 and HTTP/2 Server Push.

> You can find out the source code in [GitHub - lawrenceching/jetty-http2-example](https://github.com/lawrenceching/jetty-http2-example)  

A use case is:
1. User opens https://localhost:8443 to access index page
2. Index page calls `/api/posts/:id`  that id is from 0 to 20 to simulate loading blog posts

To simulate network slowness, I add 5s delay for all apis.

## HTTP/1.1
Browsers have their limit for concurrent TCP connections. Such as in Chrome, only 6 connections are allowed per domain.

Below screenshot is the waterfall when I loads my demo page in HTTP/1.1
It clearly shows you that:
1. It need 5s to load index.html
2. And then the page started to load all posts by calling `/api/posts/:id`
Obviously you can see 6 max concurrent limitation here. Every 6 api call in a batch. It totally cost more than 20 seconds to finish all the loadings.

![](/static/AD52F6F1-FE65-4740-84A1-38375A746E65.png)

## HTTP/2
If I switch to HTTP/2, in theory, I can trigger all API calls at once.
And the waterfall proved it.
It take 10 seconds to load the page(5s to load index.html, 5s to load all posts).

![](/static/4F947599-83E4-46C0-AD45-CE61E01FA95B.png)

## HTTP/2 with Server Push
Server Push allows you to push assets to client without waiting browser requires it.
So, while browser is loading the index.html, server can kick off the process to push blog posts to browsers. Therefore, it can cut 5 second away in page load time.

![](/static/75F56C3C-5EDD-4CF6-9357-902B2D279DF4.png)
Above page is the waterfall if I enabled HTTP/2 Server Push and push all posts while browser was fetching index.html.
You can see a huge improvement here because calling `/api/posts/:id` only need around 1 or 2 ms.
If you look at the Initiator column, all `/api/posts/:id` requests were initialized by Server Push. When index.html was loading, Chrome started to receive `/api/posts/:id` data, and store in cache. So after the index.html completed and start to call `/api/posts/:id`, Chrome gave back the response from cache without trigger a real network connection.
