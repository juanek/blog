---
  layout: post
  title: "Cómo usar los Java Enum."
  date:   2019-01-19 08:49:00 -0300
  tags: java
  summary: "Que son los java enums y como usarlos correctamente."
---

<div class="bs-callout bs-callout-success">
<h3>Contenido</h3>
<ul class="list-unstyled">
  <li><a href="#id-section1" >Introducción</a></li>
  <li><a href="#id-section2" >¿Qué son los Java enums?</a></li>
  <li><a href="#id-section3" >Los enum en lugar de constantes int</a></li>
  <li><a href="#id-section4" >Los enum como Singleton.</a></li>
  <li><a href="#id-section5" >Reemplazar los if anidados por enum.</a></li>
  <li><a href="#id-section6" >Usar interfaces para extender enum.</a></li>
  <li><a href="#id-conclusiones" >Conclusiones.</a></li>
  <li><a href="#id-referencias" >Referencias.</a></li>
</ul>
</div>

<div id='id-section1'/>
## Introducción.
Los Enums son una parte muy porderosa e importante del lenguaje Java. Aunque son muy útiles no siempre son usados correctamente.

En este post veremos como usarlos y en que casos son útiles.

<div id='id-section2'/>
## ¿Qué son los Java enums?.
Un tipo enumerado en Java es un tipo cuyos valores consisten de conjunto fijo de constantes.En Java, los enumerados son tipos de clases que tienen un conjunto predefinido de instancias. Pueden tener constructores, métodos, campos ... Son como clases, con algunas pocas diferencias específicas:


  - Extienden de java.lang.Enum
  - Tienen un conjunto fijo de instancias.
  - Pueden ser usadas en sentencias
  - No puedes ser extendidas


Los Enums son similares a las clases por lo tanto pueden ser usadas para muchas mas cosas que solo representar enumeraciones.

<div id='id-section3'/>
## Los Enum en lugar de constantes int.

Un tipo enumerado es un tipo que consiste de un conjunto fijo de constantes, como estaciones del año, los planetas del sistema solar o los palos de un mazo de naipes.

Una forma comun de representar enumerados es usar el int enum pattern

{% highlight Java %}

// El int enum pattern - tiene muchas deficiencias!
public static final int APPLE_FUJI           = 0;
public static final int APPLE_PIPPIN         = 1;
public static final int APPLE_GRANNY_SMITH   = 2;

public static final int ORANGE_NAVEL         = 0;
public static final int ORANGE_TEMPLE        = 1;
public static final int ORANGE_BLOOD         = 2;

{% endhighlight %}

Una forma más elegante elegante y segura es usar enum.

{% highlight Java %}

public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
public enum Orange { NAVEL, TEMPLE, BLOOD }

{% endhighlight %}


<div id='id-section4'/>
## Los enum como Singleton.

Usando enum en Java es fácil implementar el patrón Singleton:

{% highlight Java %}

public enum Singleton {
    INSTANCE;
    private Singleton() {
        //Es llamado la primera vez cuando el enum es inicializado
        System.out.println("I am initialised");
    }
}

{% endhighlight %}

Como puedes ver, provee una simple y correcta implementación del patron Singleton.Además, como es un enum, este no tiene que ser una sola instancia, podrían ser dos ...

<div id='id-section5'/>
## Reemplazar los if anidados por enum.

Para mostrar un ejemplo, vamos a modelar el juego Piedra-Papel-Tijera para ello usamos un enumerado que representa las tres señas posibles.

{% highlight Java %}

enum HandSign {  SCISSOR, PAPER, ROCK  }

{% endhighlight %}

Queremos obtener una seña aleatorio para la máquina y mostrar un mensaje en consola. Una posible implementación sería:

{% highlight Java %}

//generar un enum aleatorio
computerMove = HandSign.values()[new Random().nextInt(HandSign.values().length)];
//mostar mensaje informando la opción generada
if (computerMove == HandSign.SCISSOR) {
    System.out.println("   Mi turno: Tijera");
} else if (computerMove == HandSign.PAPER) {
    System.out.println("   Mi turno: Papel");
} else {
    System.out.println("   Mi turno: Piedra");
}

{% endhighlight %}

Este caso es muy sencillo, unos pocos estados y con un comportamiento asociado muy sencillo, mostrar un mensaje.

También se podría haber usado la sentencia switch, pero una forma mas elegante es representarlo usando enum.

El enum ahora además de representar un conjunto fijo de constantes tiene una lógica asociada a cada constante mediante el método abstracto show().


