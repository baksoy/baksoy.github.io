---
layout: post
title:  "How Web Applications Work"
date:   2015-11-27 09:34:52 -0500
categories: java update
---
### HTTP Protocol

**HTTP Protocol** is a **Request** and **Response** protocol and the communication between a web application client such as a web browser and a web application such as Apache Tomcat server works as follows:  

* The client first establishes a connection with the server.
* After the connection is established, the client sends the web server a **Request**. This request has details of what operations the client wants.
* The web Server responds with a **Response** back to the web client. Content of the response has what was requested and **status**.
* Finally, the connection gets closed. It's important to note that the web server doesn’t remember anything about the past connections and who the clients were. This means that another request coming from the same client is treated as a new request coming from a new client. Because of this, the **HTTP** protocol is called a **stateless protocol**. 

This one pair of HTTP Request and Response is called an **HTTP Transaction**.


### Understanding the URL

Let's look at the following example to understand what a **URL** consists of:

	http://www.example.com:8080/2015/10/15/up-and-running.html

`http` - this is the **scheme** of the above URL. Some other popular schemes are *ftp*, *mailto*, *file*, *data*. Schemes indicate how to access the resource that follows, in this case it indicates to access the resource via the http protocol.

`mailto:someone@gmail.com` - URL scheme of this one is mailto but mailto is not a protocol. So, URL's may have different schemes and they may not necessarily be protocols.

`www.example.com` - this portion is the **host**.
  
`8080` - this is the **port**. Requests from this port is routed to the application that is set up to handle requests coming from this port.

`2015/10/15/up-and-running.html` - this is the **URL path**. The web server uses this to identify the resource.

`www.example.com/some-folder/?couponCode=OFFER39` - the part that comes after the **?** is the **query string**. This string is passed to the web server. This is the **name value pair** that is passed on to the web server for processing.

`www.example.com/some-folder/?couponCode=OFFER39#discussions` - what comes after # is a **fragment**. It is like a bookmark in a resource - for example an *id of an element* in an html document. The sole purpose of a fragment in a URL is to show a particular portion of a resource at the client side. The fragment is not passed to the web server, because the web server returns the entire resource as a single unit - so the server does not need the fragment to process the information.


### Anatomy of an HTTP Request and HTTP Response Message

Let's invoke the following URL **Request** and analyze the **Response** we get: 

Request:  
`curl -v http://www.sanjaypatel.com/2014/07/up-and-running-with-spring-framework-4.html`

Response:
> Connected to www.sanjaypatel.com (184.73.237.244) port 80 (#0)
> GET /2014/07/up-and-running-with-spring-framework-4.html HTTP/1.1  
> Host: www.sanjaypatel.com  
> User-Agent: curl/7.43.0  
> Accept: */*  
> 
< HTTP/1.1 302 Moved Temporarily  
< Server: nginx/1.2.1  
< Vary: Cookie, Host, X-Forwarded-Proto  
< Vary: Accept-Encoding  
< X-Cache: MISS from denver  
< Content-Type: text/html; charset=utf-8  
< Date: Mon, 19 Oct 2015 18:04:21 GMT  
< Location: http://www.sanjaypatel.com/  
< Transfer-Encoding: chunked  
< X-From-Pagecache: skipped-gzip-only  
< X-Cache-Lookup: MISS from denver:3126  
< Via: 1.1 denver:3126 (squid/2.7.STABLE9)  
< Connection: Keep-Alive  
< 
{ [5 bytes data]  
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
Connection #0 to host www.sanjaypatel.com left intact  

`GET` - is the method that indicates to the server what to do with the resource.
`PUT, POST, DELETE, HEAD, TRACE` are some other methods.

What are **safe** and **idempotent** methods?  

`GET, PUT, DELETE, HEAD` are safe and idempotent methods, meaning they can be applied repeatedly without any problems.

`POST` is not not idempotent method because it processes data. eg. transferring money from one bank account to another.

`GET` request is sent when user clicks a link or types a url in the browser.

`POST` request can be used by sending HTML forms by setting the method attribute.
	
e.g. this form

	<form action="/user/create" method="post">
			First Name: <input type="text" name="firstName"></br>
			Last Name: <input type="text" name="lastName">
	</form>
<form action="/user/create" method="post">
			First Name: <input type="text" name="firstName" placeholder="John"></br>
			Last Name: <input type="text" name="lastName" placeholder="Smith">
	</form>  
	
will send a POST request to the server as follows:

	POST /users/create HTTP/1.1
	content-type:application/x-www-form-urlencoded;charset=utf-8  
	host: www.example.com
	content-length:30
	
	firstName=John&lastName=Smith
	
* To generate a **POST request** you can use a form with the post method 
* To generate a **GET request** you can use a URL or a form with the GET method

HTML (including HTML5) does not support **PUT** or **DELETE** methods. So, if you want to use the PUT or DELETE methods, you have to use **Ajax**, JavaScript, and generate the request programmaticaly from your code.

REQUEST LINE Starts with Method (GET, POST, PUT, HEAD, DELETE) followed with Path of resource and protocol and version (HTTP/1.1)

`Host: www.example.com`-  This is the host header with `key=Host` and `value=www.example.com`. The server may be hosting multiple urls so the host indicates which url is sought.  

`Accept: */*` -  The Accept request-header tells the server what kind of MIME type of the resource it wants. The Accept request-header field can be used to specify certain media types which are acceptable for the response. Accept headers can be used to indicate that the request is specifically limited to a small set of desired types, as in the case of a request for an in-line image.

For example:   
`Accept: audio/*; q=0.2, audio/basic`  
should be interpreted as "*I prefer audio/basic, but send me any audio type if it is the best available after an 80% mark-down in quality.*"

**RESPONSE LINE** starts with Protocol (HTTP/1.1) and Status (200, 301, 302, 404, 500, etc.)

`HTTP/1.1 200 OK` - indicates protocol and its version as well as response status code.

`Content-Type: text/html; charset=utf-8` - While the response is given, the server says this is the type I have (text/html; charset=utf-8). This is what I'm giving you, so try to use this. This is called **Content negotiation**.

`text/html` is the MIME type. Some other examples are text/css, application/pdf, image/bmp, image/png, audio/mpeg, audio/mpeg, audio/x-midi, etc.

You can read up on the other headers on the Internet.

The phrase "left intact" at the very end of the response line is a result of network optimization of the HTTP protocol version 1.1. However, as a developer you should assume that the connection is closed as soon as the response is sent.