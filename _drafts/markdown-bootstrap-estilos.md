---
  layout: post
  title: "Ejemplos de estilos markdown y boostrap para los post."
  date:   2018-09-26 17:03:00 -0300
  tags: java maven
  summary: "Ejemplos de estilos markdown y boostrap para los post."
---

> este es


`Easing.SinIn` sis i

## Título
### Subtítulo
Este es un ejemplo de texto que da entrada a una lista genérica de elementos:

- Elemento 1
- Elemento 2
- Elemento 3

Este es un ejemplo de texto que da entrada a una lista numerada:

1. Elemento 1
2. Elemento 2
3. Elemento 3



```html
 <a href="#">Hello world</a>
```

{% highlight HTML %}
 <a href="#">Hello world</a>
{% endhighlight %}

{% highlight Shell Session %}
 $ sh bin/resin.sh start
{% endhighlight %}

{% highlight Shell Session %}
$ echo $EDITOR
vim
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
$ git push
Everything up-to-date
$ echo 'All
 done!'
All
done!
{% endhighlight %}

{% highlight java %}

/**
 * @author John Smith <john.smith@example.com>
*/
package l2f.gameserver.model;

public abstract class L2Char extends L2Object {
  public static final Short ERROR = 0x0001;

  public void moveTo(int x, int y, int z) {
    _ai = null;
    log("Should not be called");
    if (1 > 5) { // wtf!?
      return;
    }
  }
}
{% endhighlight %}

<div class="alert alert-primary" role="alert">
  This is a primary alert—check it out!
</div>
<div class="alert alert-secondary" role="alert">
  This is a secondary alert—check it out!
</div>
<div class="alert alert-success" role="alert">
  This is a success alert—check it out!
</div>
<div class="alert alert-danger" role="alert">
  This is a danger alert—check it out!
</div>
<div class="alert alert-warning" role="alert">
  This is a warning alert—check it out!
</div>
<div class="alert alert-info" role="alert">
  This is a info alert—check it out!
</div>
<div class="alert alert-light" role="alert">
  This is a light alert—check it out!
</div>
<div class="alert alert-dark" role="alert">
  This is a dark alert—check it out!
</div>

<div class="bs-callout bs-callout-default">
  <h4>Default Callout</h4>
  This is a default callout.
</div>

<div class="bs-callout bs-callout-primary">
  <h4>Primary Callout</h4>
  This is a primary callout.
</div>

<div class="bs-callout bs-callout-success">
  <h4>Success Callout</h4>
  This is a success callout.
</div>

<div class="bs-callout bs-callout-info">
  <h4>Info Callout</h4>
  This is an info callout.
</div>

<div class="bs-callout bs-callout-warning">
  <h4>Warning Callout</h4>
  This is a warning callout.
</div>

<div class="bs-callout bs-callout-danger">
  <h4>Danger Callout</h4>
  This is a danger callout.
</div>

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >1 - Crear proyectos en modo interactivo.</a></li>
  <li><a href="#id-section2" >2 - Crear proyectos en modo no-interactivo.</a></li>
  <li><a href="#id-section3" >3 - Crear arquetipos desde un proyecto.</a></li>
  <li><a href="#id-section4" >4- Construir y compilar proyectos..</a></li>
  <li><a href="#id-section5" >5 - Ejecutar programas.</a></li>
  <li><a href="#id-section6" >6 - Ejecutar pruebas.</a></li>
  <li><a href="#id-conclusiones" >Conclusiones</a></li>
  <li><a href="#id-referencias" >Referencias</a></li>
</ul>
</div>

<div id='id-section1'/>
## 1 - Crear proyectos en modo interactivo.

<div id='id-section2'/>
## 2 - Crear proyectos en modo no-interactivo.

<div id='id-section3'/>
## 3 - Crear arquetipos desde un proyecto.

<div id='id-section4'/>
## 4 - Construir y compilar proyectos.

<div id='id-section5'/>
## 5 - Ejecutar programas.

<div id='id-section6'/>
## 6 - Ejecutar pruebas.

<div id='id-conclusiones'/>
## Conclusiones.

<div id='id-referencias'/>
## Referencias.



<div class="container" id="cheat-sheet">
    <div class="row row-header">
      <div class="col-sm-12"><b>Cheat Sheet Git</b></div>
    </div>
    <div class="row dark">
      <div class="col-sm-6"><b>Configure Git username</b></div>
      <div class="col-sm-6">git config --global user.name "Your Name"</div>
    </div>
    <div class="row">
      <div class="col-sm-6">Configure Git email</div>
      <div class="col-sm-6">git config --global user.email "yournamey@domain.com.ar"</div>
    </div>
    <div class="row dark">
      <div class="col-sm-6">Create Repository</div>
      <div class="col-sm-6">git init example</div>
    </div>
  </div>
