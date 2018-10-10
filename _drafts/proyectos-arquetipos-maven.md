---
  layout: post
  title: "Cómo crear proyectos y arquetipos con Maven."
  date:   2018-09-26 17:03:00 -0300
  tags: java maven
  summary: "Maven es una herramienta para crear proyectos Java y template de proyectos Java."
---

En esta guía vamos a crear proyectos Java con Maven directamente desde la consola de comandos de manera interactiva y no-interactiva.
Además, generaremos un arquetipo desde un proyecto existente.


<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >1 - Crear proyectos en modo interactivo.</a></li>
  <li><a href="#id-section2" >2 - Crear proyectos en modo no-interactivo.</a></li>
  <li><a href="#id-section3" >3 - Crear arquetipos desde un proyecto.</a></li>
  <li><a href="#id-section4" >4 - Compilar y construir proyectos.</a></li>
  <li><a href="#id-section5" >5 - Ejecutar programas.</a></li>
  <li><a href="#id-section6" >6 - Ejecutar pruebas.</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

<div id='id-section1'/>
## 1 - Crear proyectos en modo interactivo.
Para crear un proyecto en modo interactivo usamos el comando
{% highlight console %}

$ mvn archetype:generate

{% endhighlight %}

Este comando nos listará los arquetipos disponibles y nos mostrará en consola el prompt solicitando ingresar el número de arquetipo o algun filtro, por ejemplo *"quickstart"* para que nos muestre todos los arquetipos que contengan este filtro.


Por defecto, Maven nos seleccionará el arquetipo que contiene un ejemplo de proyecto Maven, en este caso es el 1255.  *Enter* y seleccionamos la opción por defecto.

{% highlight console %}
 Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 1255:
{% endhighlight %}

Posteriormente debemos especificar **groupId**, **artifactId**, **version** y **package**.

Maven nos generará un proyecto con la siguiente estructura.

{% highlight console %}
$ tree
.
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── ar
    │           └── com
    │               └── jekipes
    │                   └── App.java
    └── test
        └── java
            └── ar
                └── com
                    └── jekipes
                        └── AppTest.java

11 directories, 3 files
{% endhighlight %}

