---
  layout: post
  title: "Medir el tiempo estimado de ejecución en Java."
  date:   2019-01-31 17:46:00 -0300
  tags: java
  summary: "5 formas de medir el tiempo estimado de ejecución en Java"
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >Introducción</a></li>
  <li><a href="#id-section2" >Medir con currentTimeMillis()</a></li>
  <li><a href="#id-section3" >Medir con nanoTime()</a></li>
  <li><a href="#id-section3" >Medir con la clase Instant</a></li>
  <li><a href="#id-section3" >Medir con Joda-Time</a></li>
  <li><a href="#id-section3" >Medir con StopWatch de Apache Commons Lang</a></li>
  <li><a href="#id-section3" >Medir con StopWatch de Guava</a></li>
  <li><a href="#id-section3" >Medir con StopWatch de Spring Framework</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

https://www.baeldung.com/java-measure-elapsed-time

https://dzone.com/articles/guava-stopwatch

https://stackoverflow.com/questions/180158/how-do-i-time-a-methods-execution-in-java

https://docs.spring.io/spring/docs/2.0.x/javadoc-api/org/springframework/util/StopWatch.html

https://www.geeksforgeeks.org/java-system-nanotime-vs-system-currenttimemillis/

https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#currentTimeMillis--

https://docs.oracle.com/javase/8/docs/api/java/util/Date.html


<div id='id-section1'/>
## Introducción.


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


Returns the current time in milliseconds. Note that while the unit of time of the return value is a millisecond, the granularity of the value depends on the underlying operating system and may be larger. For example, many operating systems measure time in units of tens of milliseconds.
See the description of the class Date for a discussion of slight discrepancies that may arise between "computer time" and coordinated universal time (UTC).

Returns:
the difference, measured in milliseconds, between the current time and midnight, January 1, 1970 UTC.



System.currentTimeMillis() devuelve el número de milisegundos desde el 1 de Enero de 1970.

Si analizamos el código, obtenemos una marca de tiempo al comienzo y otra al final de lo que queremos medir. El tiempo estimado es la diferencia entre ambos valores.

Sin embargo, el resultado puede ser inexacto debido a que System.currentTimeMillis() es sensible a cambios en el relog del sistema.

System.currentTimeMillis() es thread-safe.

<div id='id-section3'/>
## Medir con nanoTime().


public static long nanoTime()
Returns the current value of the running Java Virtual Machine's high-resolution time source, in nanoseconds.
This method can only be used to measure elapsed time and is not related to any other notion of system or wall-clock time. The value returned represents nanoseconds since some fixed but arbitrary origin time (perhaps in the future, so values may be negative). The same origin is used by all invocations of this method in an instance of a Java virtual machine; other virtual machine instances are likely to use a different origin.

This method provides nanosecond precision, but not necessarily nanosecond resolution (that is, how frequently the value changes) - no guarantees are made except that the resolution is at least as good as that of currentTimeMillis().

Differences in successive calls that span greater than approximately 292 years (263 nanoseconds) will not correctly compute elapsed time due to numerical overflow.

The values returned by this method become meaningful only when the difference between two such values, obtained within the same instance of a Java virtual machine, is computed.

For example, to measure how long some code takes to execute:


 long startTime = System.nanoTime();
 // ... the code being measured ...
 long estimatedTime = System.nanoTime() - startTime;
To compare two nanoTime values


 long t0 = System.nanoTime();
 ...
 long t1 = System.nanoTime();
one should use t1 - t0 < 0, not t1 < t0, because of the possibility of numerical overflow.
Returns:
the current value of the running Java Virtual Machine's high-resolution time source, in nanoseconds


Note
that	System.nanoTime	is	used	to	time	the	activity.	For	interval	timing,
always
use
System.nanoTime
rather
than
System.currentTimeMillis.	 System.nanoTime	 is	 both	 more
accurate	 and	 more	 precise	 and	 is	 unaffected	 by	 adjustments	 to	 the	 system’s
real-time	 clock.

<div id='id-conclusiones'/>
## Conclusiones.

<div id='id-referencias'/>
## Referencias.
