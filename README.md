# Cómo habilitar puerto para conectarse a través de AJP en un Tomcat embebido
Aplicación spring boot con Tomcat embebido que admite conexiones a través del protocolo AJP

### Qué es y para qué se usa AJP ###

El protocolo AJP (Apache Jserv Protocol) es un protocolo binario diseñado para la comunicación entre un servidor web
y varios servidores de aplicaciones. Especificamente los servidores que soportan este protocolo son, por el lado de servidores web Apache 1.x y 2.2,
y por el lado de los servidores de aplicaciones Tomcat y Jetty.

#### Por qué usarlo en lugar de HTTP ####

La razón principal es la eficiencia en términos de performance, debido a que los paquetes enviados son enviados en formato binario 
en lugar del texto plano usado por HTTP. Existen otras causas relevantes como la manera en que se manejan las conexiones TCP, intentando reutilizarlas 
a través de los ciclos de requests/responses.

### Configuración del proyecto MAVEN ###

#### Empaquetar como JAR ####

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

#### Dependencias ####

* Tomcat embebido

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

#### Clase de configuración ####

* Tenemos que sobre escribir el bean que se encarga de la creación del contenedor de aplicaciones embebido, para eso definimos la 
clase TomcatConfig de la siguiente forma:

```java
@Configuration
public class TomcatConfig { //El nombre puede ser cualquiera, lo importante es el contenido
    
    private static final String PROTOCOL = "AJP/1.3";

    @Value("${tomcat.ajp.port}") //Definido en el application.properties
    int ajpPort;

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

Si todo fue bien debemos ver en el log por consola las siguientes líneas:
```
org.apache.coyote.ajp.AjpNioProtocol     : Initializing ProtocolHandler ["ajp-nio-9090"]
org.apache.coyote.ajp.AjpNioProtocol     : Starting ProtocolHandler [ajp-nio-9090]
```
