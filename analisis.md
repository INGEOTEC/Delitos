---
layout: default
title: Análisis
nav_order: 2
---

# Nube de Palabras y q-grams de Caracteres
{: .fs-10 }

---

Para dar una idea de el conjunto de datos etiquetado, la siguiente figura presenta dos 
nubes. La primera corresponde a las palabras mas significativas para seleccionar la clase 
positiva y la segunda nube contienen los q-gramas más significativos. Estos tokens fueron 
seleccionados de acuerdo al coeficiente calculado por la máquina de soporte vectorial 
entrenada en el conjunto de datos creado.

Suponiendo que el texto se encuentra en la llave *text* el siguiente código entrena 
una máquina de soporte vectorial lineal usando una bolsa de palabras previamente 
calculada.

```python
from microtc.utils import tweet_iterator
from EvoMSA.utils import load_bow
from sklearn.svm import LinearSVC

D = list(tweet_iterator('delitos_train.json'))
bow = load_bow(lang='es')
X = bow.transform([x['text'] for x in D])
m = LinearSVC().fit(X, [x['label'] for x in D])
w = m.coef_[0]
```

Para sacar la bolsa de palabras se toma en cuenta el peso que tiene la palabra
en la estadística calculada en el *inverse document frequency* del TFIDF, 
i.e., `bow.token_weight.items()`. Se seleccionan solo los tokens que tienen
un peso positivo y se calcula el producto con el coeficiente obtenido 
en el clasificador. 

```python 
tokens = {bow.id2token[id]: _w * w[id] for id, _w in bow.token_weight.items() if w[id] > 0}
```

La variable `tokens` tiene el peso de cada token para la clasificación, en el siguiente
codigo se seleccionan solo las palabras y se genera la bolsa de palabras.

```python
from matplotlib import pylab as plt
from wordcloud import WordCloud

words = {k: v for k, v in tokens.items() if k[:2] != 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

La siguiente figura muestra el resultado del proceso anterior. 

![Palabras asociadas](/Delitos/assets/images/pos_words.png)

Complementando el procedimiento anterior, el siguiente código muestra
los q-gramas más significativos.

```python 
words = {k[2:]: v for k, v in tokens.items() if k[:2] != 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

La siguiente figura muestra la nube de palabras generada con los
q-gramas más significativos. 

![q-grams asociados](/Delitos/assets/images/pos_qgrams.png)

Para el caso donde no se cuenten con los textos, el modelo 
obtenido por la máquina de soporte vectorial lineal, se puede 
obtener con el siguiente código. 

```python
from EvoMSA.utils import load_dataset
m = load_dataset(lang='es', name='delitos_ingeotec', k=1)
```