Y el correspondiente descriptor del proyecto **pom.xml**
{% highlight xml %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>ar.com.jekipes</groupId>
  <artifactId>MavenQuickstart</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>MavenQuickstart</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
{% endhighlight %}

<div id='id-section2'/>
## 2 - Crear proyectos en modo no-interactivo.
La otra forma de generar proyectos es el modo no-interactivo o batch, especificando los parámetros en el mismo comando

{% highlight console %}
$ mvn archetype:generate -DgroupId=ar.com.jekipes -DartifactId=MavenQuickstart -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
{% endhighlight %}

Los parámetros para este comando **archetype:generate** son:
- **groupId**: es el identificador de paquetes de tu proyecto dentro de todos los proyectos. Sigue las reglas del nombrado de paquetes en Java.
- **artefactId**: define el nombre de proyecto.
- **archetypeArtifactId**: define el nombre del arquetipo.
- **interactiveMode**: **false** para usar el modo no-interactivo.

De esta forma obtendremos un proyecto similar al que generamos arriba.

<div id='id-section3'/>
## 3 - Crear arquetipos desde un proyecto.
Ahora ya sabemos como crear proyectos Java con Maven a partir de los arquetipos que nos provve Maven. Pero también podemos crear nuestros propios arquetipos que generen proyectos con la estructura y dependencias que necesitemos.
Vamos a ver un ejemplo sencillo, de crear un arquetipo desde un proyecto existente.
El ejemplo anterior lo modificamos para poder crear un arquetipos que genere proyectos cosn la librería JUnit 4 y esté configurado para trabajar con logback.

Para ello modificamos el **pom.xml** y agregamos las dependencias
{% highlight xml %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>ar.com.jekipes</groupId>
  <artifactId>MavenQuickstart</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>MavenQuickstart</name>
  <url>http://maven.apache.org</url>
  <dependencies>
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
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
{% endhighlight %}

Para agregar el archivo de configuración de logs de Logback, creamos un nuevo directorio resources.
{% highlight console %}
$ mkdir -p src/test/resources
$ touch src/test/resources/logback-test.xml
{% endhighlight %}

Un simple archivo de configuración para logback **logback-test.xml**.
{% highlight xml %}
<configuration>
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <target>System.out</target>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    <root level="info">
        <appender-ref ref="console"/>
    </root>
</configuration>
{% endhighlight %}

Actualizamos la clase de test **AppTest.java** para que soporte **JUnit 4**.
{% highlight java %}
package ar.com.jekipes;

import static org.junit.Assert.assertTrue;
import org.junit.Test;
import org.slf4j.LoggerFactory;
import org.slf4j.Logger;

public class AppTest {
  private static final Logger logger = LoggerFactory.getLogger(AppTest.class.getSimpleName());
  @Test
  public void evalTest() {
	logger.info("hello test!!!");
        assertTrue( true );
  }
}
{% endhighlight %}
Ahora generamos el arquetipo con el comando
{% highlight console %}
$ mvn archetype:create-from-project
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building MavenQuickstart 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] >>> maven-archetype-plugin:3.0.1:create-from-project (default-cli) > generate-sources @ MavenQuickstart >>>
[INFO]
[INFO] <<< maven-archetype-plugin:3.0.1:create-from-project (default-cli) < generate-sources @ MavenQuickstart <<<
[INFO]
[INFO] --- maven-archetype-plugin:3.0.1:create-from-project (default-cli) @ MavenQuickstart ---
[INFO] Setting default groupId: ar.com.jekipes
[INFO] Setting default artifactId: MavenQuickstart
[INFO] Setting default version: 1.0-SNAPSHOT
[INFO] Setting default package: ar.com.jekipes
[INFO] Scanning for projects...
[INFO] ...
[INFO] ------------------------------------------------------------------------
[INFO] Archetype project created in /home/juan/tuto/MavenQuickstart/target/generated-sources/archetype
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
{% endhighlight %}
<div class="bs-callout bs-callout-warning">
Nos interesa recordar <b>groupId</b>, <b>artifactId</b> y <b>version</b> de nuestro nuevo arquetipo, ya que luego estos parámetros son requeridos para la generación de nuevos proyectos.
</div>

Instalamos el arquetipo generado en nuestro repositorio local.
{% highlight console %}
$ cd target/generated-sources/archetype/
$ mvn install
{% endhighlight %}

Ahora ya podemos crear nuevos proyectos desde nuestro arquetipo.
{% highlight console %}
mvn archetype:generate -DarchetypeGroupId=ar.com.jekipes -DarchetypeArtifactId=MavenQuickstart-archetype -DarchetypeVersion=1.0-SNAPSHOT -DgroupId=ar.com.jekipes -DartifactId=logback-quickstart -DinteractiveMode=false
{% endhighlight %}

<div class="bs-callout bs-callout-warning">
Observa los parámetros que identifican el arquetipo.
<ul class="list-unstyled">
<li>-D<b>archetypeGroupId</b>=ar.com.jekipes</li>
<li>-D<b>archetypeArtifactId</b>=MavenQuickstart-archetype</li>
<li>-D<b>archetypeVersion</b>=1.0-SNAPSHOT</li>
</ul>
</div>
La estructura deseada de nuestro proyecto.
{% highlight console %}
$ tree logback-quickstart
logback-quickstart
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── ar
    │           └── com
    │               └── jekipes
    │                   └── App.java
    └── test
        ├── java
        │   └── ar
        │       └── com
        │           └── jekipes
        │               └── AppTest.java
        └── resources
            └── logback-test.xml

12 directories, 4 files
{% endhighlight %}
Y sus dependencias.
{% highlight console %}
$ mvn dependency:tree
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building logback-quickstart 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ logback-quickstart ---
[INFO] ar.com.jekipes:logback-quickstart:jar:1.0-SNAPSHOT
[INFO] +- ch.qos.logback:logback-classic:jar:1.2.3:runtime
[INFO] |  \- ch.qos.logback:logback-core:jar:1.2.3:runtime
[INFO] +- org.slf4j:slf4j-api:jar:1.7.25:compile
[INFO] \- junit:junit:jar:4.12:test
[INFO]    \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
{% endhighlight %}

<div id='id-section4'/>
## 4 - Compilar y construir proyectos.
Maven se basa en el concepto de ciclo de vida de construcción de un proyecto. De manera que el proceso para construir y distribuir un proyecto esta claramente definido.

Hay tres ciclos de vida para la construcción de proyectos proyectos predefinidos, el ciclo de limpieza, el ciclo por defecto o de construcción y el ciclo de generación de la documentación.

Cada uno de estos ciclos está constituído por fases, donde cada fase representa un estado en el ciclo de vida de construcción del proyecto.

![Ciclo de Maven](/img/ciclo-maven.svg)

Por ejemplo, el ciclo de vida **Default** comprende las siguientes fases

validate - validate the project is correct and all necessary information is available
compile - compile the source code of the project
test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
package - take the compiled code and package it in its distributable format, such as a JAR.
verify - run any checks on results of integration tests to ensure quality criteria are met
install - install the package into the local repository, for use as a dependency in other projects locally
deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.
Estas fases (además de otras que no se muestran en este listado) son ejecutadas secuencialmente para completar el ciclo de vida **Default**.

Para empaquetar el proyecto e instalarlo en el repositorio local se puede usar
{% highlight console %}
$ mvn install
{% endhighlight %}
Este comando ejecuta cada una de las fases del ciclo de vida **Default** en orden (validate, compile, package, etc.) antes de ejecutar la fase install.

Puedes combinar fases de los ciclos de vida **Clean** y **Default**, para limpiar el proyecto eliminando lo generado  anteriormente en **/target** y compilar y crear un jar del proyecto con
{% highlight console %}
$ mvn clean package
{% endhighlight %}


<div id='id-section5'/>
## 5 - Ejecutar programas con Maven.
Puedes el proyecto compilado y empaquetado anteriormente con
{% highlight console %}
$ java -cp target/logback-quickstart-1.0-SNAPSHOT.jar ar.com.jekipes.App
Hello World!
{% endhighlight %}

<div id='id-section6'/>
## 6 - Ejecutar pruebas con Maven.
{% highlight console %}
En la fase de test puedes invocar una prueba en particular con
$ mvn -Dtest=ar.com.jekipes.AppTest test
...
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running ar.com.jekipes.AppTest
22:15:57.555 [main] INFO  AppTest - hello test!!!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.521 sec - in ar.com.jekipes.AppTest
{% endhighlight %}

<div id='id-conclusiones'/>
## Conclusiones.
En esta guía hemos visto las principales características de Apache Maven y como usarlas desde la linea de comandos para generar arquetipos y proyectos.
El código fuente utilizado para este ejemplo lo puedes encontrar en [Repositorio GitHub](https://github.com/juanek)

<div id='id-referencias'/>
## Referencias.
[https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)


[https://maven.apache.org/guides/mini/guide-naming-conventions.html](https://maven.apache.org/guides/mini/guide-naming-conventions.html)


[https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)


[http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
