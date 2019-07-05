## EL ignored in Servlet/JSP app
EL just doesn't work. Usually happens in archetype generated projects with maven tomcat 7 plugin (which a lot of people use since there is still no tomcat 8 plugin as of mid-2019). What's the solution? There are two wrong soltuions and one correct:

### 1. Deployment descriptor parameter
Add parameter to jsp-property-group of deployment descriptor (`web.xml` file inside WEB-INF):

```xml
<jsp-config>
  <jsp-property-group>
    ...
    <el-ignored>false</el-ignored>
  </jsp-property-group>
</jsp-config>
```
Why it is wrong:
1. It will likely be ignored. Yes, that's right.
2. You most likely won't expect it to be reuired since it is supposed to be the default behavior.

### 2. Per-file parameter
In each JSP file, add `@page` directive `isELIgnored="false"` parameter:
```xml
<%@ page ... 
        isELIgnored="false"="true" %>
```

In each custom tag file, add the same parameter to `@tag` directive:
```xml
<%@ tag ... 
        isELIgnored="false"="true" %>
```

Why it is bad?
1. Because you can forget it. You will forget it.
2. Again, you won't expect it to be necessary since it IS THE DEFAULT. Or is it?..

BTW, why you're here, add `trimDirectiveWhitespaces="true"`parameter of the same directive to remove unnecessary whitespace from resulting HTML.

### Correct:
Change Servlet version in deployment descriptor to 3.0 (max version supported by tomcat 7).
```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
```

Simple as that. A lot of archetypes have Servlet version 2.3 or less in the deployment descriptor. The default is for EL to be ignored below Servlet 2.4.
