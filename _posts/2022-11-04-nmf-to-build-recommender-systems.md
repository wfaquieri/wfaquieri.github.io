## Construindo sistemas de recomendação usando NMF

Nesse post vamos descobrir como construir um sistema de recomendação utilizando a linguagem **Python** e uma técnica de álgebra linear conhecida como fatoração de matriz não negativa ou, simplesmente, NMF.

A motivação: suponha que você trabalhe em um grande jornal online e deseje recomendar artigos semelhantes ao que está sendo lido por cada cliente. Caso você seja bem sucedido nessa tarefa, é esperado que a experiência de usuário desse cliente melhore e ele se sinta mais satisfeito com o serviço oferecido por esse jornal. 

Mas, dado um artigo, como você pode encontrar artigos que tenham tópicos semelhantes? Bom, esse é um problema de "Modelagem de tópicos".

- Explicar o que é NMF:

1. Aplique NMF à matriz de frequência de palavras

```python
from sklearn.decomposition import NMF
nmf = NMF(n_components=6)
nmf_features = nmf.fit_transform(articles)
```
2. Similaridade de Cosseno

```python
from sklearn.preprocessing import normalize
norm_features = normalize(nmf_features)
# if has index 23
current_article = norm_features[23,:]
similarities = norm_features.dot(current_article)
#=> # calculando as similaridades.
```

```python
import pandas as pd
df = pd.FataFrame(norm_features, index=titles)
current_article = df.loc['Dog bites man']
similarities = norm_features.dot(current_article)
print(similarities.nlargest())
#=> # Artigos com maior similaridade em relação ao 'Dog bites man'.
```