{% highlight Java %}
public enum HandSign {
    SCISSOR {
        @Override
        public void show() {
            System.out.println("   Mi turno: Tijera");
        }
    },
    PAPER {
        @Override
        public void show() {
            System.out.println("   Mi turno: Papel");
        }
    },
    ROCK {
        @Override
        public void show() {
            System.out.println("   Mi turno: Piedra");
        }
    };

    public abstract void show();
}
{% endhighlight %}

Hemos logrado encapsulación y un código fuente mas legible.

{% highlight Java %}
//generar seña
computerMove = HandSign.values()[new Random().nextInt(HandSign.values().length)];
//mostrar seña
HandSign.valueOf(computerMove.name()).show();

{% endhighlight %}

<div id='id-section6'/>
## Usar interfaces para extender enum.

Como mencionammos al comienzo de este post, los enum no pueden ser extendidos. La mayoría de las veces extender enums es una mala idea, pero hay casos donde tiene sentido.

Para el caso nuestro juego Piedra-Papel-Tijera, tenemos un enumerado que describe los distintos niveles de juego y una comportamiento asociado, usando la misma técnica del método abstracto.

{% highlight Java %}

public enum GameMode {

    LEVEL_NOVICE {
        @Override
        public Game proccesGame(HandSign player, Params params) {
            // lógica que procesa el juego nivel novato

        }

    }, LEVEL_GOD {
        @Override
        public Game proccesGame(HandSign player, Params params) {
            // lógica que procesa el juego nivel Dios

        }

    };

    public abstrac Game proccesGame(HandSign player, Params params);
}

{% endhighlight %}

El problema con este enumerado es que no esta basado en un conjunto fijo de constantes bien conocidas, y puede darse el caso  que en futuros requerimiento podríamos tener que soportar otros niveles de juegos, por ejemplo, un nuevo nivel LEVEL_ADVANCED.

El enumerado anterior HandSign que describe las señas posibles del juego Piedra-Papel-Tijera no va a cambiar en el tiempo porque define el juego, si agregáramos mas señas el juego sería otro.

Una posibilidad sería modificar el código fuente de GameMode, agregar esta nueva constante e implementar el método abstracto. Esto viola uno de los principios SOLID ,el principio de Abierto/Cerrado, cerrado a la modificación y abierto a la extensión.

Los enum violan el principio Abierto/Cerrado porque no permiten la extensión sin modificación.

 La forma de solucionar esto, es usar una interface mas que un enum, a veces esto se hace instintivamente cuando trabajamos con clases.

{% highlight Java %}

public interface IGameMode {

    public abstract Game proccesGame(HandSign player, Params params);
}

{% endhighlight %}

Y el enumerado implementando la interface IGameMode.

{% highlight Java %}

public enum GameMode implements IGameMode {

    LEVEL_NOVICE {
        @Override
        public Game proccesGame(HandSign player, Params params) {


        }

    }, LEVEL_GOD {
        @Override
        public Game proccesGame(HandSign player, Params params) {

        }

    };

}

{% endhighlight %}

Ahora ya podemos crear un nuevo enum que implemente la interface IGameMode para la constante LEVEL_ADVANCED y lograr extensión sin modificación.


<div id='id-conclusiones'/>
## Conclusiones.

Los enum de Java son poderosos para modelar dominios que describen un conjunto fijo de constantes, como son clases podemos asociarles información y comportamiento.

Los enum son una alternativa al uso de sentencia swith e if anidados.

Los enum no permiten la extensión, siempre debes evaluar el dominio y determinar si modificas el código agregando el nuevo estado al enumerado o usas interface. El principio Abierto/Cerrado, es un principio y no un dogma a ser aplicado en todos los casos.

Usamos el juego Priedra-Papel-Tijera para mostrar el uso de los enum, el código fuente puedes verlo en [https://github.com/juanek](https://github.com/juanek).

{% highlight Console %}

$ mvn exec:java -Dexec.mainClass="ar.com.jekipes.enums.RockPaperScissors"

Vamos a comenzar...

Piedra-Papel-Tijera


(Ingresa s para Tijera , p para Papel, t para Piedra, q para salir): s
Tu Turno: Tijera
Mi turno: Papel

***************************************************
  Tu ganas!
***************************************************
Piedra-Papel-Tijera


(Ingresa s para Tijera , p para Papel, t para Piedra, q para salir):

{% endhighlight %}

<div id='id-referencias'/>
## Referencias.

[https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html](https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html)


[https://blog.scottlogic.com/2016/07/28/java-enums-how-to-use-them-smarter.html](https://blog.scottlogic.com/2016/07/28/java-enums-how-to-use-them-smarter.html)


[Effective Java, 3rd Edition](https://www.oreilly.com/library/view/effective-java-3rd/9780134686097/)
