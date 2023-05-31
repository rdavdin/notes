## HTTP and Web Servers
#### Run a demo server with Python
- cd to a directory
- Run
  ```
  python3 -m http.server 8000
  ```
- Try access http://localhost:8000/ from your browser

#### Three visible parts of a URI (_Uniform Resource Identifier_)

```https://en.widipedia.org/wiki/Fish```

- ```https```: **scheme**
- ```en.wikipedia.org```: **hostname**
- ```/wiki/fish```: **path**

##### Scheme
- _Scheme_ tells the client **how to go about accessing the resource**
- **http**, **https**: tells ... 'Well, resources served by a web server'
- **file**: tells ... 'Well, clients access a file on **the local filesystem**
- There are many other URI schemes [the official list](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml)

##### Hostname
- _Hostname_ tells the client **which server** to connect to
- ```www.udacity.com``` or ```localhost```

##### Path
- _Path_ tells the server which resource the client is looking for.

##### Relative URI references
- ```<a href="cliffsofinsanity.png">cliffsofinsanity.png</a>```
- Without _scheme_ or _hostname_
- It's 'relative' to the context in which it appears - specially, the page it's on. In other words, the path is based on the path of the current page to determine.

### Hostnames and Ports
- try ```host localhost``` or ```host www.google.com```
- Thanks to **DNS** to lookup an ip address of a hostname
- **ip-address** to determine the correct **computer** on the network
- **port number** to determine the correct **program** on the computer
- A web server is 'listening on' a port, such as 80 or 8000. 'Listening' means that when the server, a program on a computer, starts up, it tells its operating system that it wants to receive connections from clients on a particular port number. So, when a client 'connects to' that port and sends a request, the operating system knows to forward that request to the server that's listening on that port.
- **_Note_**: For historical reasons, operating systems only allow the administrator (or root) account to **listen** on ports **below 1024**. This is fine for production web servers, but it's not convenient for learning. 
- port 80 is default for HTTP URIs
- port 443 is default for HTTPS URIs
- port 22 is default for SSH

#### Send a request manually with _ncat_
```ncat 127.0.0.1 8000```: to connect the terminal to the server
Then try sending a GET request manually:
```
GET / HTTP/1.1
Host: localhost <enter twice>
```
#### Be a web server with _ncat_
- Use ```ncat -l 9999``` to listen on port 9999
- Access http://localhost:9999/ with your web browser
- In the terminal, receive the GET request from the browser
- Response the browser with this response:

```
HTTP/1.1 200 OK
Content-type: text/plain
Content-length: 15

Hello, world!!!
```

- The browser receives the response with content _Hello, world!!!_

## What's an Apache or Nginx or Microsoft IIS?
- The Web was originally designed to serve documents, not to deliver applications. Even today, a large amount of the data presented on any web site is _static content_ - images, HTML files, videos, downloadable files, and other media **stored** on disk.
- Specialized web server programs - like [Apache](https://httpd.apache.org/), [Nginx](https://www.nginx.com/resources/wiki/), or [IIS](https://www.iis.net/) - can serve **static content** from disk storage very quickly and efficiently. They can also provide **access control** allowing only authenticated users to download particular static content.

##### Routing and load balancing
- Some web applications have several different server components, each running as a **separate process**. One thing a specialized web server (Apache, Nginx, IIS) can do is **dispatch requests** to the particular backend servers that need to handle each request. This is called **_request routing_** or **_reverse proxying_**
- Some web applications need to do a lot of work on the server side for each request, and need many servers to handle the load. Splitting requests up among several servers is called **_load balancing_**. 
- For instance, you have three servers running the **same** application, and a _load balancer_ sits in front of them. When a request comes in, the _load balancer_ can decide which server to send the request to based on factors such as **server availability, server load, or geographic location.** This helps ensure that each server is **handling a manageable amount of traffic**.
- _Load balancing_ also helps handle conditions where one server becomes unavailable, allowing other servers to pick up the slack.

##### _In-flight request_
- _In-flight request_ meaning that the request **has 'taken off'** from the client to the server, but the response **has not 'landed'** again back at the client. 
- A web service **cannot** just handle one request at a time and then go on to the next one; it has to be able to handle **many** at once.

##### Quiz Question
> In September 2016, the English Wikipedia received about 250 million page views per day. That's an average of about 2,900 page views every second. Let's imagine that an average page view involves three HTTP queries (the page HTML itself and two images), and that each HTTP query takes 0.1 seconds (or 100 milliseconds) to serve. **About how many requests are in flight at any instant?**

```
- 2,900 page views/second + 3 queries/view
--> 8,700 queries/second
- 1 query takes 100 milliseconds (or 0.1 s)
```

> So, about **870 requests** are going to be _in-flight_ at any instant. In other words, the server has to be able to process about **870 requests** at once.

#### Caching
- _Cache_ is a temporary storage for resources that are likely to be reused.
- Web systems can perform caching in a number of **places** - but all of them are **under control of the server** that serves up a particular resource thanks to setting **HTTP headers**
- Some places can cache resources:
  - Users' browser maintains a _browser cache_ of cacheable resources - such as images from recently-viewed web pages.
  - _Web proxy_: The browser can also be configured to pass requests through a _web proxy_, which can perform caching on behalf of many users.
  - _Reverse proxy_: a website can use a _reverse proxy_ to cache results so they don't need to be recomputed by a slower application server or database.
  - **Note:** All HTTP caching is supposed to be governed by _cache control_ headers set by **the server**. Read more in [this article](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching).

#### Cookies
##### What are cookies?
- Cookies are a way that a _server_ can ask a _browser_ to retain a piece of information, and send it back to the server when the browser makes subsequent requests.
- Every cookie has a name and a value, much like _variable_ in your code
- It also has **rules** that specify when the cookie should be sent back.

##### What are cookies for?
- If the server sends **each client** a **unique** cookie value, it can use these to tell clients apart. This can be used to implement higher-level concepts on top of HTTP requests and responses - things like _session_ and _login_.
- Cookies are used by analytics and advertising systems to track user activity from site to site.
- Cookies are also sometimes used to store user preferences for a site.

##### How cookies happen?
- The first time the client makes a request to the server, the sever sends back the response with a **Set-Cookie** header.
- This header contains three things: _a cookie name_, _a value_, and _some attributes_.
- Every subsequent time the browser makes a request to the server, it **will** send that cookie back to the server.
- The server can update cookies, or ask the browser to expire them.

#### More Resources
- [Mozilla Developer Network's HTTP index page](https://developer.mozilla.org/en-US/docs/Web/HTTP) contains a variety of tutorial and reference materials on every aspect of HTTP.
- The standards documents for HTTP/1.1 start at [RFC 7230](https://tools.ietf.org/html/rfc7230). The language of Internet standards tends to be a little difficult, but these are the official description of how it's supposed to work.
- The standards documents for HTTP/2 are at https://http2.github.io/.
- If you already run your own web site, [Let's Encrypt](https://letsencrypt.org/) is a great site to learn about HTTPS in a hands-on way, by creating your own HTTPS certificates and installing them on your site.
- [HTTP Spy](https://chrome.google.com/webstore/detail/http-spy/agnoocojkneiphkobpcfoaenhpjnmifb?hl=en) is a neat little Chrome extension that will show you the headers and request information for every request your browser makes.