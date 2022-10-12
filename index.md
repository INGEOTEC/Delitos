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
de investigación [ingeotec](https://github.com/INGEOTEC). Este corpus contiene 1,300 tweets negativos (*label* 0) y 500 tweets positivos (*label* 1)

El proceso que se siguió fue primeramente buscar mensajes que tuvieran las palabras 
*robar*, *asaltar*, *quitar*, *secuestrar* y *asesinar* o q-gramas de estas palabras. 
Se creó una bolsa de palabras y q-gramas y se seleccionó, con las similitud coseno, los 
tuits más similares a la consulta creada con las palabras en días seleccionados al azar. 
Se recuperaban los 8 tuits más similares por día y se etiquetaron como positivos aquellos 
que mencionan algún delito y negativo aquellos que no tenían mención de delitos. 

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
