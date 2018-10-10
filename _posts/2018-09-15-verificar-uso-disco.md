---
  layout: post
  title: "Como verificar el uso del disco en linux"
  date:   2018-09-15 17:03:00 +0800
  tags: linux
  summary: "El comando du muestra información sobre el uso del disco de archivos y directorios en Linux."
---


Para ontener información del uso del disco de archivos y directorios de sistemas UNIX/LINUX podemos usar el comando **du (Disk Usage)** .

Estos son algunos de los comandos **du** con combinación de opciones que nos ayudarán a encontrar esta información.

- Obtener el tamaño de todos los archivos y sub directorios recursivamente, en formato legible del directorio __/home/juan/Documentos/cursoPython/__ con un indicador del tamaño de total de uso al final.

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

- Cuando el árbol de directorios sobre el que necesitamos obtener irnformación es grande, podemos limitar su profundidad  con la opción __--max-depth=__

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

- Si sólo queremos obtener información de los directorios podemos usar.

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

- Podemos ver más opciones de este comando con **du --help**
{% highlight console %}
juan@juan:~$ du --help
Modo de empleo: du [OPCIÓN]... [FICHERO]...
       o bien:  du [OPCIÓN]... --files0-from=F
Summarize disk usage of the set of FILEs, recursively for directories.

Los argumentos obligatorios para las opciones largas son también obligatorios
para las opciones cortas.
  -0, --null            termina cada línea con NUL, no con nueva línea
  -a, --all             muestra resultados para todos los ficheros, no sólo
                        para los directorios
      --apparent-size   muestra los tamaños aparentes, en lugar del uso de
                          disco; el tamaño aparente es normalmente más pequeño,
                          puede ser más grande debido a agujeros en ficheros
                          dispersos, fragmentación interna, bloques indirectos,
                          etc.
  -B, --block-size=TAM  escala los tamaños por TAM antes de mostrarlos;
                          p. ej., '-BM' muestra los tamaños en unidades de
                          1.048.576 bytes; vea el formato de TAMAÑO más abajo
  -b, --bytes           equivalente a '--apparent-size --block-size=1'
  -c, --total           produce un "total"
  -D, --dereference-args  sigue solamente los enlaces listados en la línea de
                          órdenes
  -d, --max-depth=N     muestra el total para un directorio (o para un fichero,
                          con --all) solamente si está N o menos niveles por
                          debajo del argumento de la línea de órdenes;
                          --max-depth=0 es lo mismo que --summarize
      --files0-from=F   resume el uso de disco de los nombres de ficheros
                          terminados en NUL especificados en el fichero F;
                          si F es - entonces lee los nombres de la entrada
                          estándar
  -H                    equivalente a --dereference-args (-D)
  -h, --human-readable  muestra los tamaños de forma legible
                        (p. ej., 1K 234M 2G)
      --inodes          muestra el uso de nodos-i en lugar del uso de bloques
  -k                    como --block-size=1K
  -L, --dereference     sigue todos los enlaces simbólicos
  -l, --count-links     cuenta los tamaños varias veces si hay enlaces fuertes
  -m                    como --block-size=1M
  -P, --no-dereference  no sigue ningún enlace simbólico (predeterminado)
  -S, --separate-dirs   para directorios no incluye el tamaño de los subdirectorios
      --si              como -h, pero utiliza potencias de 1000, no de 1024
  -s, --summarize       muestra solamente un total para cada argumento
  -t, --threshold=TAM    excluye entradas menores que TAM si es positivo,
                           o entradas mayores que TAM si es negativo
      --time             muestra la fecha/hora de la última modificación de
                           cualquier fichero dentro del directorio, o de
                           cualquiera de sus subdirectorios
      --time=PALABRA     muestra la fecha/hora como PALABRA en lugar de la
                           fecha de modificación:
                           atime, access, use, ctime o status
      --time-style=ESTILO muestra las fechas/horas usando el estilo ESTILO,
                          que puede ser: full-iso, long-iso, iso, o +FORMATO
                          FORMATO se interpreta como en 'date'
  -X, --exclude-from=FICH  excluye los ficheros que coinciden con
                             cualquier patrón en FICH.
      --exclude=PATRÓN     excluye los ficheros que coinciden con PATRÓN.
  -x, --one-file-system    se salta los directorios de otros sistemas de
                             ficheros
      --help     muestra esta ayuda y finaliza
      --version  informa de la versión y finaliza

Los valores se muestran en unidades del primer TAMAÑO disponible de
--block-size, y las variables de entorno DU_BLOCK_SIZE, BLOCK_SIZE y BLOCKSIZE.
En caso contrario, las unidades son 1024 bytes (o 512 si se ha
establecido POSIXLY_CORRECT).

El argumento TAM es un entero y una unidad opcional (ejemplo: 10M es 10*1024*1024).
Las unidades son K,M,G,T,P,E,Z,Y (potencias de 1024) o KB,MB, ... (potencias de 1000).
{% endhighlight %}
