---
  layout: post
  title: "Medir el tiempo estimado de ejecución en Java."
  date:   2019-01-31 17:46:00 -0300
  tags: java
  summary: "4 formas de medir el tiempo estimado de ejecución en Java"
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >Introducción</a></li>
  <li><a href="#id-section2" >Medir con currentTimeMillis()</a></li>
  <li><a href="#id-section3" >Medir con nanoTime()</a></li>
  <li><a href="#id-section4" >Medir con la clase Instant</a></li>
  <li><a href="#id-section5" >Medir con StopWatch de Apache Commons Lang</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>


<div id='id-section1'/>
## Introducción.

Necesitas determinar el tiempo estimado de ejecución de un bloque de código.


<div id='id-section2'/>
## Medir con currentTimeMillis().

Cuando nos encontramos con el requerimiento de medir el tiempo estimado de ejecución de entre dos puntos de código, podemos intentar algo así:

{% highlight Java %}

long startTime = System.currentTimeMillis();
// lineas de código o invocación al método a medir
long endTime = System.currentTimeMillis();
long duration = (endTime - startTime);
System.out.format("En milisegundos = %s, ( Inicio : %s, Fin : %s ) \n", duration, startTime, endTime);
System.out.println("Formato legible (d HH:mm:ss.s) : " + millisToShortDHMS(duration));

{% endhighlight %}
La salida
{% highlight bash %}

Milisegundos = 5000, ( Inicio : 1590881583849, Fin : 1590881588849 ) 
Formato legible (d HH:mm:ss.s) : 00:00:05.0000

{% endhighlight %}

System.currentTimeMillis() devuelve la diferencia medida en milisegundos entre el tiempo actual y la medianoche del 1 de Enero de 1970 en UTC.

Si analizamos el código, obtenemos una marca de tiempo al comienzo y otra al final de lo que queremos medir. El tiempo estimado es la diferencia entre ambos valores.

System.currentTimeMillis() tiene precisión de milisegundos sin embargo, el resultado puede ser inexacto debido a que System.currentTimeMillis() es sensible a cambios en el relog del sistema.
Por esto dos llamadas subsecuentes pueden retornar el mismo valor ya que dependiendo de la arquitectura del sistema operativo la unidad de medida del relog del sistema puede ser de decenas de milisegundos.

System.currentTimeMillis() es thread-safe.
Se dice que una clase es thread-safe cuando los objetos de dicha clase se pueden utilizar desde varios threads sin violar el principio de seguridad.

<div id='id-section3'/>
## Medir con nanoTime().

Devuelve el valor más preciso del sistema de temporizador del sistema, en nanosegundos.
A diferencia del System.currentTimeMillis() que se basa en el relog de pared, System.nanoTime() se basa en un temporizador del sistema que es independiente del relog de pared

Una particularidad de nanoTime() es que aunque provee precisión en nanosegundos, no se garantiza resolución en nanosegundos.Si al menos la misma resolución que System.currentTimeMillis().

Para medir el tiempo de ejecución de un bloque de código

{% highlight Java %}

long startTime = System.nanoTime();
// lineas de código o invocación al método a medir
long endTime = System.nanoTime();
long duration = (endTime - startTime);
System.out.format("En nanosegundos = %s, ( Inicio : %s, Fin : %s ) \n", duration, startTime, endTime);

{% endhighlight %}
La salida
{% highlight bash %}

Nanosegundos = 5000111279, ( Inicio : 43207939175999, Fin : 43212939287278 ) 
Formato legible (d HH:mm:ss.s) : 00:00:05.0000

{% endhighlight %}

<div id='id-section4'/>
## Medir con la clase Instant.

Esta clase modela un punto en la línea de tiempo, un momento preciso en el tiempo.De manera que puede ser usada para obtener marcas de tiempos.

{% highlight Java %}

public static Instant now()

{% endhighlight %}

Sin parámetros captura el momento actual desde el sistema.

{% highlight java %}

 Instant i = Instant.now();
 System.out.println(i);
 
{% endhighlight %}
La salida
{% highlight bash %}

2020-05-30T23:40:11.861Z

{% endhighlight %}
El método con parámetro
{% highlight Java %}

public static Instant now(Clock clock)

{% endhighlight %}
La salida
{% highlight bash %}

2020-05-30T23:40:12.056Z

{% endhighlight %}

Alternativamente soporta como parámetro la clase Clock, que permite proveer acceso a un momento preciso en el tiempo para algún time-zone específico.

{% highlight Java %}

Instant i = Instant.now(Clock.system(ZoneId.of("America/Argentina/Buenos_Aires")));
System.out.println(i);

{% endhighlight %}

Para medir el tiempo de ejecución de un bloque de código

{% highlight java %}

Instant startTime = Instant.now();
Thread.sleep(3500);
Instant endTime = Instant.now();
System.out.println(Duration.between(startTime, endTime));

{% endhighlight %}
El objeto *Duration* mostrará el tiempo en segundos
{% highlight bash %}

PT3.5S

{% endhighlight %}

<div id='id-section5'/>
## Medir con StopWatch de Apache Commons Lang

La clase StopWatch ofrece un API conveniente para cronometrar tiempos.

- ***start()*** o ***createStarted()*** para iniciar el cronómetro
- ***suspend()*** pause el conteo del cronómetro
- ***resume()***  despausar el conteo del cronómetro
- ***reset()***   vuelve el conteo del cronómetro
- ***getTime()*** devuelve una marca de tiempo en milisegundos
- ***split()*** cuando el cronometro entra en modo "split", cada vez que se llama a StopWatch.getTime() este retorna el tiempo de la última vuelta
- ***unsplit()*** el cronómetro sale del modo "split" de manera que StopWatch.getTime() retornará el tiempo estimado completo desde que se inició el cronometro. 

Esta clase no es thread-safe.

{% highlight java %}

StopWatch clock = new StopWatch( );

clock.start( );
Thread.sleep(3500);
clock.stop( );
System.out.println( "Miliseconds= " + clock.getTime( ));

clock.reset( );

clock.start( );
Thread.sleep(2500);
clock.stop( );
System.out.println( "Miliseconds= " + clock.getTime( ));

clock.reset( );

clock.start( );
clock.split( );
Thread.sleep(1000);
System.out.println( "Primera vuelta: " + clock.getTime( ) );
Thread.sleep(1000);
System.out.println( "Segunda vuelta: " + clock.getTime( ) );
clock.unsplit( );
Thread.sleep(800);
System.out.println( "Tiempo total: " + clock.getTime( ) );

{% endhighlight %}
La salida en consola es
    
    Miliseconds= 3500
    Miliseconds= 2500
    Primera vuelta: 1000
    Segunda vuelta: 2000
    Tiempo total: 2801


<div id='id-conclusiones'/>
## Conclusiones.

El real beneficio de usar una librería de como Apache Commons Lang para medir el tiempo estimado es la legibilidad y mantenibilidad que le brinda al código.

StopWatch es un API clara y concisa con métodos que tienen un significado directo en analogía con el cronómetro de mano. 

<div id='id-referencias'/>
## Referencias.

[https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html](https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html)

[https://docs.oracle.com/javase/8/docs/api/java/time/Clock.html#systemUTC--](https://docs.oracle.com/javase/8/docs/api/java/time/Clock.html#systemUTC--)

[https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/time/StopWatch.html](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/time/StopWatch.html)
