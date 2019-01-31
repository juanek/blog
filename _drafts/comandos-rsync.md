---
  layout: post
  title: "10 comandos rsync más utilizados en Linux."
  date:   2018-11-26 11:03:00 -0300
  tags: linux
  summary: "Un listado de los 10 comandos rsync que más utilizo."
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >¿Que es Rsync?</a></li>
  <li><a href="#id-section2" >Como funciona</a></li>
  <li><a href="#id-section3" >1- Bla bla</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

<div id='id-section1'/>
## ¿Que es Rsync?.
Rsync es un programa que puede ser usado para sincronizar archivos y directorios en ubicaciones tanto locales como remotas. Trabaja a traves de SSH, permite hacer backups incrementales, ejecutar comandos en una máquina remota y reemplaza la necesidad de usar los comandos cp y scp.

<div id='id-section2'/>
## Como funciona.

La sintaxis básica de rsync es muy sencilla, y opera en una forma que es similar a ssh, scp y cp.

rsync opciones fuente destino

Las principales opciones

-v : verbose o modo debug
-r : copia datos recursivamente (pero no preserva timestamps y permisos mientras se transfieren datos)
-a : modo archive, permite copiar archivos recursivamente y esto también preserva enláces simbólicas, permisos, usuarios, grupos y timestamps.
-z : comprimir archivos
-h : salida en formato compreensible
-d : tranfiere directorios sin recursión
-e : especifica ssh como shell remoto.


<div id='id-conclusiones'/>
## Conclusiones.

<div id='id-referencias'/>
## Referencias.
