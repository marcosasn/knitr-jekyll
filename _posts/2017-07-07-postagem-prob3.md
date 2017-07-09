---
layout: post
title: "Tipos de filme de Lindsay Lohan"
date: 2017-07-09 08:09:01
published: true
tags: [htmlwidgets, r]
author: Marcos Antonio Silva Nascimento (marcos.nascimento@ccc.ufcg.edu.br)
---




{% highlight r %}
library(tidyverse, warn.conflicts = F)
{% endhighlight %}



{% highlight text %}
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
## Loading tidyverse: dplyr
{% endhighlight %}



{% highlight text %}
## Conflicts with tidy packages -----------------------------------------
{% endhighlight %}



{% highlight text %}
## filter(): dplyr, stats
## lag():    dplyr, stats
{% endhighlight %}



{% highlight r %}
library(rvest)
{% endhighlight %}



{% highlight text %}
## Loading required package: xml2
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'rvest'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:readr':
## 
##     guess_encoding
{% endhighlight %}



{% highlight r %}
library(plotly)
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'plotly'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:stats':
## 
##     filter
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:graphics':
## 
##     layout
{% endhighlight %}



{% highlight r %}
library(cluster)
library(ggdendro)
theme_set(theme_light())
source("plota_solucoes_hclust.R")

from_page <- read_html("https://www.rottentomatoes.com/celebrity/lindsay_lohan") %>% 
    html_node("#filmographyTbl") %>% # A sintaxe da expressão é de um seletor à lá JQuery: https://rdrr.io/cran/rvest/man/html_nodes.html 
    html_table(fill=TRUE) %>% # Faz parse
    as.tibble()

filmes = from_page %>% 
    filter(RATING != "No Score Yet", 
           `BOX OFFICE` != "—", 
            CREDIT != "Executive Producer") %>%
           mutate(RATING = as.numeric(gsub("%", "", RATING)), 
           `BOX OFFICE` = as.numeric(gsub("[$|M|k]", "", `BOX OFFICE`))) %>% 
    filter(`BOX OFFICE` != 'NA') # Tem sete filmes que não parecem ter sido lançados no mundo todo
{% endhighlight %}



{% highlight text %}
## Warning in evalq(as.numeric(gsub("[$|M|k]", "", c("$31.8k", "$32.1M",
## "$0.2M", : NAs introduced by coercion
{% endhighlight %}

# Tipos de filme de Lindsay Lohan
Neste post vamos investigar a existência de grupos de filmes com comportamentos comuns para os atuados pela atriz Lindsay Lohan. Previamente a atriz parece ter atuado em alguns filmes mais frequentemente quando fazia parte das estrelas Disney e nos dias atuais de forma mais esporádica. Será que existem grupos que definem comportamentos comuns para os filmes da atriz levando em consideração o sucesso de público e a crítica? Utilizaremos os dados cedidos pelo site Rotten Tomatoes.

Antes de mais nada precisamos descrever as variáveis que serão utilizadas para ver se já delineam a existência de grupos comuns de filmes. 

## A descrição
Vamos primeiramente olhar para o gráfico abaixo, veja como se comporta a distribuição de avaliação de cada filme da atriz.

{% highlight r %}
dist_avaliacao = filmes %>% 
  ggplot(aes(x = "Filmes", y = RATING, label = TITLE)) + 
    geom_jitter(width = .01, height = 0, size = 2, alpha = .6, color = "red") +
  labs(title = "Distribuição da avaliação do filme", x="Filmes", y="Avaliação") +
  theme(plot.title = element_text(hjust = 0.5))

ggplotly(dist_avaliacao)
{% endhighlight %}

![plot of chunk unnamed-chunk-1](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-1-1.png)

Observando as avaliações dos filmes podemos observar uma tendência de grupos. Esses grupos ficam ainda mais evidentes quando geramos o gráfico de contagem de frequência de cada avaliação (vide abaixo). As avaliações mais próximas poderiam sugerir os grupos e os gaps os delimitadores entre cada um deles.

{% highlight r %}
filmes %>% 
    ggplot(aes(x = RATING)) + 
    geom_histogram(bins = 16, fill = "red", color = "black") + 
    geom_rug() +
   labs(title = "Histograma da avaliação", x="Avaliação", y = "Frequência") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-2-1.png)

Veja como se comporta a distribuição da renda de cada filme da atriz.

