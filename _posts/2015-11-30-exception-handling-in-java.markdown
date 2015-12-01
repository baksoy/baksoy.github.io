---
layout: post
title:  "Understanding Exception Handling in Java"
date:   2015-11-30 09:34:52 -0500
categories: java
---
Exceptions occur when the regular program flow is interrupted by an unexpected behavior, which require special processing called **Exception Handling**.

**Checked Exceptions** - the checked exceptions that a method may raise are part of the method signature. Checked exceptions must be handled in a method or the method must declare the exception in a throws clause. If the program doesn't handle the errors, the compiler gives an error. When a method uses another method that throws an Exception, the calling method will either have to handle the exception with a try/catch block or throw the exception.

**Unchecked Exceptions** - also called RuntimeExceptions, occur by unforeseen circumstances at runtime such as hardware related issues. They are not enforced by the compiler to be handled. You can throw them or Java will throw them for you, eg. NullPointerException.

> Handle any exceptions as early as possible at the class that can do something about the exception.

`e.printStackTrace()` - shows all the methods called in reverse order.

Try to use validation instead of checked exceptions, because if you already know the types of exceptions that may occur, its best to take care of them on the stop by validating the data.

All exceptions and error types extend from `java.lang.Throwable`. Only Throwable objects can be thrown and caught.

![](http://i.imgur.com/7v6BEy0.png?1)
(source: https://newcircle.com/bookshelf/java_fundamentals_tutorial/exceptions)

Unchecked exceptions give programmers the power to ignore exceptions that they cannot recover from, and only handle the ones they can. This leads to less clutter. However, many programmers simply ignore unchecked exceptions, because they are by default un-recoverable. Turning all exceptions into the unchecked type would likely lead to poor overall error handling.


**Exception Lifecycle:**

* After an exception object is created, it is handed off to the runtime system (thrown).
* The runtime system attempts to find a handler for the exception by backtracking the ordered list of methods that had been called. This is known as the **Call Stack**.
* If a handler is found, the exception is caught. It is handled, or possibly re-thrown.
* If the handler is not found (the runtime backtracks all the way to the main() method), the exception stack trace is printed to the standard error channel (stderr) and the application aborts execution. 
	
for example:

	> java ExceptionDemo 100 0

	> Exception in thread "main" java.lang.ArithmeticException: / by zero
	> at ExceptionDemo.divideInts(ExceptionDemo.java:21)
	> at ExceptionDemo.divideStrings(ExceptionDemo.java:17)
	> at ExceptionDemo.divideArray(ExceptionDemo.java:10)
	> at ExceptionDemo.main(ExceptionDemo.java:4)

Looking at the list bottom-up, we see the methods that are being called from main() all the way up to the one that resulted in the exception condition. Next to each method is the line number where that method calls into the next method, or in the case of the last one, throws the exception.

Handling Exceptions flow:

![](http://i.imgur.com/e04IoGb.png?1)

Exception handling example:

	try {	
		// server will try to run this code with success and without any errors.
		// if your code is successfull, all your code here will execute and then 
		// jump past the catch block. 
	} catch(Exception e){
		// if you cannot run the code in the try block with success and you run into an 
		// Exception object, then catch it here and do something like e.getMessage() or 
		// e.printStackTrace() 
	} finally {
		// Run the code in this block no matter what
	}

When you wrap a bit of code in the try block, you can actually have more than one catch section. Each catch block should look for a specific exception. Your code path will change depending on which exception is generated.

By using Finally with your Try/Catch blocks, you ensure that you are cleaning up resources correctly and handling exceptions wherever you need in your code. 

Java 7 introduced try-with-resources which cleans up resources for you after running a code that might throw an exception so you don't have to do it in the finally block.
If you are working in Android, you wont be able to use this syntax.

> *Golden Rule:* Identify all the different exceptions that your code might generate, and then write catch blocks for each one of those. 

Example:

		String welcome = "Welcome!";
		
		char[] chars = welcome.toCharArray();
		
		try {
			char lastChar = chars[chars.length];  // Array index error
			System.out.println(lastChar);
			
			String sub = welcome.substring(10);
			System.out.println(sub);				 // String index error
		} catch (ArrayIndexOutOfBoundsException e) {
			System.out.println("Array index error occurred");
		} catch (StringIndexOutOfBoundsException e){
			System.out.println("String index error occurred");
		}


You can either rely on the exceptions thrown by the Java runtime and its core libraries, or generate your own exceptions (with throw new Exception) and give them custom error messages.

Example:

		try {
			if (chars.length < 10) {
				throw new Exception ("Length is smaller than 10");
			}
		} catch (Exception e) {
			System.out.println(e.getMessage());
		} 

You can also make your custom exception class by extending the Exception class and overriding the `getMessage()` method.
	
> A utility class is a class that defines a set of methods that perform common, often re-used functions. These common methods usually have static scope.  


### Assert

Assert is a way to test a condition in your code. For example, let's say you are going to pass a value in a method, and you wanted to make sure that that value matched a certain condition before you passed it in. You could assert that condition. You create the Assert command and then place the condition after it, and if the Assert is false, it will throw an exception.
	
	public void testMethod() throws Throwable {
		Assert.assertTrue(1 + 1 == 2);
	}

Asserts are only for testing. You can't depend on Assert for functionality in your code because they may be disabled. They only work when you add a certain command line value after the call to the virtual machine, namely when you add -ea in the VM arguments.

When you run the 'assert false' expression, the code will throw an exception. You can create a method that returns true if it runs correctly and false otherwise. Then run the 'assert yourMethod(arg)' statement to ensure the argument passed in your method returns a true so that your app doesn't go into the exception path. If your 'assert yourMethod(arg)' expression returns false, your code will throw an exception (assertion error) and any code after the expression will not be executed.

You can use asserts in your application wherever you want to check a condition. That allows you as the developer to debug and detect a condition that could kill your application at runtime.

> Tip: First make sure that your code runs correctly with the correct argument. Then, provide an incorrect argument to the method so that it throws a system exception. This will allow you to discover the type of exception java throws with the incorrect argument you provided. Now, you can handle that specific exception in a catch block.