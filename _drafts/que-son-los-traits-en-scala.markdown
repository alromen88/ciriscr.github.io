---
layout: post
title:  "¿Qué son los traits de Scala?"
tags: scala trait programación
autor: Pablo Peraza
imagenAutor: pious
---

<div class="justificado">
  <p>
    Muchas veces, al comenzar a trabajar en Scala nos encontramos con los razgos o "traits",
    que desafortunamente tienen a confundir a los que se inician en Scala. Veremos que
    en realidad, los traits no solamente son un tema simple y sencillo, sino que además,
    proveen una gran cantidad de facilidades que nos permitirán programar mejor.
  </p>
  <p>
    Este post lo estaré haciendo cargado de ejemplos, pues creo que es la mejor forma
    de explicar los traits. Además, originalmente esto fue publicado en quora. Pueden 
    ver mi respuesta original (en inglés) <a href="http://www.quora.com/Scala/What-are-mixins-and-traits-in-Scala">aquí</a>.
  </p>
  <h2>Ejemplo sencillo</h2>
  <p>
    En resumen, los traits de Scala son interfaces de Java que pueden tener funciones 
    implementadas.
    Ahora, los traits de scala son una característica realmente poderosa del lenguaje y 
    hay varias cosas interesantes que se pueden hacer con ellos. Primero, veamos
    su uso más básico: como interfaces de Java.    
  </p>

  <code data-gist-id="7605595" 
    data-gist-file="Colors.scala"    
    data-gist-hide-footer="true"></code>
  <p>
    Evidentemente, esto es un ejemplo muy simple, pero sirve para ilustrar la forma en que 
    se puede definir un "base trait Color"  que tiene 2 métodos: "nombre" que no tiene 
    implementación y "toString", el cual depende de la implementación del método "nombre".
    
    Los otros 3 traits son simples especializaciones del trait base "Color" y simplemente 
    definen la función "nombre".
  </p>
  <p>
    En este ejemplo, se puede apreciar que si llamamos al método "toString" de la clase 
    MiRojo, vamos a obtener el valor "Color Rojo".
    Por supuesto, podemos extender directamente del trait "Color" en una clase, y lo único 
    que necesitarímos sería definir el método "nombre" o hacer la clase abstracta.
  </p>
  <p>
    En este punto, podemos ver que los traits son bastante similares a las interfaces en 
    Java, pero al tener la capacidad de poder definir funcionalidad en los traits
    los convierten fácilmente en un artefacto mucho más poderoso.
  </p>
  <p>
    Scala llama a esto "Interfaces ricas" (contrario a las "interfaces delgadas" de Java).
    Veamos otro ejemplo:    
  </p>
  <h2>Interfaces ricas</h2>
  <code data-gist-id="7605595" 
    data-gist-file="Point.scala"    
    data-gist-hide-footer="true"></code>
  <p>
    Y como último ejemplo de como los traits se parecen a las interfaces en Java, se puede
    extender una clase con múltiples traits:
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="MyClass.scala"    
    data-gist-hide-footer="true"></code>

  </p>
  <p>
    Al ver que los traits nos permiten definir comportamiento, y que podemos extender de 
    múltiples traits al mismo tiempo, ¡parece que de inmediato se encidiesen todas las 
    alarmas de herencia múltiple!, por que de hecho, los traits son una forma de herencia
    múltiple.     
  </p>  
  <p>
    Por dicha, Scala tiene una elegante forma de resolver el 
    <a href="http://es.wikipedia.org/wiki/Problema_del_diamante">"Problema del diamante"</a>.
    Pero antes de pasar a este tema, veamos un último ejemplo de composición (que nos 
    servirá luego para explicar la solución al problema del diamante).    
  </p>
  <h2>Ejemplo de Interfaces ricas</h2>
  <p>
    Como primer paso, definimos una típica clase abstracta "OperacionInt" que lo único que 
    (por el momento) es definir una operación "op" que retorna como resultado el mismo número
    de entrada.
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Operations.scala"
    data-gist-line="1-3"
    data-gist-hide-footer="true"></code>
  <p>
    Con esta clase abstracta, definamos entonces dos traits que los usaremos como <a href="http://es.wikipedia.org/wiki/Mixin">"mixins"</a>.
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Operations.scala"
    data-gist-line="5-11"
    data-gist-hide-footer="true"></code>
  <p>
    Aunque de momento parece un poco extraño la sintaxis de estos dos traits, todo tendrá 
    más sentido cuando veamos la salida del programa. De momento, aprovechemos para
    definir una clase concreta que implemente nuesta "OperacionInt".
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Operations.scala"
    data-gist-line="13"
    data-gist-hide-footer="true"></code>
  <p>
    Veamos entonces, una salida del sistema:
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Output.scala"
    data-gist-line="1-5"
    data-gist-hide-footer="true"></code>
  <p>
    Podemos ver que instanciar una clase "MiOP" y el resultado de su operación "op" son
    los esperados. Qué pasará si mezclamos el trait "Doblado" en la ecuación:
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Output.scala"
    data-gist-line="8-14"
    data-gist-hide-footer="true"></code>
    <p>
      Ahora sí podemos ver como el trait "Doblado" ha modificado radicalmente el 
      comportamiento de nuestra operación "op". Debido a que este "artefacto" es bastante
      común, en Scala es posible mezclar (mix in) los traits a la hora de instanciación 
      del objeto. Con esto, no es necesario estar creando definiciones de clases que 
      implementen los traits:
    </p>
    <code data-gist-id="7605595" 
    data-gist-file="Output.scala"
    data-gist-line="17-18"
    data-gist-hide-footer="true"></code>
  <p>
    Si llamásemos a "mezclado.op(5)", obtendríamos como resultado "10". Hemos entonces,
    modificado el comportamiento de nuestros objetos en tiempo de instanciación.    
  </p>  
  <h2>El problema del diamante</h2>
  <p>
    Todo esto resulta muy bien pero ¿qué pasa si intentamos mezclar los traits al mismo 
    tiempo? Recordemos que tanto "Doblado" como "Incrementado" modifican por cuenta propia
    el comportamiento de la operación "op" de la clase "OperacionInt". Hagamos la prueba:
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Output.scala"
    data-gist-line="21-25"
    data-gist-hide-footer="true"></code>
  <p>
    Analicemos un poco el comportamiento que hemos obtenido. Primero (y esta es la clave
    de como Scala resuelve el problema del diamante), podemos concluir que el comportamiento
    de "Incrementado" se resuelve primero
    <pre>5 + 1 = 6</pre>
    Y luego, el comportamiento de "Doblado" resuelve:
    <pre>6 * 2 = 12</pre>
  </p>
  <p>
    Tratemos ahora, de realizar la extensión pero con diferente orden:
  </p>
  <code data-gist-id="7605595" 
    data-gist-file="Output.scala"
    data-gist-line="28-33"
    data-gist-hide-footer="true"></code>
  <p>
      Ahora, el comportamiento de Doblado se resuelve primero:
      <pre>5 * 2 = 10</pre>
      Y finalmente, el comportamiento de Incrementado:
      <pre>10 + 1 = 11</pre>
  </p>
  <p>
    En este punto, es evidente que Scala resuelve el problema del diamante de una forma
    lineal y dependiente del orden (de derecha a izquierda) con que sean extendidas las
     clases con los traits. El trait que sea extendido de último, será el primero en 
     resolver y modificar el comportamiento.
  </p>
  <h2>Conclusión</h2>
  <p>
    Los traits en Scala son en definitiva, una de las herramientas más poderosas que posee
    el lenguaje en cuanto a orientación a objetos. Nos permite, no solamente reducir el
    tamaño del código de forma considerable, sino que además nos provee de formas
    de modificar comportamientos para casos donde sea necesario.
  </p>  
</div>