# Why we need to disable TRACE method and how to disable TRACE in Embedded Jetty
This page show you why we need to disable TRACE HTTP method for security reason. And provide a sample project to show you how to disable TRACE in embedded Jetty.

## Why we need to disable TRACE method

Allowing TRACE method will leads to Cross-Site Tracking (a.k.a XST) problem. XST was found in 2003. It’s a very old topic and I can’t get any new discussion after searched in Google. But it’s good to disable TRACE for our server to avoid any redundant endpoint exposes to the danger world.

### Avoid XST Attack

As we all know, server will echo back all information when it receive TRACE request. “All information” means the cookies, the credential data and the server version etc. When user is visiting a malicious website, a hacker can read malicious data by calling a cross-site TRACE request.

### Limit Information Expose to the World

In the other case, we run an API server which user will not access by browser directly. It’s good practice to limit the information expose to the external world. Information like version may tell others which issue is ok to use to hack. If we don’t need TRACE (at lease in production environment), so disable it.

## How to disable TRACE in Embedded Jetty

By default Jetty enables TRACE and we need to disable it manually.
This [GitHub project](https://github.com/lawrenceching/jetty-disable-trace/blob/master/src/main/java/me/imlc/example/jetty_disable_trace/Main.java#L65) shows you how to disable TRACE for embedded Jetty.

```java
private Handler wrapWithSecurityHandler(Handler h){
        Constraint disableTraceConstraint = new Constraint();
        disableTraceConstraint.setName("Disable TRACE");
        disableTraceConstraint.setAuthenticate(true);

        ConstraintMapping mapping = new ConstraintMapping();
        mapping.setConstraint(disableTraceConstraint);
        mapping.setMethod("TRACE");
        mapping.setPathSpec("/");

        // omissionConstraint is to fix the warning log ""null has uncovered http methods for path: /
        // No impact to disable TRACE if you do not add this constraint
        // But if you're using the monitoring tool like Geneos, and your component requires keep production monitoring all green,
        // You can try to add this omissionConstraint to fix the warning Jetty prints.
        Constraint omissionConstraint = new Constraint();
        ConstraintMapping omissionMapping = new ConstraintMapping();
        omissionMapping.setConstraint(omissionConstraint);
        omissionMapping.setMethod("*");
        omissionMapping.setPathSpec("/");

        ConstraintSecurityHandler handler = new ConstraintSecurityHandler();
        handler.addConstraintMapping(mapping);
        handler.addConstraintMapping(omissionMapping);
        handler.setHandler(h);
        return handler;
    }
```

Also notice that I wrote omissionConstraint, applied to all method to fix a warning Jetty prints:
`null has uncovered http methods for path: /`

If you’r not required to keep production monitoring all green, you can delete this redundant constraint and accept this warning.

###### References

[1\] [CROSS-SITE TRACING (XST) - THE NEW TECHNIQUES AND EMERGING THREATS TO BYPASS CURRENT WEB SECURITY MEASURES USING TRACE AND XSS - Jeremiah Grossman](http://www.cgisecurity.com/whitehat-mirror/WH-WhitePaper_XST_ebook.pdf)  
[2\] [Cross Site Tracing - OWASP](https://www.owasp.org/index.php/Cross_Site_Tracing)  
[3\] [spring - Java embedded jetty is accepting HTTP TRACE method - Stack Overflow](http://stackoverflow.com/questions/29348328/java-embedded-jetty-is-accepting-http-trace-method)  
