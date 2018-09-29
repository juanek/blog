---
  layout: post
  title: "Usar systemctl para administrar servicios en Linux"
  date:   2018-09-24 17:03:00 -0300
  tags: linux
  description: "Systemctl es usado por la principales distribuciones linux para administrar servicios."
  summary: "Systemctl es usado por la principales distribuciones linux para administrar servicios."
---

### Contenido
- [Instalar el servidor Resin.](#id-section1)
- [Crear nuestro archivo .service.](#id-section2)
- [Administrar nuestro servicio](#id-section3)
- [Arrancar y parar servicios.](#id-section4)
- [Reiniciar servicios.](#id-section5)
- [Habilitar y deshabilitar servicios.](#id-section6)
- [Verificar el estado de un servicio.](#id-section7)
- [Ver los registros del sistema.](#id-section8)
- [Recursos útiles.](#id-section9)


Los servicios son programas que corren continuamente en el sistema, esperando por eventos externos para procesar alguna cosa y que hagan a esto todo el tiempo. Hay muchos servicios ejecutándose todo el tiempo en un servidor Linux. Como servidor web, base de datos, FTP,SSH, impresión,DHCP, servidor LDAP.

Systemctl es un programa que se usa para administrar las tareas de los servicios. Con el comando systemctl podemos iniciar y parar un servicio, habilitarlo como demonio, controlar el estado actual del mismo. Además podemos configurar algunas opciones de arranque y permisos de ejecución.

Vamos a mostrar como administrar y trabajar con servicios, instalando un servidor de aplicaciones Resin y hacer las configuraciones necesarias para administrarlo con systemctl.
<div id='id-section1'/>
# Instalar el servidor Resin.

Descargamos la última versión estable de [Resin](http://caucho.com/products/resin/download).
{% highlight console %}
wget http://caucho.com/download/resin-4.0.58.tar.gz
{% endhighlight %}
Descomprimimos **resin-4.0.58.tar.gz** en un directorio **resin-test**.

{% highlight console %}
tar xzfv resin-4.0.58.tar.gz -C resin-test --strip-components=1
{% endhighlight %}

Lo movemos a **/opt/resin-test** y ubicados en **/opt/resin-test** verificamos que funciona correctamente.
Iniciar el servidor Resin.
{% highlight console %}
sh bin/resin.sh start
{% endhighlight %}
Verificar que esta corriendo el port 8080 con el comando **curl**
{% highlight console %}
curl http://localhost:8080
{% endhighlight %}
Deberíamos ver la Home Page de Resin.
{% highlight HTML %}
<html>
  <head>
    <title>Resin&#174; Default Home Page</title></head>
    <body>
          <h1 style="background: #ccddff">Resin&#174; Default Home Page</h1>
            This is the default page for the Resin web server.
            <p>Documentation is available at <a href="/resin-doc">/resin-doc</a>.
            <p>Administration is available at <a href="/resin-admin">/resin-admin</a>.
    </body>
</html>
{% endhighlight %}
Parar el servidor Resin.
{% highlight console %}
sh bin/resin.sh stop
{% endhighlight %}

Tenemos instalado un servidor de aplicaciones web Resin, sabemos como iniciar y parar el servicio web. Vamos a crear un archivo **.service** para poder administar este servicio con el comando **systenctl**.

Para una instalación y configuración mas avanzada del servidor, ver [Resin](http://caucho.com/resin-4.0/)

<div id='id-section2'/>
# Crear nuestro archivo .service.
Vamos a crear un unproceso que se encargue de iniciar y vigilar nuestro servidor web Resin (volver a iniciarlo si se para, reiniciarlo cuando el sistema se reinicie, poder controlar su estado…). En systemd estos procesos se definen y configuran en los archivos denominados de unidad, con extensión **.service**

Creamos un archivo **resin-test.service** en el directorio **/etc/systemd/system**.
{% highlight console %}
touch resin-test.service
{% endhighlight %}
El contenido de nuestro archivo **.service** es
{% highlight console %}
[Unit]
Description=Resin Test service
After=syslog.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-8-oracle
Environment=RESIN_HOME=/opt/resin-test
SyslogIdentifier=Resin Test
ExecStart=/bin/sh /opt/resin-test/bin/resin.sh start
ExecStop=/bin/sh /opt/resin-test/bin/resin.sh stop
User=juan

[Install]
WantedBy=multi-user.target
{% endhighlight %}

explicar que hace este archivo

Los archivos **unit** consisten de tres secciones:

#### [Unit]

  **Description:** una descripción significativa del archivo de unidad. Este texto se mostrará cuando se usa el comando **systemctl status**.

  **After:** define el orden que arrancará la unidad. La unidad arrancará después que la unidad especificada en **After** esté activa. La opción **Before** tiene la funcionalidad opuesta a **After**.

#### [Service]

**Type:** especifica el tipo de arranque que afectará a la funcionalidad de **ExecStart** y sus opciones. Una de estas opciones puede ser **Forking**, en esta opción el proceso comienza con ExecStart y crea un proceso hio que se convierte en el proceso principal del servicio, el proceso padre espera hasta ser notificado que esta todo bien para dar por finalizado el proceso de inicio exitoso.

**Environment** esta directiva nos permite configurar variables de entorno para ejecutar nustro proceso.

**SyslogIdentifier** este identificador será de utilidad para identificar nuestro servicio en los registros del sistema.

**ExecStart** especifica comandos o script a ser ejecutados para arrancar la unidad. **ExecStartPre** y **ExecStartPost**
especifican los comandos a ser ejecutados antes y despues de **ExecStart**.

**ExecStop** especifica comandos o scripts a ser ejecutados para parar la unidad.


#### [Install]
Esta sección es opcional e indica que es lo que se debería ocurrir cuando la unidad se habilite.

**WantedBy**
WantedBy	cuando se habilite, esta nueva unidad pasará formar parte del grupo de unidades conocidas como **multi-user.target**, el servicio arrancará cuando el sistema alcance el nivel runLevel 2.

La tabla de target y sus run levels:

| Run Lvl  | Target Units                               | Description                     |
|:--------:|:------------------------------------------:|---------------------------------|
|0         |runlevel0.target, poweroff.target           |Shut down and power off          |
|1         |runlevel1.target, rescue.target             |Set up a rescue shell            |
|2,3,4     |runlevel[234].target,multi-user.target      |Set up a non-gfx multi-user shell|
|5         |runlevel5.target, graphical.target          |Set up a gfx multi-user shell    |
|6         |runlevel6.target, reboot.target             |Shut down and reboot the system  |
{:.mbtablestyle}
<br/>
Para una descrición detallada , ver [CREATING AND MODIFYING SYSTEMD UNIT FILES](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-managing_services_with_systemd-unit_files)

<div id='id-section3'/>
# Administrar nuestro servicio.

Ahora notificamos a systemd que existe un nuevo **.service** ejecutando el comando
{% highlight console %}
sudo systemctl daemon-reload

{% endhighlight %}

> Cada vez que creamos un nuevo **.service** o modificacmos uno existente debemos ejecutar el comando **systemctl daemon-reload**


Ahora podemos administrar nuestro **resin-test.service** como cualquier otro servicio.
<div id='id-section4'/>
# Arrancar y parar servicios.

Para iniciar un servicio, systemd ejecuta las instrucciones del archivo de unidad del servicio, usando el comando **start**. Si estas ejecutando comandos como usuario **no root**, debes usar **sudo** ya que esto afecta el estado del sistema operativo.

{% highlight console %}
sudo systemctl start resin-test.service
{% endhighlight %}

Para parar un servicio que está ejecutándose puedes usar el comando **stop**.
<div id='id-section5'/>
# Reiniciar servicios.

To restart a running service, you can use the restart command:

{% highlight console %}
sudo systemctl restart resin-test.service
{% endhighlight %}
<div id='id-section6'/>
# Habilitar y deshabilitar servicios.

Los comandos de arriba son útiles para iniciar o para el servicio dureante la sesión actual. Para notificar a **systemd** que inicie el servicio automáticamnete durante el boot, debes habilitarlo.
Para habilitar el servicio se usa el comando **enabled**.

{% highlight console %}
juan@juan:~$sudo systemctl enable resin-test.service
Created symlink from /etc/systemd/system/multi-user.target.wants/resin-test.service to /etc/systemd/system/resin-test.service.
{% endhighlight %}

Esto creará un enlace simbólico a nuestro **.service** en la ubicación donde **systemd** busca los archivos de autoarranque.

Para deshabilitar el autoarranque usa el comando **disable**.
{% highlight console %}
juan@juan:~$sudo systemctl disable resin-test.service
Removed symlink /etc/systemd/system/multi-user.target.wants/resin-test.service.
{% endhighlight %}

Puedes verificar si un servicio tiene habilitado el autoarranque con el comando **is-enabled**.
{% highlight console %}
sudo systemctl is-enabled resin-test.service
{% endhighlight %}
<div id='id-section7'/>
# Verificar el estado de un servicio.

Para verificar el estado de un servicio en tu sistema, puede usar el comando **status**

{% highlight console %}

juan@juan:~$ systemctl status resin-test.service
● resin-test.service - Resin Test service
   Loaded: loaded (/etc/systemd/system/resin-test.service; disabled; vendor preset: enabled)
   Active: active (running) since sáb 2018-09-19 17:25:50 -03; 45min ago
 Main PID: 7811 (java)
   CGroup: /system.slice/resin-test.service
           ├─7811 /usr/lib/jvm/java-8-oracle/bin/java -Dresin.watchdog=app-0 -Djava.util.logging.manager=com.caucho.log
           └─7867 /usr/lib/jvm/java-8-oracle/bin/java -Dresin.server=app-0 -Djava.util.logging.manager=com.caucho.log.L

sep 19 17:25:46 juan systemd[1]: Starting Resin Test service...
sep 19 17:25:47 juan Resin Test[7767]: Resin/4.0.58 launching watchdog at 127.0.0.1:6600
sep 19 17:25:49 juan Resin Test[7767]: Resin/4.0.58 started -server 'app-0' with watchdog at 127.0.0.1:6600
sep 19 17:25:50 juan systemd[1]: Started Resin Test service.

{% endhighlight %}

<div id='id-section8'/>
# Ver los registros del sistema.
Finalmente podemos usar **Journalctl** que es la herramienta más utilizada para acceder a los registros del sistema y filtrar los registros pertenecientes a nuestro servicio
{% highlight console %}
journalctl -u resin-test.service
{% endhighlight %}
<div id='id-section9'/>
# Recursos útiles.

[http://www.caucho.com/](http://www.caucho.com/)

[https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)

[https://wiki.archlinux.org/index.php/Systemd](https://wiki.archlinux.org/index.php/Systemd)
