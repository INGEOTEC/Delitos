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
Este corpus contiene 1,351 tuits negativos (*label* 0) y 449 tuits positivos (*label* 1)

El corpus está dividido en un conjunto de entrenamiento [delitos_train.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json) y un conjunto de
prueba [delitos_test.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json).

El formato de estos dos archivos es un json por linea, el json tiene tres llaves;
*annotations* que contienen la lista de etiquetas asignadas por tres personas, 
*id* es el identificador del tuit y *label* que es la clase asignada. El siguiente
código muestra un tuit etiquetado.

```json
{'annotations': [0, 0, 0], 'id': 1152148249157021698, 'label': 0}
```

# Etiquetado

El procedimiento que se siguió para etiquetar un tuit como positivo o negativo 
fue el siguiente.  Un tuit se identificó como positivo cuando se 
puede contestar la pregunta: **¿Cuál es el delito?** y además se debe 
cumplir que el mensaje sea **un hecho**, que el 
**objetivo, del mensaje, sea sobre el delito**, es decir, que no sea una opinión
y que **no se trate de un accidente.**

Con el propósito de clarificar estas reglas se presentan algunos mensajes controversiales. 

En los siguientes tres ejemplos, se está dando una opinión sobre un 
delito por ese motivo se marca como negativo.

    - Este caso tiene mucho que ver con lo que paso en EEUU, Kenosha —Kyle Rittenhouse, 
    el notorio adolescente blanco armado acusado de asesinar a dos personas e intentar 
    matar a una tercera durante una protesta por la violencia policial en agosto pasado, 
    fue declarado inocente.

    - Así se las gasta la gente del #FrenteAncho... 
    Los bomberos obligados a manifestarse vs Obrador y los disidentes amenazados... 
    Ayer asesinaron a uno de ellos... @IsmaelBombero señalado en video...

    - Amenazas a la fiscal Ximena Chong : Hagamos memoria - Estallido Social en Chile

En los siguientes tuits se infiere por el contexto que el delito es el 
tráfico de personas y conducción en estado de ebriedad dado que se está 
usando una patera y se menciona un chofer

    - Detenido el organizador de un viaje en patera en el que murieron 11 inmigrantes

    - Chofer de autobús escolar ebria es detenida por estudiantes valientes


En los siguientes mensajes se identifica el delito, pero el objetivo del 
mensaje es comunicar otro evento que no es un delito. 

    - La Policía Nacional homenajea a cuatro agentes asesinados en octubre

    - HOY SE CUMPLEN 18 AÑOS DEL ATAQUE TERRORISTA A LOS E.U.A. 
    Los atentados del 11 de septiembre de 2001 fueron una serie de cuatro atentados 
    terroristas suicidas cometidos en la mañana del martes #September11 #torresgemelas

    - Cuerpos de seguridad del estado desmantelaron la banda de roba coches

    - Radican cargos en ausencia a mujer por tentativa de asesinato en Aibonito

En los siguientes ejemplos se menciona que las personas fueron atropelladas lo cual se 
considera como delito, pero en el primero se puede suponer que fue un accidente y en el
segundo no hay información para suponer que el evento fue accidental.  

    - Accidente deja un muerto en #Galeras #SUCRE Benito Navarro iba en una moto 
    de Sincé hacia Galerías, paró en una orilla de la carretera a orinar, otra 
    moto que iba detrás lo atropelló, lanzándolo hacia la vía, un camión que 
    venía detrás le provocó heridas mortales en la cabeza.

    - Lamentablemente el conductor de una motocicleta perdió la vida al ser
    atropellado por un camión, esto en Avenida El Rosario casi con calle Cultura 
    Norte Colonia El Rosario Alcaldía de Azcapotzalco. 

# Procedimiento

El proceso que se siguió estuvo divido en tres etapas, en las primeras dos etapas
solo se involucró una persona y en la última etapa se involucrarón tres personas.

## Primera etapa

La primera etapa consistió en buscar mensajes que tuvieran las palabras 
*robar*, *asaltar*, *quitar*, *secuestrar* y *asesinar* o q-gramas de estas palabras. 
Se creó una bolsa de palabras y q-gramas y se seleccionó, con las similitud coseno, los 
tuits más similares a la consulta creada con las palabras en días seleccionados al azar. 
Se recuperaban los 8 tuits más similares por día y se etiquetaron como positivos aquellos 
que mencionan algún delito y negativo aquellos que no tenían mención de delitos, 
siguiendo las reglas mencionadas anteriormente. 

## Segunda etapa

Cuando en la primera etapa se tuvieron etiquetados un par de decenas 
de la clase positiva, se inició con la segunda etapa. En esta etapa se 
entrenó un clasificador donde el texto era representado en un espacio de emoticones 
(ver [EvoMSA](http://evomsa.readthedocs.io)) y el clasificador era un máquina de soporte 
vectorial lineal. Al azar se seleccionaban todos los tuits recolectados en un hora 
particular, de esos tuits se seleccionaban 2048 tuits, etiquetando los 8 tuits con el 
máximo valor de la función de decisión del clasificador. Este proceso se continuaba hasta 
que se terminaban todos los tuits recolectados en la hora, o hasta que se repetía algún 
tuit ya etiquetado. Finalmente, el modelo se actualizaban de manera automática cuando se 
seleccionaba otra hora. El proceso mencionado anteriormente se realizó hasta tener 
etiquetados 500 tuits con la clase positiva y se obtuvieron 1,500 tuits de la clase 
negativa.

## Tercera etapa 

En la tercera etapa se etiquetó el conjunto de 1,800 tuits por tres personas 
siguiendo las reglas mencionadas anteriormente. El conjunto etiquetado es
el que se encuentra en los archivos [delitos_train.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json) y [delitos_test.json](https://github.com/INGEOTEC/Delitos/blob/main/corpus/delitos_train.json).
