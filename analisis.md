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

```python
D = list(tweet_iterator('delitos_train.json'))
bow = load_bow(lang='es')
X = bow.transform([x['text'] for x in D])
m = LinearSVC().fit(X, [x['label'] for x in D])
w = m.coef_[0]
```

```python 
_ = {bow.id2token[id]: _w * w[id] for id, _w in bow.token_weight.items() if w[id] > 0}
words = {k: v for k, v in _.items() if k[:2] == 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

![Palabras asociadas](/Delitos/assets/images/pos_words.png)

```python 
_ = {bow.id2token[id]: _w * w[id] for id, _w in bow.token_weight.items() if w[id] > 0}
words = {k[2:]: v for k, v in _.items() if k[:2] != 'q:'}
word_cloud = WordCloud().generate_from_frequencies(words)
plt.imshow(word_cloud, interpolation='bilinear')
```

![q-grams asociados](/Delitos/assets/images/pos_qgrams.png)



