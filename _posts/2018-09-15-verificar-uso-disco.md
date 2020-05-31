---
  layout: post
  title: "Encontrar los archivos que más espacio ocupan en disco."
  date:   2018-09-15 17:03:00 +0800
  tags: linux
  summary: "Combinando los comandos df y du podemos analizar y liberar espacio en nuestro sistema Linux."
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >1 - Analizar el espacion es disco.</a></li>
  <li><a href="#id-section2" >2 - Buscar los archivos y directorios mas grandes.</a></li>
  <li><a href="#id-section3" >3 - Eliminar archivos y directorios.</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

<div id='id-section1'/>
## 1 - Analizar el espacio en disco.

El comando "df" muestra la información del nombre del dispositivo, total de bloques, espacio de disco usado, la disponibilidad de espacio en disco y los puntos de montaje del sistema.

Este comando significa disk filesystem.

Podemos usar df -h o df -k para ver una lista del uso del disco.

El comando df provee una opción para mostar el tamaño de los archivos y directorios en un formato "Human Readable" usando "-h", imprime los resultados en un formato mas comprensible, por ejemplo, 1K,2M, 3G.

Al ejecutar este comando veras una serie de columnas como **S.ficheros**, **Tamaño**, **Usados**, **Disp Uso%** y **Montado en**.
{% highlight console %}
$ df -h
S.ficheros     Tamaño Usados  Disp Uso% Montado en
udev             3,9G      0  3,9G   0% /dev
tmpfs            794M   9,5M  784M   2% /run
/dev/sda1        451G   220G  208G  52% /
tmpfs            3,9G    72M  3,9G   2% /dev/shm
tmpfs            5,0M   4,0K  5,0M   1% /run/lock
tmpfs            3,9G      0  3,9G   0% /sys/fs/cgroup
cgmfs            100K      0  100K   0% /run/cgmanager/fs
tmpfs            794M    84K  793M   1% /run/user/1000
{% endhighlight %}

En esta caso vemos que tenemos disponible 208G y un porcentaje de uso de 52%.


<div id='id-section2'/>
## 2 - Buscar los archivos y directorios mas grandes.
Cuando ya tenemos una estadística del uso del disco, sabremos si tenemos la necesidad de liberar espacio. Para esto primero podemos identificar cuáles son los archivos y directorios que ocupan mas espacio usando el comando **du (Disk Usage)** .

Algunos de las combinaciones de comandos du que podemos usar

- ##### Obtener el tamaño de todos los archivos y sub directorios recursivamente, en formato legible del directorio `/home/juan/Documentos/cursoPython/` con un indicador del tamaño de total de uso al final.

{% highlight console %}
juan@juan:~$ du -ahc  /home/juan/Documentos/cursoPython/
0	/home/juan/Documentos/cursoPython/tutorial/ejercicio2.py
4,0K	/home/juan/Documentos/cursoPython/tutorial/ejemploExisteDirectorio.py
52K	/home/juan/Documentos/cursoPython/tutorial/dependencias.txt
60K	/home/juan/Documentos/cursoPython/tutorial
932K	/home/juan/Documentos/cursoPython/apuntes/curso-python.pdf
936K	/home/juan/Documentos/cursoPython/apuntes
4,0K	/home/juan/Documentos/cursoPython/indice.txt
4,0K	/home/juan/Documentos/cursoPython/libros
1008K	/home/juan/Documentos/cursoPython/
1008K	total
{% endhighlight %}

- ##### Cuando el árbol de directorios sobre el que necesitamos obtener irnformación es grande, podemos limitar su profundidad  con la opción __--max-depth=__

{% highlight console %}
juan@juan:~$ du -ahc --max-depth=1 /home/juan/bitbucket/java/
448K	/home/juan/bitbucket/java/CalculatorWS_Client_Application.
152K	/home/juan/bitbucket/java/java-gson
200K	/home/juan/bitbucket/java/.git
4,0K	/home/juan/bitbucket/java/.gitignore
184K	/home/juan/bitbucket/java/CalculatorWSApplication
760K	/home/juan/bitbucket/java/java-test
232K	/home/juan/bitbucket/java/CalculatorWSClient
0	/home/juan/bitbucket/java/mitexto.txt
2,0M	/home/juan/bitbucket/java/
2,0M	total
{% endhighlight %}

- ##### Si sólo queremos obtener información de los directorios podemos usar.

{% highlight console %}
juan@juan:~$ du -hc --max-depth=1 /home/juan/bitbucket/java/
448K	/home/juan/bitbucket/java/CalculatorWS_Client_Application.
152K	/home/juan/bitbucket/java/java-gson
200K	/home/juan/bitbucket/java/.git
184K	/home/juan/bitbucket/java/CalculatorWSApplication
760K	/home/juan/bitbucket/java/java-test
232K	/home/juan/bitbucket/java/CalculatorWSClient
2,0M	/home/juan/bitbucket/java/
2,0M	total
{% endhighlight %}

- ##### obtener los 5 acrhivos y/o directorios más grandes de `/home/juan/bitbucket/java/`
{% highlight console %}
$ du -ah /home/juan/bitbucket/java/ | sort -n -r | head -n 5
800K	/home/juan/bitbucket/java/java-test
512K	/home/juan/bitbucket/java/java-test/target
448K	/home/juan/bitbucket/java/CalculatorWS_Client_Application.
276K	/home/juan/bitbucket/java/java-test/src
232K	/home/juan/bitbucket/java/java-test/target/surefire-reports
{% endhighlight %}

- ##### obtener los 5 acrhivos y/o directorios más grandes de `/home/juan/bitbucket/java/` en un formato más legible

{% highlight console %}
$ cd /home/juan/bitbucket/java/
$ du -hs * | sort -rh | head -5
800K	java-test
448K	CalculatorWS_Client_Application.
232K	CalculatorWSClient
184K	CalculatorWSApplication
152K	java-gson
{% endhighlight %}

<div id='id-section3'/>
## 3 - Eliminar archivos y directorios.
Para eliminar archivos y/o directoros usamos el comando **rm**.

- ##### borrar todo el contenido de `/home/juan/temp/*`
{% highlight console %}
$ rm /home/juan/temp/*
{% endhighlight %}

- ##### borrar todo el contenido mostrando los mensajes de debug
{% highlight console %}
$ rm -v /home/juan/temp/*
{% endhighlight %}

- ##### borrar todo el contenido recursivamente sin preguntar
{% highlight console %}
$ rm -rfv /home/juan/temp/
{% endhighlight %}

-  **-r** : remover el directorio y su contenido recursivamente.
-  **-f** : opción forzar, ignora archivos inexistentes, nunca muestra el prompt
-  **-v** : opción verbose



<div id='id-conclusiones'/>
## Conclusiones.

En esta guía vimos los comandos básicos para analizar el uso del disco con el comando **df**, obtener los archivos y directorios que más espacio ocupan con el comando **du** y cómo eliminarlos con el comando **rm**.

<div id='id-referencias'/>
## Referencias.

[https://www.ibm.com/support/knowledgecenter/en/SSLTBW_2.3.0/com.ibm.zos.v2r3.bpxa500/df.htm](https://www.ibm.com/support/knowledgecenter/en/SSLTBW_2.3.0/com.ibm.zos.v2r3.bpxa500/df.htm)

[https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/)

[man df](#)

[man du](#)
