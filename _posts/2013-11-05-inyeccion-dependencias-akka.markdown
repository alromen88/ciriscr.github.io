---
layout: post
title:  "Inyección de dependencias de base de datos en Akka"
tags: scala akka mongodb casbah salat dependencias monad reader
autor: Pablo Peraza
imagenAutor: pious
---

<div class="justificado">
  <p>
    Muchas veces nos encontramos programando en capas, y nos damos cuenta que las capas 
    de mayor profundidad, no conocen o no tienen disponible el contexto adecuado
    para decidir sobre qué recurso usar (Ej. conexión a desarrollo/preproducción/producción,
    etcétera). A continuación voy a describir una posible solución a como inyectar 
    dependencias desde capas superiores, concretamente, como inyectar la conexión a la base
    de datos usando el patrón Lector (Reader Monad).
  </p>
  <p>
    Comencemos con el actor de base datos. En Ciris usamos el driver de Scala para 
    MongoDB llamado <a href="http://api.mongodb.org/scala/casbah/2.0/">Casbah</a>. Además,
    también usamos <a href="https://github.com/novus/salat/">Salat</a>, que es una librería
    de serialización de objetos de Scala a MongoDB.
  </p>
  <h2>Actor de Base de Datos</h2>
  <p>
    Este actor es un simple envoltorio de un SalatDAO. Aquí bien podría haber una conexión
    JDBC, un servicio REST, o cualquier otra fuente de datos que requiera algún tipo de
    conexión. He separado el código en 3 objetos:
    <ul>
      <li>El actor en sí</li>
      <li>Un trait que contiene la lógica (queries, deletes, updates, etc.)</li>
      <li>Un companion object que simplemente tiene funciones utilitarias para ayudar a instanciar el actor.</li>
   </ul>   
  </p>  
  <p>
    <h3>El actor</h3>
    <code data-gist-id="7313054" 
    data-gist-file="ServicioBDUsuario.scala"
    data-gist-line="1-15" 
    data-gist-hide-footer="true"></code>
    <p>
      Como se puede apreciar, el actor es un típico actor de akka con 1 sólo parámetro de
      clase: la conexión a la base de datos a usar. Este parámetro es el que queremos
      inyectar en las capas externas de nuestro software.
    </p>
    <p>
      Tenemos además 2 atributos internos: 1) el objeto DAO (que le pasamos la conexion que
      hemos recibido) y 2) la lógica del actor. Hemos decidido separar en un trait externo
      la lógica el actor por varias razones, pero por ahora se puede ver como una forma de organizar 
      y especializar nuestro código y a su vez, nos permitirá probar la lógica
      independientemente del actor. El DAO, por otro lado, lo he marcado como implícito
      pues la lógica a su vez, espera que el DAO le sea inyectado.
    </p>
    <p>
      En el receive tenemos defina la función para cuando recibimos un String (el login del
      usuario por ejemplo.) Lo único que hacemos es invocar a la lógica y devolver la 
      respuesta al sender.
    </p>
  </p>
  <p>
    <h3>La lógica</h3>
    <code data-gist-id="7313054"
    data-gist-file="ServicioBDUsuario.scala" 
    data-gist-line="17-19" 
    data-gist-hide-footer="true"></code>
    <p>
      Como ya he comentado, hemos decidido separar las funciones en un trait aparte del 
      actor. En este caso, nuestra lógica es bastante simple: únicamente tiene una función
      que utiliza un DAO (el cual le es inyectado implícitamente) para buscar a un usuario
      en la base de datos por medio de su login.
    </p>
  </p>  
  <p>
    <h3>Companion Object</h3>
    <code data-gist-id="7313054" 
    data-gist-file="ServicioBDUsuario.scala"
    data-gist-line="21-44" data-gist-hide-footer="true"></code>
    <p>
      Esta "fábrica" existe para facilitarnos la vida. Primero, define un método para obtener
      un objeto Props del actor.
    </p>
    <p>
      Segundo, tenemos una función "nuevo", la cual nos devuelve una nueva instancia
      del actor encapsulada en un ActorRef. Tiene 2 listas de parámetros: un posible nombre
      y la otra sería la fábrica implícita de actores. (Nuevamente, esta fábrica nos será
      inyectada por alguien más).
    </p>
    <p>
      Finalmente, el método apply es el que realiza la magia monádica: Recibiendo un posible
      nombre, nos devuelve una función ContextoConexion => ActorRef.
      Esto, básicamente nos encapsula la funcionalidad del método "nuevo" en una función a la cual 
      se le debe aplicar un contexto para obtener el resultado deseado.
      Además, por medio de una conversión implícita, convertimos nuesta función
       ContextoConexion => ActorRef en una función de tipo 
       ContextoConexion => LectorBD[ActorRef]. Esto nos dará una gama de posibilidades 
       monádicas cuando tratemos de utilizar estas funciones.            
    </p>
  </p>

  <h2>Monad Lector</h2>
  <p>
    Antes de ver el monad, veamos primero nuestro contexto de conexion.
  </p>
    <code data-gist-id="7313054" 
    data-gist-file="ContextoConexion.scala"
    data-gist-hide-footer="true"></code>
  <p>
    Este ContextoConexion tiene 2 atributos: la conexión a la base de datos y la fábrica de
    actores que queremos usar. Si lo han notado ya, estos son los dos parámetros que
    queremos inyectar a lo largo de nuestro software.
  </p>
  <p>
    <h3>El Monad</h3>
    <code data-gist-id="7313054" 
    data-gist-file="LectorBD.scala"
    data-gist-line="1-22"
    data-gist-hide-footer="true"></code>
    <p>
      Aquí hay varios aspectos del código que merecen una explicación. Primero, las 
      funciones <pre>map[B](transformacion: A => B): LectorBD[B]</pre>
       <pre>flatMap[B](aplanar: A => LectorBD[B]): LectorBD[B]</pre>
       <pre>filter(filtrado: A => A): LectorBD[A]</pre> 
       son las proveedoras de nuestras funciones monádicas. Con ellas podemos trasformar
       nuestra función original ContextoConexion => LectorBD[ActorRef]
       en una función ContextoConexion => LectorBD[B] donde B es cualquier tipo de objeto.
       Además, al definir map, flatMap y filter ganamos otra funcionalidad: podremos extraer
       nuestras funciones en un "for comprehension".
    </p>
    <p>
      El método apply es el que se encarga de recibir el parámetro ContextoConexion y 
      aplicarlo a nuestra función ContextoConexion => A. Esto nos dará como 
      resultado un objeto de tipo A.
      El segundo apply, que tiene 2 listas de parámetros, es todavía más útil que el primero, 
      pues sabe construir el contexto a partir de un parámetro implicíto y 
      otro explícito.      
    </p>
  </p>
  <p>
    <h3>El Monad Companion</h3>
    <code data-gist-id="7313054" 
    data-gist-file="LectorBD.scala"
    data-gist-line="24-29"
    data-gist-hide-footer="true"></code>
    <p>
      En nuestro companion object, definimos una función utilitaria (simple[A]) que convierte
      cualquier valor A en un Monad LectorBD[A].
    </p>
    <p>
      La segunda funcionalidad es nuestra conversión implícita de funciones. Como se 
      puede ver, esta conversión toma una función de tipo ContextoConexion => A y la 
      encapsula en nuestro objeto LectorBD[A]. Por ende, cada vez que devolvamos una 
      función ContextoConexion => A, esta será convertida a un LectorBD[A] sin
      que nosotros tengamos que hacer nada más. Los métodos map, flatMap y filter
      son los casos en los que se puede ver en acción esta transformación.
    </p>
  </p>
  <h2>¡Probemos el código!</h2>
  <p> 
    Veamos ahora, como usar nuestro código. Esto bien podría ser en una capa exterior o 
    en cualquier otro lugar donde se vaya a ejecutar nuestro código. Veamos primero como
    probar nuestra lógica (el trait de lógica del actor):
  </p>
    <code data-gist-id="7313054" 
    data-gist-file="ServicioBDUsuarioTest.scala"
    data-gist-line="3-14" data-gist-hide-footer="true"></code>
  <p>
    Aquí, genero un DAO con su conexión (sin inyección, nosotros mismos vamos a proveer la 
    conexión in-situ) y lo asigno a una variable implícita. Recordemos que la lógica
    espera que le sea inyectado un DAO para poder trabajar.
  </p>
  <p>
    <h3>Actor e inyecciones monádicas</h3>
    <code data-gist-id="7313054" 
    data-gist-file="ServicioBDUsuarioTest.scala"
    data-gist-line="16-39" data-gist-hide-footer="true"></code>
    <p>
      Nuevamente, aquí hay varias cosas que explicar. Primero, defino 2 valores implícitos:
      un timeout y una fábrica de actores (¿recuerdan, esa fábrica de actores que en 
      muchos lugares la recibimos implicitamente? Pues, ¡aquí está!).
    </p>
    <p>
      Segundo, invoco al companion object de mi actor con ServicioBDUsuario(). Este llamado
      invoca al método apply(nombre: Option[String] = None) de object ServicioBDUsuario. 
      Como no estoy pasándole ningún parámetro, se invocará con un valor None. Esta 
      invocación, me da un objeto monádico LectorBD[ActorRef] (osea,
      una función ContextoConexion => ActorRef).
    </p>
    <p>
      Luego, demuestro como usar la función de dos formas distintas para obtener el mismo
      valor: primero con un map, y luego con un for. Nótese, que tanto el resultado del
      map como del for, es un objeto de tipo LectorBD[Future[Option[Usuario]]].
      Lo único que ocupamos ahora, es simplemente aplicarle una conexion de base de datos
      a ambos resultados para obtener sus contenidos (Future[Option[Usuario]]). Lo bueno
      de todo esto es que, como podrán ver, en ningún momento me he preocupado
      por mi conexión a base de datos. Es más, pareciera que no la necesito,
       y esa es la idea.
    </p>
    <p>
      Finalmente, le inyectamos la conexión a nuestros objetos y esperamos a que el futuro
      se resuelva para desplegar los resultados.
    </p>
  </p>
  <p>
    Pueden ver el gist completo en <a href="https://gist.github.com/piousp/7313054">https://gist.github.com/piousp/7313054</a>.
    Muchas gracias y ¡felices inyecciones!
  </p>
</div>