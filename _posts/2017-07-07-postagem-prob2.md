---
layout: post
title: "Problema 2 - Checkpoint 1"
date: 2017-07-07 10:34:14
published: true
tags: [htmlwidgets, r]
author: Marcos Antonio Silva Nascimento (marcos.nascimento@ccc.ufcg.edu.br)
---



# Revisitando uma visualização sua
## Black Mirror x Sense8

Neste post vamos investigar como se comportou a avaliação de uma nova temporada após o término de uma temporada anterior. Será que a avaliação dos episódios de uma nova temporada cresce ou decresce quando a mesma é comparada a avaliação da temporada anterior?

Para responder essa pergunta vamos olhar para o gráfico que representa a curva de avaliação de todos os episódios da série.

{% highlight text %}
## Warning: Ignoring unknown aesthetics: text
{% endhighlight %}



{% highlight text %}
## Warning in normalizePath(path.expand(path), winslash, mustWork):
## path[1]=".\webshot10c065376aa1.png": O sistema não pode encontrar o
## arquivo especificado
{% endhighlight %}



{% highlight text %}
## Warning in file(con, "rb"): cannot open file 'C:\Users\Win7\AppData
## \Local\Temp\Rtmp4Gw60a\file10c06ad62084\webshot10c065376aa1.png': No
## such file or directory
{% endhighlight %}



{% highlight text %}
## Error in file(con, "rb"): cannot open the connection
{% endhighlight %}
Black Mirror apresenta uma variação considerável da avaliação dos episódios se comparada a série Sense8, além disso, esta também apresenta uma curva crescente.

Precisamente a avaliação dos episódios cresceu ou decresceu ao longo de cada temporada? 

{% highlight text %}
## Warning: Ignoring unknown aesthetics: text
{% endhighlight %}



{% highlight text %}
## Warning in normalizePath(path.expand(path), winslash, mustWork):
## path[1]=".\webshot10c045ff4eac.png": O sistema não pode encontrar o
## arquivo especificado
{% endhighlight %}



{% highlight text %}
## Warning in file(con, "rb"): cannot open file 'C:\Users\Win7\AppData
## \Local\Temp\Rtmp4Gw60a\file10c0287943a6\webshot10c045ff4eac.png': No
## such file or directory
{% endhighlight %}



{% highlight text %}
## Error in file(con, "rb"): cannot open the connection
{% endhighlight %}
Utilizando a mediana como métrica de comparação percebe-se claramente que Black Mirror mantém a avaliação dos episódios(variação em termos de décimos). Percebe-se também o crescimento da avaliação dos episódios de Sense8.

Podemos concluir que a avaliação dos episódios das novas temporadas de Black Mirror foi mantida enquanto a avaliação dos episódios da nova temporada de Sense8 foi acrescida.

Copyright © 2017 [Marcos Nascimento](http://https://github.com/marcosasn/AD1/tree/master/problema1/R)
