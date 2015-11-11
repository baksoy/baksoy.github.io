---
layout: post
title:  "How Web Applications Work"
date:   2015-11-06 09:34:52 -0500
categories: java update
---
## 1 - HTTP
**HTTP Protocol** is a **Request** and **Response** protocol and the communication between a web application client such as a web browser and a web application such as Apache Tomcat server works as follows:  

* The client first establishes a connection with the server.
* After the connection is established, the client sends the web server a **Request**. This request has details of what operations the client wants.
* The web Server responds with a **Response** back to the web client. Content of the response has what was requested and **status**.
* Finally, the connection gets closed. It's important to note that the web server doesn’t remember anything about the past connections and who the clients were. This means that another request coming from the same client is treated as a new request coming from a new client. Because of this, the **HTTP** protocol is called a **stateless protocol**. 

This one pair of HTTP Request and Response is called an **HTTP Transaction**.


## 2 - URL

Let's disect the following example URL to understand the pieces:

	http://www.example.com:8080/2015/10/15/up-and-running.html

`http` - this is the **scheme** of the above URL. Some other popular schemes are *ftp*, *mailto*, *file*, *data*. Schemes indicate how to access the resource that follows, in this case it indicates to access the resource via the http protocol.

`mailto:someone@gmail.com` - URL scheme of this one is mailto but mailto is not a protocol. So, URL's may have different schemes and they may not necessarily be protocols.

`www.example.com` - this portion is the **host**.
  
`8080` - this is the **port**. Requests from this port is routed to the application that is set up to handle requests coming from this port.

`2015/10/15/up-and-running.html` - this is the **URL path**. The web server uses this to identify the resource.

`www.example.com/some-folder/?couponCode=OFFER39` - the part that comes after the **?** is the **query string**. This string is passed to the web server. This is the **name value pair** that is passed on to the web server for processing.

`www.example.com/some-folder/?couponCode=OFFER39#discussions` - what comes after # is a **fragment**. It is like a bookmark in a resource - for example an *id of an element* in an html document. The sole purpose of a fragment in a URL is to show a particular portion of a resource at the client side. The fragment is not passed to the web server, because the web server returns the entire resource as a single unit - so the server does not need the fragment to process the information.



