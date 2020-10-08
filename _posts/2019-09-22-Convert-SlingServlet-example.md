---
layout: post
title: How to convert @SlingServlet to @Component in AEM 6.4
tags: [AEM, OSGI]
---


Last problem that I had to work was actually to convert all the apache felix annotation to the new R6 OSGI annotation. Anybody who is working in an AEM project knows that once you update your project to run with the AEM 6.4 server you have to update the annotation of all your services, servlets, components to the R6 OSGI.

In this post I will explain how this can be done for the servlets with examples.

## Code highlighting examples

A simple servlet which is using the @SlingServlet looks like this:

### Java

java example

```java
@SlingServlet(
    paths = {"/pathtest/content"},
    methods = {"GET", "PUT"},
    name = "com.servlet.TestServlet"
)
@Properties({
       @Property(name = Constants.SERVICE_VENDOR, value = "Test Vendor"),
       @Property(name = Constants.SERVICE_DESCRIPTION, value = "Test description"),
})

public class TestServlet extends SlingAllMethodsServlet {

    @Override
        protected void doGet(SlingHttpServletRequest request, @NotNull SlingHttpServletResponse response) throws IOException {
          responseHTML = "<div>This is a test Servlet</div>"
          response.setContentType("text/html");
          response.setCharacterEncoding("UTF-8");
          response.setStatus(200);
          response.getWriter().write(responseHTML);
        }
}
```
If we are converting the servlet above the we get the following result:

```java

@Component(
    immediate = true,
    service ={Servlet.class},
    property = {
        "sling.servlet.paths=/pathtest/content",
        "sling.servlet.methods=get",
        "sling.servlet.methods=put",
        Constants.SERVICE_VENDOR+"=Test Vendor",
        Constants.SERVICE_DESCRIPTION+"=Test description"
    }
)

public class TestServlet extends SlingAllMethodsServlet {

 @Override
        protected void doGet(SlingHttpServletRequest request, @NotNull SlingHttpServletResponse response) throws IOException {
          responseHTML = "<div>This is a test Servlet</div>"
          response.setContentType("text/html");
          response.setCharacterEncoding("UTF-8");
          response.setStatus(200);
          response.getWriter().write(responseHTML);
        }
}
```