---
layout: post
title:  "How Java Servlets Work I"
date:   2015-11-26 09:34:52 -0500
categories: java update
---

Suppose Servlets did not exist.

Your app would be a network app listening to Http requests and sending back Http responses. So when a request comes, it would read the Http request and then prepare and send back a response as per what is requested. Your web app can support multiple kinds of requests. That means it could support a GET request to a resource `/add` which could generate HTML response containing the sum of two numbers supplied as request parameters:
					
					GET /add?x=2&y=5


There could be another GET request for showing a user signup form like this:

 					GET /users/create


and a POST request for submitting the user signup form like this: 

 					POST /users/create


To get multiple such kind of requests, it would be good design to have separate routines for handling separate resources or better why not have a routine per resource per HTTP method (GET, POST, PUT, etc). **Thinking deeper, how about having a class per resource and a Java method in the class per HTTP method.** 

So, we can have a AddHandler.java class for `GET /add?x=2&y=5` request with a `doGet()` instance method to handle GET requests. We can have a UserCreator.java class for `GET /users/create` and `POST /users/create` with `doGet()` and `doPost()` instance methods. 

To handle the request, the routines will need the request data. For example, the value of x and y parameters above. So, we should parse the request data to a Java object so that we can provide that to the routines. The routines will do two things: i) build the response data, and ii) send the response to the client.

Sending the response to the client involves non-functional code common to all the routines. By non-functional code we mean code for creating the status line, the headers, the body of the response, serializing them, putting them in the response stream, etc. 

So, why not create a helper object to help sending the response and provide that object to the routines so that the routine can use that helper object to send the response data.

We also need to have another piece of code that will pick the right routine to call and it will do that by looking at the request method and the resource path because we are having routines based on the request method and the resource path. 

So, to summarize, Web Applications read HTTP requests -> they parse the request data and map them to a Java object -> they create a response object to help sending the response -> then looking at the request object they pick the right routine to call (GET, POST, etc) and provide the request and the response object to that -> they build the response data -> they send the response to the client

All the steps above are common to all web applications. So, why not package the common things as a product and let the developers use that product and write only business routines which are specific to the functionality of their applications. 

Many such products are available in the market: Tomcat, Glassfish, Weblogic, Jetty, JBoss etc. These are also called Containers.

Using these servlet containers, you will only have to code classes containing your application logic which are called **Servlets**.

Containers contain your servlets - hence the name. A Web Application could contain many servlets. Containers are not limited to only handling Http protocol. They handle any Request and Response protocol but they are used mostly for handling Http protocols. These containers are developed according to a common standard specification called the Java EE specification. Because they are all developed on the same specifications, your servlets can be deployed on any of them. 

#### How a container handles HTTP requests  

Client --> Container --> Servlet Classes (they inherit from HttpServlet class of the Java EE spec)

When a request comes from the client to the container, the container looks at the resource path and tries to find out which servlet to use for handling the requests. How does it do that? It does that by the xml config file called the deployment descriptor (**web.xml**) or some annotation-based metadata we provide that indicates the mapping from the resource paths to the servlets. If no servlet matches the resource path, it falls back to a static resource in that path.

Assuming that a servlet is found, the container then builds an object of type **HttpServletRequest** (a standard Java EE class) in which it puts all the request data in a good manner so that the servlet code can refer to it conveniently. Then the container builds another object **HttpServletResponse** to be used by the servlet code for sending the response conveniently. The container then creates a singleton object of the identified servlet - the servlet object is created only if it is not created earlier and that's why it's a singleton object. There is a way to enable the container to create non-singleton objects but it is not recommended.

Then the container calls the service method of the servlet object passing to it the request and the response objects created earlier:

				service(request, response); 

The service method then looks at the request object to find out the Http method (GET, POST, PUT, DELETE) and depending on that it calls either a doGet(request, response) or doPost(request, response). You have to write your code here inside the doGet() or doPost() routines for handling the business functionality and sending the response to the client. That means you write a Servlet class by inheriting from an HttpServlet class and overriding doGet(), doPost(), or whichever methods you want to handle.    

Let's look at a Servlet example to see how it works:

Your Wep App is listening for Http requests and gets an Http request `GET /add?x=5&y=7`. The xml/annotation map you configured maps the request to AddHandler.java class that extends from HttpServlet.java

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

`request.getParameter()` searches for parameters not only in the query string of the URL but also in the body of the request. That means you can also read the form input fields in the post request which travel in the body of the request. Besides getting the parameters, the request object can be used for getting any other request data - *e.g. the headers, cookies, session, method, etc.*

We send a response to the client using the response helper object after setting the content type with the `response.setContentType()` method. Then, we instantiate a writer object PrintWriter to write to the response body. Then with `out.println()` method we send back to the client our response data. Note that the data goes straight to the client and is not held in the response object. Response is just a helper object and is not an intermediary space to store the data. After the `doGet()` method finishes executing, the container closes the connection with the client. 

In practice, you can do whatever you want in this doGet() method and are not limited to what's shown above. In this `doGet()` space you can:

* access a database
* access other components like EJBs
* connect to other servers
* or whatever you can imagine under the sun

In a nutshell, the equation is `some servlets` + `configuration files` + `some static resources` + `some jar files` = `Web Application`

A container can host multiple web applications. Clients sends requests, web application does whatever needs to be done and responds with a response.

Although `doGet()` method is expected to be idempotent, nothing prevents you from writing code in it that is not idempotent like updating the database. But that would be bad design. Non-idempotent operations should go in `doPost()` methods.

**Redirection** - sometimes rather than handling the request with a response, you want to redirect the client to some other resource instead. A common scenario when redirection is needed is when a non-idempotent (eg. post) request comes and rather than handling the request directly and responding with a success message, you want to redirect the client to a different page which shows that success message. This way you end up driving the client away from the page that initiates the post and the URL in the browser changes so that even if the user refreshes the page, they don't end up posting the same request again. 

Some Common Features of Application Servers:

* Dispatch requests (Routing HTTP requests to servlets and serving responses)
* Thread Management (to deal with multithreading issues and correct thread behavior)
* Session Management (Http is stateless and thus a session manager is much needed)
* Caching and object pooling* (to prevent high consumption of system resources like memory) 

 *An object pool helps an application avoid creating new Java objects repeatedly. Most objects can be created once, used and then reused. An object pool supports the pooling of objects waiting to be reused. (source: ibm)
 
 
### Some topics to learn next

* How to configure your Web Application
* How to hook your routines when Servlets get initialized
* How to hook your routines to global events in your application, which are called Listeners
* How to maintain sessions. Remember HTTP is stateless but there are ways to remember the client using Sessions and Cookies. 
* How to code cross-cutting concerns using chain of Filters. You can have routines called Filters intercepting your Http transitions and they are used for coding cross-cutting concerns, eg. login 
* Security, or how to have authentication and authorization in your application
How to use multiple servlets together using include and forward for architecting your application well.
* Java Server Pages is a templating language which compile to servlets.
MVC Model - understand how servlets do the processing of the requests and forward them to JSP's for UI rendering.

If you want to jumpstart building web applications without going through a Servlets course, just learn some JSP, JSTL, EL and jump to a framework like the Spring or Play framework. Then you can learn about key concepts in servlets such as filters, security, etc.  
With the introduction of Spring 4 and Spring Boot, Spring Framework got very simplified.