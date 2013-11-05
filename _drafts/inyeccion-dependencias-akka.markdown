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
    de mayor profondidad, no conocen o no tienen disponible el contexto adecuado
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
  ##Actor de Base de Datos##
  <p>
    Este actor es un simple envoltorio de un SalatDAO. Aquí bien podría haber una conexión
    JDBC, un servicio REST, o cualquier otra fuente de datos que requiera algún tipo de
    conexión. He separado el código en 3 objetos:
     - El actor en sí
     - Un trait que contiene la lógica (queries, deletes, updates, etc.)
     - Un companion object que simplemente tiene funciones utilitarias para ayudar a instanciar el actor.
  </p>
  <script src="https://gist.github.com/piousp/7313054.js"></script>

  <h2>Monad Lector</h2>
  <script src="https://gist.github.com/piousp/7313097.js"></script>

  <h2>Todo junto: Probemos el código!</h2>
  <script src="https://gist.github.com/piousp/7313104.js"></script>
</div>