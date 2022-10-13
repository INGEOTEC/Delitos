---
layout: default
title: Delitos
nav_order: 1
permalink: /
---

# Delitos
{: .fs-10 }

---

Este repositorio contiene el corpus de **Delitos** generado por el grupo
de investigación [ingeotec](https://github.com/INGEOTEC). 
Este corpus contiene 1,300 tweets negativos (*label* 0) y 500 tweets positivos (*label* 1)

El proceso que se siguió fue primeramente buscar mensajes que tuvieran las palabras 
*robar*, *asaltar*, *quitar*, *secuestrar* y *asesinar* o q-gramas de estas palabras. 
Se creó una bolsa de palabras y q-gramas y se seleccionó, con las similitud coseno, los 
tuits más similares a la consulta creada con las palabras en días seleccionados al azar. 
Se recuperaban los 8 tuits más similares por día y se etiquetaron como positivos aquellos 
que mencionan algún delito y negativo aquellos que no tenían mención de delitos. 

Se etiquetó un tuit como positivo cuando se puede contestar la pregunta:  **¿Cuál es el delito?** y
además que el mensaje sea **un hecho**, que el **objetivo del mensaje sea sobre el delito**, es decir, que no sea una opinión
y que **no se trate de un accidente.**

Con el propósito de clarificar estas reglas se presentan algunos mensajes controversiales. 

En los siguientes tres ejemplos, se está dando una opinión sobre un 
delito por ese motivo se marca como negativo.

    - Este caso tiene mucho que ver con lo que paso en EEUU, Kenosha —Kyle Rittenhouse, 
    el notorio adolescente blanco armado acusado de asesinar a dos personas e intentar 
    matar a una tercera durante una protesta por la violencia policial en agosto pasado, 
    fue declarado inocente.

    - Así se las gasta la gente del #FrenteAncho... 
    Los bomberos obligados a manifestarse vs Obrador y los disidentes amenazados... Ayer asesinaron a uno de ellos...
    @IsmaelBombero señalado en video...

    - Amenazas a la fiscal Ximena Chong : Hagamos memoria - Estallido Social en Chile

En los siguientes ejemplos se infiere por el contexto que el delito es el 
tráfico de personas y conducción en estado de ebriedad dado que se está 
usando una patera y se menciona un chofer

    - Detenido el organizador de un viaje en patera en el que murieron 11 inmigrantes

    - Chofer de autobús escolar ebria es detenida por estudiantes valientes


En los siguientes ejemplos se identifica el delito, pero el objetivo del 
mensaje es comunicar otro evento que no es un delito. 

    - La Policía Nacional homenajea a cuatro agentes asesinados en octubre

    - HOY SE CUMPLEN 18 AÑOS DEL ATAQUE TERRORISTA A LOS E.U.A. Los atentados del 11 de septiembre de 2001 fueron una serie de cuatro atentados terroristas suicidas cometidos en la mañana del martes #September11 #torresgemelas

    - Cuerpos de seguridad del estado desmantelaron la banda de roba coches

    - Radican cargos en ausencia a mujer por tentativa de asesinato en Aibonito


En los siguientes ejemplos se menciona que las personas fueron atropelladas lo cual se considera como delito, pero en el primero se puede suponer que fue un accidente y en el segundo no hay información para suponer que el evento fue accidental.  

    - Accidente deja un muerto en #Galeras #SUCRE Benito Navarro iba en una moto de Sincé hacia Galerías, paró en una orilla de la carretera a orinar, otra moto que iba detrás lo atropelló, lanzándolo hacia la vía, un camión que venía detrás le provocó heridas mortales en la cabeza.

    - Lamentablemente el conductor de una motocicleta perdió la vida al ser  atropellado por un camión, esto en Avenida El Rosario casi con calle Cultura Norte Colonia El Rosario Alcaldía de Azcapotzalco. 

Cada tuit fue etiquetado por tres personas siguiente las reglas mencionadas 
anteriormente.

Cuando se tuvieron etiquetados un par de decenas de la clase positiva, se entrenó un 
clasificador donde el texto era representado en un espacio de emoticones 
(ver [EvoMSA](http://evomsa.readthedocs.io)) y el clasificador era un máquina de soporte 
vectorial lineal. Al azar se seleccionaban todos los tuits recolectados en un hora 
particular, de esos tuits se seleccionaban 2048 tuits, etiquetando los 8 tuits con el 
máximo valor de la función de decisión del clasificador. Este proceso se continuaba hasta 
que se terminaban todos los tuits recolectados en la hora, o hasta que se repetía algún 
tuit ya etiquetado. Finalmente, el modelo se actualizaban de manera automática cuando se 
seleccionaba otra hora. El proceso mencionado anteriormente se realizó hasta tener 
etiquetados 500 tuits con la clase positiva y se obtuvieron 1,500 tuits de la clase 
negativa.

<!--
¿Cuál es el delito?
-->

El corpus está dividido en un conjunto de entrenamiento [delitos_train.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json) y un conjunto de
prueba [delitos_test.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json).
