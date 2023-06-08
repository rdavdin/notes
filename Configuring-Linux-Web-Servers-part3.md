## [Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299) - Part 3
[Part 1](/Configuring-Linux-Web-Servers-part1.md) [Part 2](/Configuring-Linux-Web-Servers-part2.md)

### Web Application Servers

[Apache HTTP Server Document](https://httpd.apache.org/docs/2.4/)

Setup your web application server **one piece at a time**, **testing each** as you progress.

#### The first step

Get your server responding to HTTP requests

Installing Apache HTTP Server

```
sudo apt update
sudo apt install apache2
```

Some additional packages that are installed with ```apache2``` so that it functions properly.

- ```apache2```: The main package that installs the Apache HTTP Server itself
- ```apache2-data```: This package contains the default configuration files, error pages, and other data files that Apache needs to function
- ```apache2-utils```: This package contains some useful utilities for working with Apache, such as ```htpasswd``` for managing user authentication.
- ```apache2-bin```: This package contains the Apache HTTP Server binary files.

#### The second step

Now, configure Apache to hand-off certain requests to an application handler like ```mod_wsgi```. 

1. Install ```mod_wsgi```. [What is mod_wsgi?](https://modwsgi.readthedocs.io/en/master/index.html)
```
sudo apt install libapache2-mod-wsgi
```

2. Configure Apache to handle requests using the WSGI module by editing the file ```/etc/apache2/sites-enabled/000-default```. This file tells Apache how to respond to requests, where to find the files for a particular site and much more ([Read Configuration Files on Apache](http://httpd.apache.org/docs/current/configuring.html)). Add the line ```WSGIScriptAlias / /var/www/html/myapp.wsgi``` into the end of the block ```<VirtualHost>...</VirtualHost>```. [Read more about WSGIScriptAlias](https://modwsgi.readthedocs.io/en/develop/configuration-directives/WSGIScriptAlias.html)

#### The third step

Now, write a very basic WSGI application.

**Remember:** WSGI is the _Web Server Gateway Interface_. It is a specification that describes how a web server communicates with web applications, and how web applications can be chained together to process on request. [Source](https://wsgi.readthedocs.io/en/latest/what.html)

Create the file ```/var/www/html/myapp.wsgi```
Then write a HelloWorld application with Python code as below:
```
def application(environ, start_response):
  status = '200 OK'
  output = 'Hello World!'
  response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
  start_response(status, response_headers)
  return [output]
```

That's it! Try reloading the browser with an appropriate url which is ```localhost:<configured_port>```. ```configured_port``` is the number port Apache is listening.

###### Some useful commands

```
sudo ln -s /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-enabled/000-default.conf
sudo unlink /etc/apache2/sites-enabled/000-default.conf
sudo systemctl restart apache2.service
```

###### [How to Use Apache as a Reverse-Proxy with mod_proxy on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-apache-http-server-as-reverse-proxy-using-mod_proxy-extension-ubuntu-20-04)

**Requirement 1**
There 2 sites: 
- site1 runs at port 8081 as the blog of the main site
- site2 runs at port 8082 as the main site
- The proxy is listening at port 80

**Configuration**
Steps to configure Apache

1. Create a new file under ```/etc/apache2/sites-available/```, for example the name ```reverse-proxy```
```
sudo touch /etc/apache2/sites-available/reverse-proxy
```

2. Edit the file ```reverse-proxy``` as below

```
<VirtualHost *:80>
        ProxyPreserveHost On

        ProxyPass /blog http://127.0.0.1:8081/
        ProxyPassReverse /blog http://127.0.0.1:8081/

        ProxyPass / http://127.0.0.1:8082/
        ProxyPassReverse / http://127.0.0.1:8082/
</VirtualHost>
```

- _ProxyPreserveHost_ directive: makes Apache pass the original ```Host``` header to the backend server.
- _ProxyPass_ directive: specifies that everything under the blog url ```/blog``` should be mapped to the backend server serving for the blog, and other things under th root url ```/``` should be mapped to the backend server serving for the main site.
- _ProxyPassReverse_ directive: tells Apache to **modify the response headers** from the backend server.

3. Create a symbolic link under ```/etc/apache2/sites-enabled/``` of the ```/etc/apache2/sites-available/reverse-proxy``` with the command

```
sudo ln -s /etc/apache2/sites-available/reverse-proxy.conf /etc/apache2/sites-enabled/reverse-proxy.conf
```

4. restart the apache2 service

```
sudo systemctl restart apache2
```

**Requirement 2**

This requirement is almost similar to the **Requirement 1**, but some additional points as below:

- Add a site 3 which runs at the port 8083 as one more instance of the site 2
- The proxy now runs as a load balancer that distributes the traffic of the main site to 2 instances named site 2 and site 3.

**Configuration**

Adjust the file ```reverse-proxy``` as below

```
<VirtualHost *:80>
        <Proxy balancer://mycluster>
                BalancerMember http://127.0.0.1:8082
                BalancerMember http://127.0.0.1:8083
        </Proxy>

        ProxyPreserveHost On

        ProxyPass /blog http://127.0.0.1:8081/
        ProxyPassReverse /blog http://127.0.0.1:8081/

        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
</VirtualHost>
```

- _Proxy_ block is used to define multiple servers. The block is named ```balancer://mycluster``` (or any name you want) and consists of one or more ```BalancerMember```s, which specify the underlying backend server addresses.

#### More info


1. *mod_node* is a handler similar to the module *mod_wsgi* but for _Nodejs_.
2. [Apache _Directive_ index](https://httpd.apache.org/docs/2.4/mod/directives.html)
3. Overlapping Webspace:
- _Location_ directive: the more **specific**, the more preferable
- But, _Alias_ or _ProxyPass_ directives: the **order** in which they appear in the configuration file determines which one is applied in case of overlap. 