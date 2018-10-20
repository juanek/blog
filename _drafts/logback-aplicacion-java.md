---
  layout: post
  title: "Agrega Logback a tu aplicación Java."
  date:   2018-10-13 21:56:00 -0300
  tags: java logback maven
  summary: "Una guía de Logback para usar en tu aplciación Java."
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >1 - Introducción.</a></li>
  <li><a href="#id-section2" >2 - Logger, Appenders y Layouts.</a></li>
  <li><a href="#id-section3" >3 - Configuración.</a></li>
  <li><a href="#id-section4" >4 - Ejemplo básico y configuración.</a></li>
  <li><a href="#id-section5" >5- Logger contextos.</a></li>
  <li><a href="#id-section6" >6 - Configuración detallada.</a></li>
  <li><a href="#id-section7" >7 - Appenders.</a></li>
  <li><a href="#id-section8" >8 - Layouts</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

Una guía para Logback.

<div id='id-section1'/>
## 1 - Introducción

Logback es uno de los frameworks de logging más populares en la comunidad Java, fue creado como sucesor del proyecto Log4j. Dado que el logging es una parte crucial de cualquier aplicación tanto para propósitos de debugger como de auditoría, elegir la adeucuada librería de logging es una decisión importante para cualquier proyecto.

¿Porqué elegir Logback?

- Es más rápido que Log4j.
- Arquitectura modular.
- Fácil de integrar con sistemas legacy.
- Altamente configurable.

Veremos la como está diseñado Logback y como usarlo para desarrollar mejores aplicaciones.

<div id='id-section2'/>
## 2 - Logger, Appenders y Layouts

Logback está desarrollado en torno a tres clases principales: **Logger**, **Appender** y **Layout**.

**Logger** es un contexto para los mensajes de log. Esta es la clase que interactúa con las aplicaciónes para crear mensajes de log.

Los **Appenders** son los lugares a donde serán dirigidos los mensajes de log. Un Logger puede tener más de un Appender. Generalmente pensamos en los Appenders como la consola o archivos de textos, pero Logback es más potente que esto.

La clase **Layout** es la encargada de darle formato a la salida de los mensages de log. Logback soporta la creación de clases personalizadas para el formateo de los mensajes, así como también robustas opciones de configuración para las ya existentes.

<div id='id-section3'/>
## 3 - Dependencias.

Necesitamos crear un proyecto maven de ejemplo y agregar las dependencias.

Logback usa Simple Logging Facade para Java (SLF4J) como su interfaz nativa. Así que necesitamos agregar Logback y SLF4J a nuestro pom.xml

{% highlight xml %}
<dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.3</version>
      <scope>runtime</scope>
</dependency>
<dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.25</version>
</dependency>
{% endhighlight %}

Maven central tiene la última version de Logback core y la versión mas reciente de slfj-api.


<div id='id-section4'/>
## 4 - Ejemplo básico.

Vamos a comenzar con un ejemplo sencillo de usar Logback en una aplicación.
Primero necesitamos un archivo de configuración. Creamos un archivo de texto llamado logback.xml y lo ponemos en algún lugar en nuestro classpath, en este caso en **/src/main/resources/logback.xml**
{% highlight console %}
├── pom.xml
├── README.md
├── src
│   ├── main
│   │   ├── java
│   │   │   └── ar
│   │   │       └── com
│   │   │           └── jekipes
│   │   │               ├── App.java
│   │   │               └── Simple.java
│   │   └── resources
│   │       └── logback.xml
{% endhighlight %}

{% highlight xml %}
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
{% endhighlight %}
Además necesitamos una clase simple con un método main. Para registrar un mensaje de log con Logback, inicializamos un Logger desde SLF4J o Logback y lo usamos

{% highlight java %}
public class Simple {

    private static final Logger logger = LoggerFactory.getLogger(Simple.class);

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        logger.info("Hello world from {}", Simple.class.getSimpleName());
    }
}
{% endhighlight %}

Ejecutamos nuestro programa

{% highlight console %}
$ mvn exec:java -Dexec.mainClass="ar.com.jekipes.Ejemplo"
21:00:50.444 [ar.com.jekipes.Ejemplo.main()] INFO  ar.com.jekipes.Ejemplo - Un log de Ejemplo
{% endhighlight %}

Esta clase crea un Logger y llama al método info() para generar un mensaje de log.
Es fácil ver porque Logback es tan popular, podemos configurarlo y comenzar a usarlo en minutos.