{% highlight r %}
dist_renda = filmes %>% 
    ggplot(aes(x = "Filmes", y = `BOX OFFICE`, label = TITLE)) + 
    geom_jitter(width = .02, height = 0, size = 2, alpha = .6, color = "red") +
  labs(title = "Distribuição da bilheteria", x="Filmes", y="Bilheteria") +
  theme(plot.title = element_text(hjust = 0.5))
ggplotly(dist_renda)
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-3-1.png)

Podemos ver uma estrutura de grupo de filmes bem próximos quanto ao sucesso de público. O maior grupo define os filmes com bilheteria inferior a 40 milhões de dólares. Ainda sobre a bilheteria podemos observar se os valores de bilheteria são frequentes.

{% highlight r %}
filmes %>% 
    ggplot(aes(x = `BOX OFFICE`)) + 
    geom_histogram(bins = 20, fill = "red", color = "black") + 
    geom_rug() +
  labs(title = "Histograma da bilheteria", x="Bilheteria", y = "Frequência") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-4-1.png)

Quanto a frequência de bilheterias comuns observamos que a maioria dos valores de bilheteria são distintos e existem poucos valores duplicados. Poderia ser interessante analisar o comportamento da distribuição de bilheteria dos filmes em uma outra escala já que a maior concentração de valores de bilheteria impede uma melhor visualização da real magnitude dos valores.

{% highlight r %}
dist_renda_logscale = filmes %>% 
    ggplot(aes(x = "Filmes", y = `BOX OFFICE`, label=TITLE)) + 
    geom_jitter(width = .02, height = 0, size = 2, alpha = .6, color = "red") + 
    scale_y_log10() +
  labs(title = "Distribuição da bilheteria", x="Filmes", y="Bilheteria") +
  theme(plot.title = element_text(hjust = 0.5))
ggplotly(dist_renda_logscale)
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-5-1.png)

Um valor de bilheteria que anteriormente parecia fazer parte de um grupo majoritário de valores contém o menor valor de bilheteria. Isso fica ainda mais evidente se olharmos o gráfico de histograma abaixo para a contagem de frequência de bilheteria de cada filme na escala logarítimica.

{% highlight r %}
filmes %>% 
    ggplot(aes(x = `BOX OFFICE`)) + 
    geom_histogram(bins = 20, fill = "red", color = "black") + 
    scale_x_log10() + 
    geom_rug() +
  labs(title = "Histograma da bilheteria", x="Bilheteria", y = "Frequência") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-6-1.png)

# Agrupamento com duas dimensões

## Sem normalização e sem transformação

Podemos perceber pela descrição das variáveis que a crítica e a bilheteria já tendenciam a estrutura de grupos mas como ficaria o gráfico de distribuição dessas duas variáveis?

{% highlight r %}
dist_avaliacao_renda = filmes %>% 
    ggplot(aes(x = RATING, y = `BOX OFFICE`, label = TITLE)) + 
    geom_point(color="red") +
  labs(title = "Distribuição da avaliação e da bilheteria", x="Avaliação", y="Bilheteria") +
  theme(plot.title = element_text(hjust = 0.5))
ggplotly(dist_avaliacao_renda)
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-7-1.png)

Quais seriam os possíveis grupos para esta distribuição bidimensional?

{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>%
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")
{% endhighlight %}



{% highlight text %}
## Warning: Setting row names on a tibble is deprecated.
{% endhighlight %}



{% highlight r %}
ggdendrogram(agrupamento_h_2d, rotate = TRUE) +
  geom_hline(yintercept = 30, colour = "red")
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-8-1.png)

Observando o gráfico do dendrograma acima fica apropriado escolher 4 grupos já que os mesmos vão conter filmes menos distintos e mais característicos daquele grupo no qual estão inseridos. Como ficariam dispostos os nossos grupos de filmes?

{% highlight r %}
plota_hclusts_2d(agrupamento_h_2d, 
                 filmes, 
                 c("RATING", "`BOX OFFICE`"), 
                 linkage_method = "centroid", ks = 1:6) %>% ggplotly()
{% endhighlight %}



{% highlight text %}
## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to
## character
{% endhighlight %}

![plot of chunk unnamed-chunk-9](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-9-1.png)

Escolhendo 4 grupos esta escolha agruparia filmes característicos do grupo no qual estariam inseridos?

{% highlight r %}
distancias = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>% 
    dist(method = "euclidean")
{% endhighlight %}



{% highlight text %}
## Warning: Setting row names on a tibble is deprecated.
{% endhighlight %}



