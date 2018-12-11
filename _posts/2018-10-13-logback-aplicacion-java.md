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
$ mvn exec:java -Dexec.mainClass="ar.com.jekipes.Simple

12:58:26.895 [ar.com.jekipes.Simple.main()] INFO  ar.com.jekipes.Simple - Hello world from Simple

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

Cuando logger.info() es llamado desde el looger **root** sólo una sentencia se imprime en consola pero cuando es invocado desde el logger **ar.com.jekipes** se imprime dos veces. Y esto se debe a la jerarquía de logs.


Para evitar esto, agregamos la propiedad additivity=false. De esta manera sólo el logger **ar.com.jekipes** manejará los mensajes.

{% highlight xml %}

    <logger name="ar.com.jekipes" level="INFO" additivity=false>
        <appender-ref ref="STDOUT" />
    </logger>

{% endhighlight %}

La configuración de los Logger se realiza de manera jerárquica, en donde el Logger `padre` de todos los demás es `root`. Cada Logger adicional se jerarquiza basado en su nombre y usando puntos como separador, así un Logger de nombre `ar.com.jekipes` es `padre` de `ar.com.jekipes.util` por lo que este último hereda la configuración definida en `logback.xml` para su Logger `padre`.

<div class="bs-callout bs-callout-success">
<b>Regla: La jerarquía de logs significa que un logger (X.Y) es automáticamnte considerado padre de todos los loggers nombrados (X.Y.*). Si el logger padre está definido en la configuración, cualquiera de los mensajes de log dirigidos al logger hijo también seran manejados por el logger padre.</b>
</div>


### 5.3 - Parametrizando mensajes.
<div id='id-section6'/>

Logback permite usar multiples parámetros y además el último argumento puede ser tratado como una exception.

{% highlight java %}

logger.error("one two three four five: {} {} {} {} {}", "1", "2",
             "3","4", "5",new Exception("error"));

{% endhighlight %}

Ejecutamos este código y en consola veremos
{% highlight console %}

20:33:19.903 [main] ERROR ParametersMessagesTest - one two three four fivr: 1 2 3 4 5
java.lang.Exception: error
	at ar.com.jekipes.ParametersMessagesTest.parameters1(ParametersMessagesTest.java:54)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)

{% endhighlight %}


## 6 - Configuración detallada.


### 6.1 - Ubicando el archivo de configuración

Los pasos de inicialización que Logback sigue para intentar configurarse son los siguientes

   * Logback intenta encontrar un archivo llamado **logback-test.xml** en el classpath.
   * Sino intenta encontrar el archivo llamado **logback.groovy** en el classpath.
   * Sino busca el archivo **logback.xml** en el classpath.

Si no encuentra ningun archivo de configuración, logback se configura a si mismo usando el **BasicConfigurator** el cual causa la salida de los mensajes de log directamente de consola.

Con un proyecto Maven, puedes usar el archivo **src/test/resources/logback-test.xml** y el archivo **src/main/resources/logback.xml** para producción.

{% highlight console %}
logback-quickstart$ tree
.
├── config
│   └── config.xml
├── nbproject
│   └── project.properties
├── pom.xml
├── README.md
├── src
│   ├── main
│   │   ├── java
│   │   │   └── ar
│   │   │       └── com
│   │   │           └── jekipes
│   │   │               ├── App.java
│   │   │               ├── HierarchyLogs.java
│   │   │               └── Simple.java
│   │   └── resources
│   │       └── logback.xml
│   └── test
│       ├── java
│       │   └── ar
│       │       └── com
│       │           └── jekipes
│       │               ├── AppTest.java
│       │               └── ParametersMessagesTest.java
│       └── resources
│           └── logback-test.xml

{% endhighlight %}

Otra opción, logback permite especificar un archivo de configuración usando el parámetro **logback.configurationFile**.


{% highlight console %}

 mvn exec:java -Dexec.mainClass="ar.com.jekipes.Simple" -Dlogback.configurationFile=/path/to/logback-quickstart/config/config.xml

{% endhighlight %}

### 6.3 - Solución a problemas de configuración.

Para ver la información de depuración de cómo Logback procesa la configuración, puedes activar el modo debug.
{% highlight xml %}
  <configuration debug="true">
  ...
  </configuration>
{% endhighlight %}

Hay un segundo mecanismo para imprimir la información de estado:
{% highlight xml %}
<configuration>
    <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />
    ...
</configuration>
{% endhighlight %}
El StatusListener, intercepta los mensajes de estado y los imprime durante la configuración y mientras el programa está corriendo.

### 6.4 - Recarga automática de la configuración.

Recargar el archivo de configuración de logback mientras la aplicación está corriendo es una poderosa herramienta de solución de problemas. Logback hace esto posible con el parámetro scan
{% highlight xml %}
<configuration scan="true">
  ...
</configuration>
{% endhighlight %}

El comportamiento por defecto es escanear el archivo de configuración en busca de modificaciones cada 60 segundos. Se modifica este intervalo agregando  scanPeriod
{% highlight xml %}
<configuration scan="true" scanPeriod="15 seconds">
  ...
</configuration>
{% endhighlight %}
Podemos especificar valores en "milliseconds", "seconds", "minutes", o "hours".