Esta configuración y este código nos dá unas pocas pistas de como trabaja.
- 1 - Tenemos un appender llamado STDOUT que referencia a la clase ConsoleAppender.
- 2 - Hay un tag <pattern> que describe el formato de nuestro mensaje de log.
- 3 - Nuestro código crea un Logger y le pasamos nuestro mensaje vía el método info()

Ahora que comprendemos lo básico , vamos a un análisis más detallado.

<div id='id-section4'/>
## 5- Logger
### 5.1 - Niveles de logs.

Como vimos arriba en el archivo **logback.xml** podemos configurar el logger para mostrar mensajes de distintos tipos, trace, error, info, etc.  Estos son los niveles de logs que maneja logback.
<div class="bs-callout bs-callout-info">
TRACE < DEBUG < INFO < WARN < ERROR
</div>

En nuestro ejemplo, el root looger está configurado a nivel `INFO`, si cambiamos `logger.info()` por `logger.debug()` veremos que no imprime nada. y si volvemos a reemplazar `logger.debug()` por `logger.warn()` veremos que el logger registra mensajes de log.
<div class="bs-callout bs-callout-success">
<b>REGLA: Cualquier logger con nivel X puede manejar mensajes de nivel Y, donde Y >= X.</b>
</div>

### 5.2 - Jerarquías de logs.
Otro concepto importante de la configuración de logs es la jeraquía de los mismos. Para comprender mejor este concepto vamos a modificar nuestro **logback.xml** con el siguiente contenido
{% highlight xml %}
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="ar.com.jekipes" level="INFO">
        <appender-ref ref="STDOUT" />
    </logger>
    <root level="INFO">
        <appender-ref ref="STDOUT"/>
    </root>
</configuration>
{% endhighlight %}
En la configuración de arriba tenemos dos logger, el logger **root** y el logger **ar.com.jekipes**. Agregamos el siguiente código y ejecutamos el programa.
{% highlight java %}
public class HierarchyLogs {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Logger logger = LoggerFactory.getLogger(HierarchyLogs.class.getSimpleName());
        logger.info("Hello world!!!");

        logger = LoggerFactory.getLogger(HierarchyLogs.class.getPackage().getName());
        logger.info("Hello world again!!");
    }

}
{% endhighlight %}
Este código nos imprimirá en consola
{% highlight console %}

08:13:55.231 [ar.com.jekipes.HierarchyLogs.main()] INFO  HierarchyLogs - Hello world!!!
08:13:55.234 [ar.com.jekipes.HierarchyLogs.main()] INFO  ar.com.jekipes - Hello world again!!
08:13:55.234 [ar.com.jekipes.HierarchyLogs.main()] INFO  ar.com.jekipes - Hello world again!!

{% endhighlight %}

Cuando logger.info() es llamado desde el looger **root** sólo una sentencia se imprime en cosnola pero cuando es invocado desde el logger **ar.com.jekipes** se imprime dos veces. Y esto se debe a la jerarquía de logs.
<div class="bs-callout bs-callout-success">
Regla: La jerarquía de logs significa que un logger (X.Y) es automáticamnte considerado padre de todos los loggers nombrados (X.Y.*). Si el logger padre está definido en la configuración, cualquiera de los mensajes de log dirigidos al logger hijo también seran manejados por el logger padre.
</div>
In our case, com.pawan.logger is parent logger of com.pawan.logger.test logger. When we tried to log via com.pawan.logger.test then as per log hierarchy rules, the parent level logger also handled the request & logged the message. Once again go the official documentation here & make sure that you clearly understand the concept of logger hierarchy.

### 5.3 - Parametrizando mensajes.
<div id='id-section6'/>
## 6 - Configuración detallada.
### 6.1 - Ubicando el archivo de configuración
### 6.2 - Configuración básica.
### 6.3 - Solución a problemas de configuración.
### 6.4 - Recarga automática de la configuración.
### 6.5 - Modificando Loggers
### 6.6 - Sustitución de variables.
<div id='id-section7'/>
## 7 - Appenders.
### 7.1 - ConsoleAppender.
### 7.2 - FileAppender.
### 7.3 - RollingFileAppender.
### 7.4 - Personalizando los Appenders
<div id='id-section8'/>
## 8 - Layouts
<div id='id-conclusiones'/>
## Conclusión.
<div id='id-referencias'/>
## Referencias

1 - Introducción.

Logback es uno de los frameworks de logging más populares en la comunidad Java. Es un reemplazo para su antecesor Log4j. Logback ofrece una implementación más veloz que Log4j, provee más opciones de configuración y más flexibilidad para archivar arcivos de logging antiguos.
Esta introducción nos introducirá en la arquitectura de Logback y muestra como lo puedes usar para hacer mejores aplicaciones.
