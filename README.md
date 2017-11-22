# How to enable AJP connector on an embedded Tomcat
Spring boot application with an embedded Tomcat that allows connections through AJP protocol

### What is AJP and for what is it used for? ###

The AJP protocol (Apache JServ Protocol) is a binary protocol designed for the communication between a web server and
other application servers. The servers that specifically supports this protocol are, on the web server side, Apache 1.x
and 2.2, and, on the application server side, Tomcat and Jetty.

#### Why to use it instead of HTTP ####

The main reason is performance, because the sent packages are sent on binary format instead of plane text used by HTTP.
There are more relevante causes such as the way that TCP handle connections, AJP try to reuse through request/responses 
cycles.

### Maven project configuration ###

#### Package as JAR ####

```xml
<packaging>jar</packaging>
```

#### Spring boot 1.4.3 ####

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.3.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

#### Dependencies ####

* Embedded Tomcat

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

#### Configuration class ####

* We have to define the bean that handle the embedded application container creation, we define this TomcatConfig class
as follows to achieve that:

```java
@Configuration
public class TomcatConfig { //The class name can be anything
    
    private static final String PROTOCOL = "AJP/1.3";

    @Value("${tomcat.ajp.port}") //Defined on application.properties
    private int ajpPort;

    @Bean
    public EmbeddedServletContainerFactory servletContainer() {
        TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory();
        Connector ajpConnector = new Connector(PROTOCOL);
        ajpConnector.setProtocol(PROTOCOL);
        ajpConnector.setPort(ajpPort);
        tomcat.addAdditionalTomcatConnectors(ajpConnector);
        return tomcat;
    }
}
```

If all went well, when we execute it, we should see on the logs the following lines:
```
org.apache.coyote.ajp.AjpNioProtocol     : Initializing ProtocolHandler ["ajp-nio-9090"]
org.apache.coyote.ajp.AjpNioProtocol     : Starting ProtocolHandler [ajp-nio-9090]
```
