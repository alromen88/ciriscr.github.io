---
layout: post
title:  "Inyección de dependencias de base de datos en Akka"
tags: scala akka mongodb casbah salat dependencias
autor: Pablo Peraza
imagenAutor: pious
---

<div class="justificado">
  <p>
    Muchas veces nos encontramos programando en capas, y nos damos cuenta que las capas 
    de mayor profondidad, no conocen o no tienen disponible el contexto adecuado
    para decidir sobre qué recurso usar (Ej. conexión a desarrollo/preproducción/producción,
    etcétera).    
  </p>
</div>