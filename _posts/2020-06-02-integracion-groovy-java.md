---
  layout: post
  title: "Proyecto maven que integra java y groovy."
  date:   2020-06-02 10:51:00 -0300
  tags: java groovy
  summary: "Vamos a configurar un proyecto maven para que compile y ejecutar código fuente en java y groovy"
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >Introducción.</a></li>
  <li><a href="#id-section2" >Plugin GMavenPlus.</a></li>
  <li><a href="#id-section3" >Estructura del proyecto.</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

<div id='id-section1'/>
## Introducción.

Vamos a configurar un proyecto maven para que compile y ejecute código fuente en java y groovy. Usaremos el plugin de
 maven GMavenPlus que es el sucesor del ya obsoleto GMaven plugin.


<div id='id-section2'/>
## Plugin GMavenPlus.

GMavenPlus plugin requiere al menos jdk 1.7 y groovy 1.8.2 o superior.  
Para compilar proyectos donde interactúan clases java y groovy con dependencias mutuas será necesario generar *stubs* 
durante la fase de compilación.  
Para esto debemos agregar al *pom.xml* lo siguiente
- el plugin **gmavenplus**
- configurar los objetivos de ejecución del plugin
- el plugin de compilación de maven
- la dependencia a la librería de groovy

{% highlight xml %}

<project>
  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.gmavenplus</groupId>
        <artifactId>gmavenplus-plugin</artifactId>
        <version>1.9.0</version>
        <executions>
          <execution>
            <goals>
              <goal>addSources</goal>
              <goal>addTestSources</goal>
              <goal>generateStubs</goal>
              <goal>compile</goal>
              <goal>generateTestStubs</goal>
              <goal>compileTests</goal>
              <goal>removeStubs</goal>
              <goal>removeTestStubs</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>org.codehaus.groovy</groupId>
      <artifactId>groovy-all</artifactId>
      <version>3.0.3</version>
      <type>pom</type>
    </dependency>
  </dependencies>
</project>

{% endhighlight %}

<div id='id-section3'/>
## Estructura del proyecto.

Por defecto maven buscará las clases groovy en **src/main/groovy** y los test groovy en **src/test/groovy**

{% highlight bash %}
.
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── groovy
    │   │   └── ar
    │   │       └── com
    │   │           └── jekipes
    │   │               └── app
    │   │                   └── Util.groovy
    │   └── java
    │       └── ar
    │           └── com
    │               └── jekipes
    │                   └── app
    │                       └── Main.java
    └── test
        ├── groovy
        │   └── ar
        │       └── com
        │           └── jekipes
        │               └── app
        │                   └── UtilTest.groovy
        └── java
            └── ar
                └── com
                    └── jekipes
                        └── app
                            └── MainTest.java

{% endhighlight %}

Para poder ejecutar las clases java y groovy usamos el plugin, **exec-maven-plugin.**
Por defecto el **maven-surefire-plugin** está desactualizado, es buena práctica configurarlo explícitamente en el 
*pom.xml* para ejecutar los test sin problemas.
La versión final del *pom.xml*

{% highlight xml %}

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>ar.com.jekipes</groupId>
    <artifactId>simple-groovy</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <mainClass>ar.com.jekipes.app.Main</mainClass>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-all</artifactId>
            <version>3.0.3</version>
            <type>pom</type>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.6.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>java</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <mainClass>${mainClass}</mainClass>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
            </plugin>
            <plugin>
                <groupId>org.codehaus.gmavenplus</groupId>
                <artifactId>gmavenplus-plugin</artifactId>
                <version>1.9.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>addSources</goal>
                            <goal>addTestSources</goal>
                            <goal>generateStubs</goal>
                            <goal>compile</goal>
                            <goal>generateTestStubs</goal>
                            <goal>compileTests</goal>
                            <goal>removeStubs</goal>
                            <goal>removeTestStubs</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!-- bug for junit 4 with sufire default-->
            <!-- surefire-junit47 forced provide for [TestNG] [ERROR] No test suite found. Nothing to run -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M4</version>
                <dependencies>
                    <dependency>
                        <groupId>org.apache.maven.surefire</groupId>
                        <artifactId>surefire-junit47</artifactId>
                        <version>3.0.0-M4</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
   
</project>

{% endhighlight %}

Para compilar los fuentes java y groovy tanto de main y de test ejecutamos la fase de compilación

{% highlight Shell Session %}
$ mvn compile
{% endhighlight %}

La clase **Main.java** nos permite ejecutar código java que invoca el código groovy de la clase **Util.groovy**.

{% highlight java %}
package ar.com.jekipes.app;

/**
 *  mvn -q exec:java
 *  mvn -q exec:java -DmainClass="ar.com.jekipes.app.Main"
 *
 * @author juan kipes
 */
public class Main {
    
    public static void main(String[] args) {
        System.out.println("from java ...");
        System.out.println("Hello "+Util.message("groovy"));
        Util util = new Util();
        System.out.println("Hello "+util.message2("groovy 2"));
    }
}
{% endhighlight %}

En consola
{% highlight Shell Session %}
$ mvn -q exec:java
from java ...
message from static  groovy
Hello >> groovy <<
message from groovy
Hello >> groovy 2 <<

{% endhighlight %}



<div id='id-conclusiones'/>
## Conclusiones.
Configuramos un proyecto maven que nos permite compilar tanto fuentes groovy como java.
Con el plugin exec-maven-plugin ejecutamos las clases de **src/main**

- ***mvn -q exec:java -DmainClass="ar.com.jekipes.app.Main"***
- ***mvn -q exec:java -DmainClass="ar.com.jekipes.app.Util"***

Y las clases de test 

- ***mvn -Dtest=ar.com.jekipes.app.MainTest test***
- ***mvn -Dtest=ar.com.jekipes.app.UtilTest test***

El código fuente del proyecto en ***https://github.com/juanek/simple-groovy.git***

<div id='id-referencias'/>
## Referencias.

[Plugin GMavenPlus](http://groovy.github.io/GMavenPlus/project-info.html)