{% highlight r %}
plot(silhouette(cutree(agrupamento_h_2d, k = 4), distancias))
{% endhighlight %}

![plot of chunk unnamed-chunk-10](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-10-1.png)

Observando o gráfico acima parece que sim porque cada grupo contém filmes característicos daquele grupo.

## Com normalização e com transformação
Na seção anterior não fizemos normalização ou padronização dos dados antes de reuni-los em clusters, isto seria adequado? Seria certo ou justo comparar variáveis de escalas divergentes e afirmar algo sensato? Você certamente deve estar ciente que não.

A técnica de transformação não foi aplicada na seção anterior. Seria apropriado afirmar algo sobre grupos de valores em um gráfico sem observar como o mesmo se comporta em uma outra escala? A resposta mais sensata também seria não. Na seção de descrição das variáveis notamos que um filme que teve a pior bilheteria não foi distinto de um grupo com valores de bilheterias maiores. Como levar isso em consideração? 

Veja como fica o gráfico de distribuição da crítica e da renda quando observamos a renda na escala logarítimica.

{% highlight r %}
dist_avaliacao_renda = filmes %>% 
    ggplot(aes(x = RATING, y = `BOX OFFICE`, label = TITLE)) + 
    geom_point(color="red") +
  scale_y_log10() +
  labs(title = "Distribuição da avaliação e da bilheteria", x="Avaliação", y="Bilheteria") + 
  theme(plot.title = element_text(hjust = 0.5))
ggplotly(dist_avaliacao_renda)
{% endhighlight %}

![plot of chunk unnamed-chunk-11](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-11-1.png)

Quais seriam os possíveis grupos para esta distribuição bidimensional se as variáveis fossem normalizadas e o eixo de bilheteria fosse transformado para a escala logarítimica?

{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>% 
    mutate(`BOX OFFICE` = log10(`BOX OFFICE`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")
{% endhighlight %}



{% highlight text %}
## Warning: Setting row names on a tibble is deprecated.
{% endhighlight %}



{% highlight r %}
ggdendrogram(agrupamento_h_2d, rotate = TRUE)
{% endhighlight %}

![plot of chunk unnamed-chunk-12](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-12-1.png)

{% highlight r %}
filmes2 = filmes %>% mutate(`BOX OFFICE` = log10(`BOX OFFICE`))
ggplotly(plota_hclusts_2d(agrupamento_h_2d, 
                 filmes2, 
                 c("RATING", "`BOX OFFICE`"), 
                 linkage_method = "ward.D", ks = 1:6) + scale_y_log10())
{% endhighlight %}



{% highlight text %}
## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to
## character
{% endhighlight %}

![plot of chunk unnamed-chunk-12](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-12-2.png)

Escolhendo 4 grupos esta escolha ainda agruparia filmes característicos do grupo no qual estariam inseridos?

{% highlight r %}
distancias = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>% 
    mutate(`BOX OFFICE` = log10(`BOX OFFICE`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean")

plot(silhouette(cutree(agrupamento_h_2d, k = 4), distancias))
{% endhighlight %}

![plot of chunk unnamed-chunk-13](/knitr-jekyll-ad1figure/source/2017-07-07-postagem-prob3/unnamed-chunk-13-1.png)

Observando o gráfico acima parece que esta escolha agrupa um filme em um agrupamento que não lhe caracteriza tão bem embora isso não vá fazer tanta diferença e impactar tanto na análise.

# Conclusão
O ideal é realizar a normalização com transformação da variável bilheteria. Uma escolha dos grupos poderia ser 4. Esta escolha agruparia os filmes da atriz sem prejuízo de informação. Os nomes para os grupos seriam: os **piores filmes**, os **filmes medianos**, os **filmes que você deveria ver** e os **melhores filmes**.

Os **piores filmes** são aqueles que não fizeram sucesso de público e nem foram bem avaliados (por ex. InAPPropriate Comedy). Os **filmes medianos** são aqueles que tiveram um sucesso considerável de público e ainda assim não foram bem avaliados (por ex. The Canyons). Os **filmes que você deveria ver** aqueles filmes que foram bem avaliados mas não tiveram sucesso de público (por ex. Love, Marilyn) e os **melhores filmes** aqueles que tiveram sucesso de público e de avaliação (por ex. Mean Girls).

Copyright © 2017 [Marcos Nascimento](https://github.com/marcosasn/AD1/blob/master/problema3/R/prob3_cpoint1.Rmd)