<div id='id-section7'/>
## 7 - Appenders.
### 7.1 - ConsoleAppender.

Como su nombre lo describe, agrega mensajes al System.out o al System.err y es el archivo **logback.xml** que hemos estado usando hasta ahora.

### 7.2 - FileAppender.

FileAppender agrega mensajes a un archivo. Agregamos el appender *"name=File"* a nustro archivo de configuración y lo referenciamos desde el logger, así

{% highlight xml %}
<configuration debug="true">

    <property name="PATH" value="/home/juan/logs" />

    <property name="APP" value="myapp" />

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>${PATH}/${APP}.log</file>
        <append>true</append>
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="ar.com.jekipes" level="INFO" additivity="false">
        <appender-ref ref="FILE" />
    </logger>
	<logger name="ar.com.jekipes.Simple" level="TRACE" >
        <appender-ref ref="STDOUT" />
    </logger>
    <root level="OFF">
        <appender-ref ref="STDOUT"/>
    </root>
</configuration>
{% endhighlight %}

Usamos el tag <file> para configurar el nombre del archivo y nos valemos de la definición de las variables **PATH** y **APP** para construir el nombre completo del archivo de log.

El tag <append> instruye a Appender a agregar los logs a un archivo si este existe.

La propiedad additivity en false del logger **"ar.com.jekipes"** permitirá no registrar los mensajes de logs en consola ni ninguno de sus descendiente y sólo se mostrarán el archivo de log.

Para probar esta configuración ejecutamos el programa invocando directamente el archivo de configuración

{% highlight console %}
mvn exec:java -Dexec.mainClass="ar.com.jekipes.Simple" -Dlogback.configurationFile=path/to/logback-quickstart/config/configFileAppender.xml
{% endhighlight %}


### 7.3 - RollingFileAppender.

RollingFileAppender nos brinda la posibilidad de rotar los archivos de log de acuerdo a alguna politica.
Para este ejemplo usamos el **TimeBasedRollingPolicy**, que define una política basada en tiempo, por ejemplo por dia o por mes.
Con esta configuración logback rotará los archivos diariamente y los moverá al directorio **PATH_BACKUP** comprimidos en formato gz. Se mantendrán hasta 30 archivos o hasta que estos archivos superen los 3GB, eliminando los más antiguos.

{% highlight xml %}
<configuration debug="true">

    <property name="PATH" value="/home/juan/logs" />
    <property name="PATH_BACKUP" value="/home/juan/logs/backups" />

    <property name="APP" value="myapp" />

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${PATH}/${APP}.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${PATH_BACKUP}/${APP}.%d{yyyy-MM-dd}.gz</fileNamePattern>
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="ar.com.jekipes" level="INFO" additivity="false">
        <appender-ref ref="FILE" />
    </logger>
    <logger name="ar.com.jekipes.Simple" level="TRACE" >
        <appender-ref ref="FILE" />
    </logger>
    <root level="OFF">
        <appender-ref ref="FILE"/>
    </root>
</configuration>
{% endhighlight %}


{% highlight console %}
mvn exec:java -Dexec.mainClass="ar.com.jekipes.Simple" -Dlogback.configurationFile=path/to/logback-quickstart/config/configRollingAppender.xml
{% endhighlight %}

<div id='id-section8'/>
## 8 - Layouts

Los Layouts formatean los mensajes de log. Como en el resto de Logback, los Layouts se pueden extender y crear propios, Sim embargo, el aptron por defecto PatterLayout ofrece lo que la mayoría de las aplicaciones necesitan y mas.

Hemos usado el patrón PatternLayout en todas nuestros ejemplos hasta ahora

{% highlight console %}
<encoder>
    <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
{% endhighlight %}

Esta parte de la  configuración contiene la configuración del PatterLayoutEncoder. Le pasamos un Encoder a nuestro Appender, y este enconder usa el PatternLayout para formatear los mensajes.
El texto de tag <pattern> define como los mensajes de log son formateados. PatternLayout implemnta una larga variedad de conversión de palabras y modifcadores de formatos para crear patrones.

Vamos a descomponer esto. PatternLayout reconoce las palabras de conversión con un %, de esta manera las conversiones en nuestro patrón genera:

* **%d{HH:mm:ss.SSS}** – un timestamp con horas, minutos, segundos y milisegundos.
* **[%thread]** – el nombre del hilo que genera los mensajes de log entre corchete
* **%-5level** – el nivel del evento de log en un bloque de 5 caracteres.
* **%logger{36}** – el nombre del logger, limitado a 35 caracteres.
* **%msg%n** – los mensajes de log seguido de un separador de linea dependiente de la plataforma.

<div id='id-conclusiones'/>
## Conclusión.

Logback es uno de los frameworks de logging más populares en la comunidad Java. Es un reemplazo para su antecesor Log4j. Logback ofrece una implementación más veloz que Log4j, provee más opciones de configuración y más flexibilidad para rotar archivos de logs.
Herramienta imprescindible para hacer mejores aplicaciones.

<div id='id-referencias'/>
## Referencias


[Manual de Logback](https://logback.qos.ch/manual/index.html)

[https://www.baeldung.com/logback](https://www.baeldung.com/logback)

[https://stackoverflow.com/](https://stackoverflow.com/)
