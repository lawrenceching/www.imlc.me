# Remote Debugging for Java Application
It's an important feature that JVM provided and I'm so surprised that I used it at the first time today.

## How to enable remote debugging

To enable it, you will just need to add below JVM arguments in command line:

```
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005
```

Then you can use your debugger to attach to remote JVM.

`suspend` is quite useful argument here. If `suspend` is `y`, JVM will suspend before main class is loaded until debugger attach to it. `n` otherwise. 

So set `suspend` to `y` if you need to debug application initialization phase.

[Check Official Document for detailed introduction](https://docs.oracle.com/javase/7/docs/technotes/guides/jpda/conninv.html)

## Remote Debugging in IntelliJ IDEA

I believe all IDEs have that function to debug Java application remotely. I use IntelliJ here for demonstration.

![](/static/remote-debugging-for-java-application.png)

Add a new configuration under Remote category, you will see IntelliJ provides command line arguments for you. That's exactly what I show in previous section.

Change the host to your server's url, and launch your application with remote debug arguments, then you can debug as what you did on debugging local application.

And remember, **DO NOT REMOTE DEBUG YOUR PRODUCTION APPLICATION**
