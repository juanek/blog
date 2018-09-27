---
  layout: post
  title: "Usar systemctl para administrar servicios en Linux"
  date:   2018-09-24 17:03:00 -0300
  tags: linux
  description: "Systemctl es usado por la principales distribuciones linux para administrar servicios."
---
Los servicios son programas que corren continuamente en el sistema, esperando por eventos externos para procesar alguna cosa y que hagan a esto todo el tiempo. Hay muchos servicios ejecutándose todo el tiempo en un servidor Linux. Como servidor web, base de datos, FTP,SSH, impresión,DHCP, servidor LDAP.

Systemctl es un programa que se usa para administrar las tareas de los servicios. Con el comando systemctl podemos iniciar y parar un servicio, habilitarlo como demonio, controlar el estado actual del mismo. Además podemos configurar algunas opciones de arranque y permisos de ejecución.

Vamos a mostrar como administrar y trabajar con servicios, instalando un servidor de aplicaciones Resin y hacer las configuraciones necesarias para administrarlo con systemctl.

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

[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-8-oracle
Environment=RESIN_HOME=/opt/resin-test
WorkingDirectory=/opt/resin-test
SyslogIdentifier=Resin Test
ExecStart=/bin/sh /opt/resin-test/bin/resin.sh start
ExecStop=/bin/sh /opt/resin-test/bin/resin.sh stop
User=juan

[Install]
WantedBy=multi-user.target
{% endhighlight %}

explicar que hace este archivo

Unit files typically consist of three sections:
[Unit] — contains generic options that are not dependent on the type of the unit. These options provide unit description, specify the unit's behavior, and set dependencies to other units. For a list of most frequently used [Unit] options, see

Description	A meaningful description of the unit. This text is displayed for example in the output of the systemctl status command.
After[b]	Defines the order in which units are started. The unit starts only after the units specified in After are active. Unlike Requires, After does not explicitly activate the specified units. The Before option has the opposite functionality to After.

Type	Configures the unit process startup type that affects the functionality of ExecStart and related options. One of:
Forking – The process started with ExecStart spawns a child process that becomes the main process of the service. The parent process exits when the startup is complete.

ExecStart	Specifies commands or scripts to be executed when the unit is started. ExecStartPre and ExecStartPost specify custom commands to be executed before and after ExecStart. Type=oneshot enables specifying multiple custom commands that are then executed sequentially.
ExecStop	Specifies commands or scripts to be executed when the unit is stopped.

Important [Install] Section Options
WantedBy	A list of units that weakly depend on the unit. When this unit is enabled, the units listed in WantedBy gain a Want dependency on the unit.


Para una descrición detallada del sistema de administración systemd, ver [CREATING AND MODIFYING SYSTEMD UNIT FILES](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-managing_services_with_systemd-unit_files)

# Habilitar y administrar nuestro servicio.

Notify systemd that a new name.service file exists by executing the following command as root:
systemctl daemon-reload
systemctl start name.service

Always run the systemctl daemon-reload command after creating new unit files or modifying existing unit files

The name.service unit can now be managed as any other system service with commands

A code section without any header

Service Management
The fundamental purpose of an init system is to initialize the components that must be started after the Linux kernel is booted (traditionally known as "userland" components). The init system is also used to manage services and daemons for the server at any point while the system is running. With that in mind, we will start with some simple service management operations.

In systemd, the target of most actions are "units", which are resources that systemd knows how to manage. Units are categorized by the type of resource they represent and they are defined with files known as unit files. The type of each unit can be inferred from the suffix on the end of the file.

For service management tasks, the target unit will be service units, which have unit files with a suffix of .service. However, for most service management commands, you can actually leave off the .service suffix, as systemd is smart enough to know that you probably want to operate on a service when using service management commands.

Starting and Stopping Services

To start a systemd service, executing instructions in the service's unit file, use the start command. If you are running as a non-root user, you will have to use sudo since this will affect the state of the operating system:

sudo systemctl start application.service
As we mentioned above, systemd knows to look for *.service files for service management commands, so the command could just as easily be typed like this:

sudo systemctl start application
Although you may use the above format for general administration, for clarity, we will use the .service suffix for the remainder of the commands to be explicit about the target we are operating on.

To stop a currently running service, you can use the stop command instead:

sudo systemctl stop application.service
Restarting and Reloading

To restart a running service, you can use the restart command:

sudo systemctl restart application.service
If the application in question is able to reload its configuration files (without restarting), you can issue the reload command to initiate that process:

sudo systemctl reload application.service
If you are unsure whether the service has the functionality to reload its configuration, you can issue the reload-or-restart command. This will reload the configuration in-place if available. Otherwise, it will restart the service so the new configuration is picked up:

sudo systemctl reload-or-restart application.service

Enabling and Disabling Services

The above commands are useful for starting or stopping commands during the current session. To tell systemd to start services automatically at boot, you must enable them.
To start a service at boot, use the enable command:

sudo systemctl enable application.service
This will create a symbolic link from the system's copy of the service file (usually in /lib/systemd/system or /etc/systemd/system) into the location on disk where systemd looks for autostart files (usually /etc/systemd/system/some_target.target.wants. We will go over what a target is later in this guide).

To disable the service from starting automatically, you can type:

sudo systemctl disable application.service
This will remove the symbolic link that indicated that the service should be started automatically.

Keep in mind that enabling a service does not start it in the current session. If you wish to start the service and enable it at boot, you will have to issue both the start and enable commands.

Checking the Status of Services

To check the status of a service on your system, you can use the status command:

systemctl status application.service
This will provide you with the service state, the cgroup hierarchy, and the first few log lines.

recargar el si cambia el archivo .service

{% highlight Bash %}
A small HTML snippet
{% endhighlight %}
