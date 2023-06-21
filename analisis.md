---
layout: default
title: Análisis
nav_order: 2
---

# Nube de Palabras y q-grams de Caracteres
{: .fs-10 }

---

Para dar una idea de el conjunto de datos etiquetado, 
la siguientes dos figuras presentan dos nubes. 
La primera corresponde a las palabras 
mas significativas para seleccionar la clase 
positiva y la segunda nube contienen 
los q-gramas más significativos. Estos tokens fueron 
seleccionados de acuerdo al coeficiente calculado por 
la máquina de soporte vectorial 
entrenada en el conjunto de datos creado.

![Palabras asociadas](/Delitos/assets/images/pos_words.png)
![q-grams asociados](/Delitos/assets/images/pos_qgrams.png)

Las figuras anteriores se crearon siguiendo este procedimiento que empieza
suponiendo que el texto se encuentra en la llave *text*, 
entonces el siguiente código entrena una máquina de soporte vectorial 
lineal usando una bolsa de palabras previamente calculada.

```python
from EvoMSA import BoW
from microtc.utils import tweet_iterator

D = list(tweet_iterator('delitos_train.json'))
bow = BoW(lang='es').fit(D)
w = bow.estimator_instance.coef_[0]
```

Para sacar la bolsa de palabras se toma en cuenta el peso que tiene la palabra
en la estadística calculada en el *inverse document frequency* del TFIDF, 
i.e., `bow.token_weight.items()`. Se seleccionan solo los tokens que tienen
un peso positivo y se calcula el producto con el coeficiente obtenido 
en el clasificador. 

```python 
tokens = {bow.bow.id2token[id]: _w * w[id] for id, _w in bow.bow.token_weight.items() if w[id] > 0}
```

La variable `tokens` tiene el peso de cada token para la clasificación, en el siguiente
código se seleccionan solo las palabras y se genera la bolsa de palabras.

```python
from matplotlib import pylab as plt
from wordcloud import WordCloud

words = {k: v for k, v in tokens.items() if k[:2] != 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

Complementando el procedimiento anterior, el siguiente código muestra
los q-gramas más significativos.

```python 
words = {k[2:]: v for k, v in tokens.items() if k[:2] != 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

Para el caso donde no se cuenten con los textos, el modelo 
obtenido por la máquina de soporte vectorial lineal, se puede 
obtener con el siguiente código. 

```python
from EvoMSA.utils import load_dataset
m = load_dataset(lang='es', name='datasets')
m = [i for i in m if i.labels[-1] == 'delitos_ingeotec'][0]
```

# Rendimiento

El conjunto de datos está divido en dos partes un conjunto de entrenamiento
y otro de prueba. Con el conjunto de entrenamiento se entrenó una máquina
de soporte vectorial lineal y en esta sección se presentan algunas medidas
de rendimiento de este algoritmo medido en el conjunto de prueba. 

El proceso para entrenar el modelo fue utilizado para crear la bolsa de
palabras mostradas en la sección anterior. El siguiente código 
se puede utilizar para usar el modelo y predecir los datos 
del conjunto de prueba. 

```python
from EvoMSA import BoW
from EvoMSA.utils import load_dataset

bow = BoW(lang='es')
m = load_dataset(lang='es', name='datasets')
m = [i for i in m if i.labels[-1] == 'delitos_ingeotec'][0]
bow.estimator_instance = m
```

Suponiendo que el conjunto de prueba tiene el campo *text* que contiene
el texto del tuit entonces se puede predecir la clase de la siguiente manera.

```python
from microtc.utils import tweet_iterator

Dtest = list(tweet_iterator('delitos_test.json'))
hy = bow.predict(Dtest)
```

Con las predicciones se puede calcular el rendimiento de
del algoritmo, la siguiente tabla muestra la medida *f1*, el *recall* y 
la *precision*, todas estas calculadas con la librería `sklearn`.
La tabla también incluye la error estandar, calculado mediante bootstraping.

| f1 | recall | precision |
|----|--------|-----------|
|$$0.7727 \pm 0.0418$$| $$0.6711 \pm 0.0572$$| $$0.9107 \pm 0.0384$$|

El siguiente código se utilizó para medir el rendimiento. 
```python
from sklearn.metrics import f1_score, recall_score, precision_score
import numpy as np

y = np.array([x['label'] for x in Dtest])

funcs =  [f1_score, recall_score, precision_score]
B = []
for _ in range(500):
    s = np.random.randint(y.shape[0], size=y.shape[0])
    _ = [func(y[s], hy[s]) for func in funcs]
    B.append(_)
B = np.array(B)

medida = [func(y, hy) for func in funcs]
error_estandar = B.std(axis=0)
``` 

# Etiquetadores

Siguiendo el mismo procedimiento utilizado para medir el algoritmo
desarrollado se puede medir la etiqueta de cada anotador, es decir,
la clase verdadera es el consenso y se toma cada una de las etiquetas individuales
como predicciones. 

Se tienen tres anotaciones por cada tuit, entonces el rendimiento
de estos etiquetadores se muestra en la siguiente tabla. 

| f1 | recall | precision |
|----|--------|-----------|
|$$0.9737 \pm 0.0128$$| $$0.9737 \pm 0.0180$$| $$0.9737 \pm 0.0171$$|
|$$0.9157 \pm 0.0208$$| $$1.0000 \pm 0.0000$$| $$0.8444 \pm 0.0352$$|
|$$0.9660 \pm 0.0165$$| $$0.9342 \pm 0.0305$$| $$1.0000 \pm 0.0000$$|
