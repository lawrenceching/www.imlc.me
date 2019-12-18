# How to serve one jersey resource or jetty servlet for different path
Today I'm trying to make an endpoint accessible in different paths. Such as I can go to `http://localhost/` and `http://localhost/hello` for a same hello message. 

I do got some solutions.

I'm using Jersey over Jetty. But whether or not you're using Jetty servlet or writing Jersey Resource, I think you will get some ideas.

Ps: I copy the code from my GitLab Snippets and will keep it update. So If you find something wrong, pls comment in GitLab

### Using the regular expression in Jersey Resource

[https://gitlab.com/snippets/1676612](https://gitlab.com/snippets/1676612)

```java
package me.imlc.example.jetty.multiplehandler;

import com.sun.jersey.spi.container.servlet.ServletContainer;
import me.imlc.example.jetty.multplehandler.HelloResource;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.ServletContextHandler;
import org.eclipse.jetty.servlet.ServletHolder;
import org.glassfish.jersey.server.ResourceConfig;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import java.util.Objects;

import static org.junit.Assert.assertEquals;

public class RegexpResourceTest {

    private static Server server;
    private static CloseableHttpClient client = HttpClients.createDefault();

    @BeforeClass
    public static void beforeAll() throws Exception {
        ResourceConfig rc = new ResourceConfig(RegexpResource.class);

        ServletHolder holder = new ServletHolder(new ServletContainer(rc));

        server = new Server(8080);

        ServletContextHandler contextHandler = new ServletContextHandler(server, "/*");
        contextHandler.addServlet(holder, "/*");

        server.start();
    }

    @Test
    public void canAccessByRootPath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @Test
    public void canAccessByServicePath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/service/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @AfterClass
    public static void afterAll() throws Exception {
        if(Objects.nonNull(server)) server.stop();
        if(Objects.nonNull(client)) client.close();
    }

    @Path("/{a:(.*)|service}")
    public static class RegexpResource {
        @GET
        @Produces(MediaType.TEXT_PLAIN)
        public String hello() {
            return "Hello, Lynn.";
        }
    }
}
```

### Specify multiple paths for one servlet

[https://gitlab.com/snippets/1676613](https://gitlab.com/snippets/1676613)

```java
package me.imlc.example.jetty.multiplehandler;

import com.sun.jersey.spi.container.servlet.ServletContainer;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.ServletContextHandler;
import org.eclipse.jetty.servlet.ServletHolder;
import org.glassfish.jersey.server.ResourceConfig;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import java.util.Objects;

import static org.junit.Assert.assertEquals;

public class MultipleServletTest {

    private static Server server;
    private static CloseableHttpClient client = HttpClients.createDefault();

    @BeforeClass
    public static void beforeAll() throws Exception {
        ResourceConfig rc = new ResourceConfig(RegexpResourceTest.RegexpResource.class);

        ServletHolder holder = new ServletHolder(new ServletContainer(rc));

        server = new Server(8080);

        ServletContextHandler contextHandler = new ServletContextHandler(server, "/*");
        contextHandler.addServlet(holder, "/*");
        contextHandler.addServlet(holder, "/service/*");

        server.start();
    }

    @Test
    public void canAccessByRootPath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @Test
    public void canAccessByServicePath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/service/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @AfterClass
    public static void afterAll() throws Exception {
        if(Objects.nonNull(server)) server.stop();
        if(Objects.nonNull(client)) client.close();
    }

    @Path("/hello")
    public static class MultipleServletResource {
        @GET
        @Produces(MediaType.TEXT_PLAIN)
        public String hello() {
            return "Hello, Lynn.";
        }
    }
}
```

### Serve one servlet in different context handler

[https://gitlab.com/snippets/1676615](https://gitlab.com/snippets/1676615)

```java
package me.imlc.example.jetty.multiplehandler;

import com.sun.jersey.spi.container.servlet.ServletContainer;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.eclipse.jetty.server.Handler;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.ServletContextHandler;
import org.eclipse.jetty.servlet.ServletHolder;
import org.glassfish.jersey.server.ResourceConfig;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import java.util.Objects;

import org.eclipse.jetty.server.handler.ContextHandlerCollection;

import static org.junit.Assert.assertEquals;

public class MultipleContextHandlerTest {
    private static Server server;
    private static CloseableHttpClient client = HttpClients.createDefault();

    @BeforeClass
    public static void beforeAll() throws Exception {
        ResourceConfig rc = new ResourceConfig(MultipleContextHandlerResource.class);

        ServletHolder holder = new ServletHolder(new ServletContainer(rc));

        server = new Server(8080);

        ServletContextHandler rootContext = new ServletContextHandler(server, "/*");
        rootContext.addServlet(holder, "/*");

        ServletContextHandler serviceContext = new ServletContextHandler(server, "/service/*");
        serviceContext.addServlet(holder, "/*");

        ContextHandlerCollection contextHandlerCollection = new ContextHandlerCollection();
        contextHandlerCollection.setHandlers(new Handler[]{
                rootContext, serviceContext
        });

        server.setHandler(contextHandlerCollection);

        server.start();
    }

    @Test
    public void canAccessByRootPath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @Test
    public void canAccessByServicePath() throws Exception {
        HttpGet get = new HttpGet("http://localhost:8080/service/hello");
        String content = EntityUtils.toString(client.execute(get).getEntity());
        assertEquals(content, "Hello, Lynn.");
    }

    @AfterClass
    public static void afterAll() throws Exception {
        if(Objects.nonNull(server)) server.stop();
        if(Objects.nonNull(client)) client.close();
    }

    @Path("/hello")
    public static class MultipleContextHandlerResource {
        @GET
        @Produces(MediaType.TEXT_PLAIN)
        public String hello() {
            return "Hello, Lynn.";
        }
    }
}
```
