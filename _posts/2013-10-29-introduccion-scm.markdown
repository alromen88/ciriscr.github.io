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
<p>
	De manera tradicional, los controladores de versiones funcionan bajo la arquitectura cliente-servidor centralizada: Existe un servidor que aloja todo
	el código de fuente y que sirve dicho código a los clientes (programadores). Todo cambio que se haga, debe pasar por fuerza por este servidor especializado.
</p>
<p>
	Debido a la forma en que se tiende a trabajar y a visualizar el código (y la serie de versiones), se tiene a comparar el historial de version con un árbol, donde hay un "tronco" (trunk) y existen diferentes "ramas de desarrollo" (branches).
</p>
<p>
	Existen también otros conceptos que se aplican para casi todos los sitemas de control de versiones:
	- Traer cambios
	- Guardar y empujar cambios
	- Unir versiones
	- Conflictos
	- Head: La versión más reciente (último cambio)
</p>
<p>
	<h3>Desarrollos en paralelo</h3>
	Cuando existe la necesidad de trabajar en diferentes carecterísticas, o en algoritmos experimentales, se pueden crear nuevas ramas de desarrollo. Esto facilita el desarrollo en paralelo pues en un rama, no es necesario estar actualizando el código con el Head.
</p>
<p>
	<h3>Unión de ramas</h3>
	Después de haber trabajado en una rama, y ya habiendo probado que todo funcione,
	se puede "reintegrar" dicha rama de nuevo al trunk.
</p>
<p>
	<h3>Conflictos</h3>
	Es inevitable, que en algún momento, 2 personas modifiquen el mismo archivo de forma simultanea. Cuando esto paso, la última persona en tratar de guardar sus cambios tendrá un conflicto.

	<p>
		Un conflicto no es más que haber hecho cambios a un archivo, que ya está desactualizado. Muchas veces, el software controlador de versiones puede resolver conflictos simples, pero en ocasiones es necesario que haya
		intervención humana: se necesitaría analizar las 2 diferentes versiones y decidir como se deben unir en una versión nueva, que contenga los cambios de
		las dos versiones en conflicto.
	</p>
</p>
<h2>Qué es un sistema de control de versiones distribuido</h2>
<p>
	A diferencia de los sistemas "tradicionales" o centralizados, donde el esquema 
	es el de un servidor centralizado de código, en los sistemas distribuídos todos los
	clientes y pueden ser (y de hecho, lo son) servidores del código fuente. Cada cliente pues,
	tiene su propio servidor (local) y su propia copia local del código. Aunque esto puede
	sonar complejo, en la práctica no lo es y uno normalmente no se preocupa por estos aspectos
	técnicos a la hora de programar.
</p>
<p>
	Habitualmente se fija siempre una máquina como repositorio "base" o principal, que sirve
	para que todos los desarrolladores puedan compartir sus cambios de forma más eficaz. La
	diferencia con un sistema centralizado, es que cada vez que uno hace "commit", lo hace en
	su servidor local, y si se desea compartir sus cambios, es necesario "empujar" (push) sus
	cambios a algún servidor remoto (que, de nuevo, normalmente se designa un servidor remoto
	como el servidor principal).
</p>
<p>
	Y con esto llego a esta leve intrudicción a los sistemas de control de versiones. Es un
	tema bastante amplio y podría escribir varias decenas de páginas sobre este software, pero 
	mi objetivo es justamente dar una breve introducción al tema. En el próximo tema, estaré
	comenzando un tutorial del uso de github.
</p>
</div>