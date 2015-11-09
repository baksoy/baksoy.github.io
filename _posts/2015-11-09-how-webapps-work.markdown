---
layout: post
title:  "How Web Applications Work"
date:   2015-11-06 09:34:52 -0500
categories: java update
---
## Http Protocol is a Request and Response protocol
* Client establishes connection with web server.
* Client sends web server a **Request**. This request has details of what operations the client wants.
* Web Server responds with a **Response** back to the web client. Content of the response has what was requested and status.
* Connection closes. Web server doesn’t remember anything about the past connections and who the clients were. That means another request coming from the same client is treated as a new request coming from a new client. Because of this, **HTTP** protocol is called a stateless protocol. 

This one pair of Http Request and Response is called an Http Transaction.

## URL

```java
public class AddHandler extends HttpServlet {	
		@Override 
		public void doGet(HttpServletRequest request, HttpServletResponse response){
			int x = Integer.parseInt(request.getParameter("x"));
			int y = Integer.parseInt(request.getParameter("y"));
	
			response.setContentType("text/html"); //set content type
	
			PrintWriter out = response.getPrintWriter();
			out.println("<!doctype html>\n" +
						"<html>\n" + 
						"<head><title>Sum of two numbers </title></head>\n " +
						"<body>" +
						"Sum of x and y is " + (x + y) +
						"</body></html>");
		}
	}
```

**`request.getParameter()`** searches for parameters not only in the query string of the URL but also in the body of the request. That means you can also read the form input fields in the post request which travel in the body of the request. Besides getting the parameters, the request object can be used for getting any other request data, for example the headers, cookies, session, method, etc.
  
We send a response to the client using the response helper object after setting the content type with the **response.setContentType()** method. Then, we instantiate a writer object PrintWriter to write to the response body. Then with **out.println()** method we send back to the client our response data. Note that the data goes straight to the client and is not held in the response object. Response is just a helper object and is not an intermediary space to store the data. After the **doGet()** method finishes executing, the container closes the connection with the client. 

In practice, you can do whatever you want in this **doGet()** method and are not limited to what's shown above. In this **doGet()** space you can:

* access a database  
* access other components like *EJBs*
* connect to other servers
* or whatever you can imagine under the sun





