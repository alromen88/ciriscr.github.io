---
layout: post
title:  "Introducción a los sistemas de control de versiones"
tags: scm dscm drcs version git mercurial hg
autor: Pablo Peraza
imagenAutor: pious
categories: capacitacion capitulo1 scm
---
<div class="justificado">
<p>
	La programación de software no trivial, hoy en día requiere de un esfuerzo coordinado de varias personas, no solamente de los programadores, sino también de ejecutivos, directores y demás involucrados en un proyecto de desarrollo.
</p>
<p>
	Muchas veces sucede, que las personas aprenden a escribir código, pero no a trabajar debidamente en un equipo de desarrollo, donde varios programadores 
	no sólo modifican constante el código fuente si no que además, realizan desarrollos de características nuevas en paralelo, se corrigen errores en <b>versiones previas</b>, o bien, se hacen "backports" de funcionalidades a antiguas
	revisiones.
</p>
<p>
	Conforme un equipo diseña, desarrolla y despliega software, es usual que diferentes
	versiones del mismo software sea desplegado en diferentes ambientes (por ejemplo,
	para pruebas y producción). Además, habitualmente ocurre que errores (bugs) y
	características se encuentren presentes en ciertas versiones del software y no en
	ciertas otras.También sucede que puede llegar a ser necesario trabajar en varias versiones diferentes al mismo tiempo.
</p>
<p>
	Todo esto tendría un alto costo y necesitaría que todos los programadores tengan 
	una disciplina de hierra de hierro para no cometer errores. Y aquí es donde entra 
	a funcionar un software de control de versiones: a ayudar a los programadores a 
	trabajar en esta gama de diferentes versiones de un mismo software.
</p>
<p>
	Es por esto, y por muchas otras razones más, que el uso de un sistema de control de versiones no solamente es un requisito para todo programador, sino que es una parte fundamental en el proceso de desarrollo de software.
</p>

<h2>Qué es un sistema de control de versiones</h2>
<p>
	Es un software que maneja los cambios que se realicen al código de fuente. Los cambios generalmente son identificados por un número de versión. Por ejemplo,
	el primer conjunto de archivos a guardar es la "revisión 1". Cuando el primer cambio es realizado, el conjunto de archivos resultantes es la "revisión 2", y así sucesivamente. Cada revisión tiene asociada una marca de fecha y tiempo, así como también la persona qué realizó el cambio. Las revisiones pueden ser comparadas, restauradas y/o unidas.
</p>

<h2>Qué es un sistema de control de versiones distribuido</h2>
<p>
</p>
<h2>Distribuido contra centralizado</h2>
</div>