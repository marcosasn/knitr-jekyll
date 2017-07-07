---
layout: post
title: "Black Mirror ou Sense8?"
date: 2017-07-07 16:52:17
published: true
tags: [htmlwidgets, r]
author: Marcos Antonio Silva Nascimento (marcos.nascimento@ccc.ufcg.edu.br)
---



# Black Mirror ou Sense8?

Neste post vamos investigar como se comportou a avaliação de uma nova temporada após o término de uma temporada anterior. Será que a avaliação dos episódios de uma nova temporada cresce ou decresce quando a mesma é comparada a avaliação da temporada anterior?

Para responder essa pergunta vamos olhar para o gráfico que representa a curva de avaliação de todos os episódios da série.

{% highlight text %}
## Warning: Ignoring unknown aesthetics: text
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob2/unnamed-chunk-2-1.png)
Black Mirror apresenta uma variação considerável da avaliação dos episódios se comparada a série Sense8, além disso, esta também apresenta uma curva crescente.

Precisamente a avaliação dos episódios cresceu ou decresceu ao longo de cada temporada? 

{% highlight text %}
## Warning: Ignoring unknown aesthetics: text
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob2/unnamed-chunk-3-1.png)
Utilizando a mediana como métrica de comparação percebe-se claramente que Black Mirror mantém a avaliação dos episódios(variação em termos de décimos). Percebe-se também o crescimento da avaliação dos episódios de Sense8.

Podemos concluir que a avaliação dos episódios das novas temporadas de Black Mirror foi mantida enquanto a avaliação dos episódios da nova temporada de Sense8 foi acrescida.

Copyright © 2017 [Marcos Nascimento](https://github.com/marcosasn/AD1/blob/master/problema2/R/prob2_cpoint1.Rmd)
