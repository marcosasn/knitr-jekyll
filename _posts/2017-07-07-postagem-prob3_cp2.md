---
layout: post
title: "Tipos de filme de Hollywood quanto ao gênero"
author: Marcos Antonio Silva Nascimento (marcos.nascimento@ccc.ufcg.edu.br)
date: 2017-07-09 10:21:37
published: true
tags: [htmlwidgets, r]
---




{% highlight r %}
require(GGally, quietly = TRUE)
require(reshape2, quietly = TRUE)
require(tidyverse, quietly = TRUE, warn.conflicts = FALSE)
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
library(ggfortify)
{% endhighlight %}



{% highlight text %}
## Loading required package: methods
{% endhighlight %}



{% highlight r %}
library(cluster)
library(ggdendro)
library(broom)
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
library(readr)
library(magrittr)
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'magrittr'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:purrr':
## 
##     set_names
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:tidyr':
## 
##     extract
{% endhighlight %}



{% highlight r %}
theme_set(theme_bw())
{% endhighlight %}
# Tipos de filme quanto ao gênero do personagem e da quantidade de palavras que eles falam 

Neste post vamos investigar a existência de tipos de filmes quanto ao gênero do personagem e da quantidade de palavras que ele fala. Esta investigação vai ajudar as pessoas a se confrontarem com o que se conhece popularmente a respeito de filmes voltados para o público feminino e os filmes do gênero de terror, por exemplo. Será que os filmes femininos o número de personagens é predominantemente feito de mulheres? Será que elas falam em maior quantidade que os homens? Será que existem grupos que definem comportamentos comuns para os filmes analisados? Utilizaremos os dados cedidos pelo Github. [Github](https://github.com/matthewfdaniels/scripts).


{% highlight r %}
personagens = read_csv(file = "dados/character_list5.csv")
{% endhighlight %}



{% highlight text %}
## Parsed with column specification:
## cols(
##   script_id = col_integer(),
##   imdb_character_name = col_character(),
##   words = col_integer(),
##   gender = col_character(),
##   age = col_character()
## )
{% endhighlight %}



{% highlight r %}
personagens = personagens %>%
  filter(age != 'NULL') %>% 
  mutate(age = as.numeric(age))

filmes = read.csv(file = "dados/meta_data7.csv")
filmes = filmes %>%
  filter(gross != 'NA', gross > 0)

filmes_personagens = merge(filmes, personagens, by="script_id")

mulheres = filmes_personagens %>%
  filter(gender == 'f') %>%
  group_by(script_id, imdb_id, title, year, gross) %>%
  summarise(n_f=n(), words_f=median(words)) %>%
  filter(n_f > 1)

homens = filmes_personagens %>%
  filter(gender == 'm') %>%
  group_by(script_id, imdb_id, title, year, gross) %>%
  summarise(n_m=n(), words_m=median(words)) %>%
  filter(n_m > 1)

dados = merge(mulheres, homens, 
                           by=c('script_id','imdb_id','title','year','gross'))
duplicados = dados %>%
  group_by(title) %>% filter(row_number() > 1)

dados = dados %>% 
  filter(!(title %in% duplicados$title))
  
dados = dados %>%
  subset(select = -c(script_id,imdb_id,year,gross))
{% endhighlight %}
## Decisões sobre filtrar dados ou variáveis 

Observando os dados cedidos pelo repositório pude notar que o valor da variável idade, da tabela de personagens, não estava disponível ou continha valor nulo. Desta forma foi feita a filtragem dessas observações. A variável renda da tabela dos filmes tinha comportamento semelhente. Algumas observações continha valor não disponível ou então igual a zero, desta forma, eu achei que seria prudente filtra-los uma vez que, filmes sem valor de renda ou com valor de renda igual a zero não seriam relavantes na análise.

Uma limitação encontrada durante a análise foi o fato de alguns filmes possuirem o mesmo nome embora fossem diferentes então para submeter os dados para a análise eu tive que fazer a filtragem desses filmes com nomes repetidos também.

Esta análise só levará em consideração os filmes que contenham mais de um personagem de cada gênero.

## As dimensões submetidas a análise 

As dimensões submetidas a análise foram 4 variáveis numéricas calculadas a partir do conjunto de dados cedido pelo Github mencionado acima. São elas: **nº de personagens do sexo feminino no filme**, **mediana de palavras dos personagens do sexo feminino no filme**, **nº de personagens do sexo masculino no filme** e **mediana de palavras dos personagens do sexo masculino no filme**.

O conjunto de dados submetido a análise contém, para cada filme, uma observação com valores para cada variável mencionada acima. A escolha das variáveis acima visava obter a resposta para a seguinte pergunta: visando o gênero do personagem e a quantidade de palavras ditas por ele em um filme, quais os tipos de filmes? Filmes em que as mulheres são protagonistas? Filmes em que os homens são protagonistas?

## Sumário e descrição dos dados 

Vamos primeiramente olhar para o gráfico abaixo, veja como se comporta a distribuição de cada dimensão dos dados.

### Dados brutos

{% highlight r %}
dw = dados

dw %>% 
  select(-title) %>% 
   ggpairs(columnLabels = c("Nº mulheres",
                           "Palavras mulheres",
                           "Nº homens",
                           "Palavras homens"),
          title = "Distribuição e correlação das dimensões")+
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-2-1.png)

Na diagonal do gráfico acima podemos observar a distribuição de cada uma das dimensões submetidas a análise. De primeira já podemos observar um viesamento dos dados a esquerda. Isto impede ver melhor a magnitude dos valores porque eles acabam se concentrando à esquerda do gráfico. De ante mão já podemos identificar que o número de homens nos filmes é ligeiramente maior que o número de mulheres. Isto fica ainda mais evidente no sumário dos dados abaixo, em termos de mediana, o número de homens nos filmes é duas vezes maior que o número de mulheres.

Embora possa ser contraditório, a mediana de palavras ditas pelas mulheres é um pouco superior a mediana de palavras ditas pelos homens. Isto fica ainda mais evidente no sumário abaixo.

Ainda sobre o gráfico a cima podemos observar que a medida que o número de mulheres ou homens presentes nos filmes aumenta eles tendem a falar menos em termos de mediana. No que diz respeito a correlação não podemos observar nenhuma correlação forte positiva ou negativa entre cada uma das dimensões analisadas.

{% highlight r %}
summary(select(dw, -title))
{% endhighlight %}



{% highlight text %}
##       n_f            words_f            n_m            words_m      
##  Min.   : 2.000   Min.   : 103.0   Min.   : 2.000   Min.   : 125.5  
##  1st Qu.: 2.000   1st Qu.: 335.5   1st Qu.: 4.000   1st Qu.: 346.0  
##  Median : 3.000   Median : 541.8   Median : 6.000   Median : 529.2  
##  Mean   : 3.442   Mean   : 764.4   Mean   : 6.635   Mean   : 722.0  
##  3rd Qu.: 4.000   3rd Qu.: 924.5   3rd Qu.: 8.000   3rd Qu.: 882.0  
##  Max.   :14.000   Max.   :7664.0   Max.   :23.000   Max.   :5240.0
{% endhighlight %}
### Dados em escala de log

Como foi dito na seção anterior, é aconselhável observar a distruibuição de cada uma das dimensões na escala logarítmica para observar melhor a magnitude dos valores que se enviesam ou se concentram à esquerda do gráfico.

{% highlight r %}
# Escala de log 
dw2 <- dw %>% 
    mutate_each(funs(log), 2:5)

dw2 %>% 
    select(-title) %>% 
    ggpairs(columnLabels = c("Nº mulheres",
                           "Palavras mulheres",
                           "Nº homens",
                           "Palavras homens"),
          title = "Distribuição e correlação das dimensões")+
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-4-1.png)

As conclusões sobre a figura acima são quase as mesmas para a figura da seção anterior mas a atenção se volta parar o gráfico de mediana de palavras das mulheres e a mediana de palavras dos homens. Na seção anterior percebemos que as mulheres falavam mais palavras do que os homens apesar de o número de mulheres ser duas vezes menor do que o número de homens, neste gráfico vemos que esta diferença é quase imperceptível. Isto fica ainda mais evidente no sumário de dados abaixo.


{% highlight r %}
summary(select(dw2, -title))
{% endhighlight %}



{% highlight text %}
##       n_f            words_f           n_m            words_m     
##  Min.   :0.6931   Min.   :4.635   Min.   :0.6931   Min.   :4.832  
##  1st Qu.:0.6931   1st Qu.:5.816   1st Qu.:1.3863   1st Qu.:5.846  
##  Median :1.0986   Median :6.295   Median :1.7918   Median :6.271  
##  Mean   :1.1449   Mean   :6.343   Mean   :1.7777   Mean   :6.339  
##  3rd Qu.:1.3863   3rd Qu.:6.829   3rd Qu.:2.0794   3rd Qu.:6.782  
##  Max.   :2.6391   Max.   :8.944   Max.   :3.1355   Max.   :8.564
{% endhighlight %}

### Dados padronizados

Depois de analisar os dados brutos e na escala logarítmica é chegada a hora de ver como se comporta a distribuição dos dados padronizados ou normalizados.

{% highlight r %}
dw2.scaled = dw2 %>% 
  mutate_each(funs(as.vector(scale(.))), 2:5)

dw2.scaled %>% 
    select(-title) %>% 
     ggpairs(columnLabels = c("Nº mulheres",
                           "Palavras mulheres",
                           "Nº homens",
                           "Palavras homens"),
          title = "Distribuição e correlação das dimensões")+
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-6-1.png)

O comportamento da distribuição de cada uma dimensão agora está em torno da distribuição normal com média zero. Como podemos observar nos gráficos acima e no sumário abaixo a média de cada dimensão é zero (própria da distribuição normal com média zero) e os demais valores são distanciamentos da média. Isto facilitará a análise dos gráficos de agrupamento nas seções posteriores.

Uma vez normalizados ou padronizados os dados podem ser utilizados no processo de agrupamento de forma igualitária ou justa (estaremos agrupando dados ou variáveis na mesma escala).

{% highlight r %}
summary(select(dw2.scaled, -title))
{% endhighlight %}



{% highlight text %}
##       n_f             words_f              n_m          
##  Min.   :-1.0954   Min.   :-2.30107   Min.   :-2.21744  
##  1st Qu.:-1.0954   1st Qu.:-0.71042   1st Qu.:-0.80027  
##  Median :-0.1121   Median :-0.06497   Median : 0.02872  
##  Mean   : 0.0000   Mean   : 0.00000   Mean   : 0.00000  
##  3rd Qu.: 0.5855   3rd Qu.: 0.65493   3rd Qu.: 0.61689  
##  Max.   : 3.6234   Max.   : 3.50387   Max.   : 2.77604  
##     words_m       
##  Min.   :-2.2575  
##  1st Qu.:-0.7383  
##  Median :-0.1017  
##  Mean   : 0.0000  
##  3rd Qu.: 0.6634  
##  Max.   : 3.3327
{% endhighlight %}

# O agrupamento multidimensional utilizado o algoritmo k-means

## Escolhendo o valor de k

Antes de realizar o agrupamento precisamos escolher um bom valor para k (basicamente indica o número de grupos ou tipos que iremos identificar no conjunto de dados). Uma medida comumente usada no k-means é comparar a distância (quadrática) entre o centro dos clusters e o centro dos dados com a distância (quadrática) entre todos os pontos nos dados e o centro dos dados.

Aqui o centro dos dados é um ponto imaginário na média de todas as variáveis. Calculamos a distância do centro de cada cluster para o centro dos dados e multiplicamos pelo número de pontos nesse cluster. Somando esse valor para todos os clusters, temos betweenss abaixo. Se esse valor for próximo do somatório total das distâncias dos pontos para o centro dos dados (totss), os pontos estão próximos do centro de seu cluster. Essa proporção pode ser usada para definir um bom valor de k. Quando ela para de crescer, para de valer à pena aumentar k.

{% highlight r %}
dists = dw2.scaled %>%
      column_to_rownames("title") %>% 
    dist(method = "euclidean")

hc = hclust(dists, method = "ward.D")

n_clusters = 4

dw2 <- dw2 %>% 
    mutate(cluster = hc %>% 
               cutree(k = n_clusters) %>% 
               as.character())

dw2.scaled <- dw2.scaled %>% 
    mutate(cluster = hc %>% 
               cutree(k = n_clusters) %>% 
               as.character())

dw2.long = melt(dw2.scaled, id.vars = c("title", "cluster"))

dw2.scaled = dw2.scaled %>% 
    select(-cluster) # Remove o cluster adicionado antes l? em cima via hclust

set.seed(123)
explorando_k = tibble(k = 1:15) %>% 
    group_by(k) %>% 
    do(
        kmeans(select(dw2.scaled, -title), 
               centers = .$k, 
               nstart = 20) %>% glance()
    )
{% endhighlight %}



{% highlight text %}
## Warning: did not converge in 10 iterations
{% endhighlight %}



{% highlight r %}
explorando_k %>% 
    ggplot(aes(x = k, y = betweenss / totss)) + 
    geom_line() + 
    geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-8-1.png)

Observando o gráfico acima fica fácil perceber que o melhor valor de k seria 4, já que, apartir de 4 betweenss começa a parar de crescer. O ponto k=4 é também conhecido como joelho da curva.

## Agrupando os dados

{% highlight r %}
# O agrupamento de fato:
km = dw2.scaled %>% 
    select(-title) %>% 
    kmeans(centers = n_clusters, nstart = 20)

# O df em formato longo, para visualiza??o 
dw2.scaled.km.long = km %>% 
    augment(dw2.scaled) %>% # Adiciona o resultado de km 
                            # aos dados originais dw2.scaled em 
                            # uma vari?vel chamada .cluster
    gather(key = "variável", 
           value = "valor", 
           -title, -.cluster) # = move para long todas as 
                                            # vari?vies menos title 
                                            # e .cluster
dw2.scaled.km.long %>% 
    ggplot(aes(x = `variável`, y = valor, group = title, colour = .cluster)) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ .cluster) +
    xlab("Variável") + 
    ylab("Valor") +
    ggtitle("Gráfico de coordenadas paralelas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-9](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-9-1.png)

{% highlight r %}
#autoplot(km, data = dw2.scaled, label = TRUE)

dists = dw2.scaled %>% 
    select(-title) %>% 
    dist() # s? para plotar silhouetas depois
#plot(silhouette(km$cluster, dists), col = RColorBrewer::brewer.pal(n_clusters, "Set2"))
{% endhighlight %}

Observando o gráfico acima e olhando a direção em que as linhas dos filmes cruzam e tocam cada uma das variáveis ou coordenadas podemos observar grupos que caracterizam os filmes que ali cabem.

## Descrição e interpretação dos grupos

Observando os agrupamentos do gráfico de coordenadas paralelas acima vamos interpretar cada um deles logo abaixo.

{% highlight r %}
dw2.scaled.km.long %>% 
  filter(.cluster == 1) %>%
    ggplot(aes(x = `variável`, y = valor, group = title, colour = .cluster)) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ .cluster) +
    xlab("Variável") + 
    ylab("Valor") +
    ggtitle("Gráfico de coordenadas paralelas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-10](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-10-1.png)

O grupo 1 é caracterizado por conter mais personagens do sexo masculino do que personagens do sexo feminino embora a quantidade de palavras ditas pelas mulheres seja maior do que a quantidade de palavras ditas por homens. Estes filmes poderiam fazer referência aqueles voltados para o público feminino já que as mulheres parecem protagonizar cada um deles. Um nome para este grupo seria **filmes femininos**, como exemplo posso citar o filme Pretty Woman.


{% highlight r %}
dw2.scaled.km.long %>% 
  filter(.cluster == 2) %>%
    ggplot(aes(x = `variável`, y = valor, group = title, colour = .cluster)) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ .cluster) +
    xlab("Variável") + 
    ylab("Valor") +
    ggtitle("Gráfico de coordenadas paralelas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-11](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-11-1.png)

O grupo 2 é caracterizado por conter mais personagens do sexo feminino do que personagens do sexo masculino embora os personagens do sexo masculino falem mais do que os personagens do sexo feminino. Estes filmes poderiam fazer referência aqueles voltados para o público masculino já os homens parecem protagonizar cada um deles. Um nome para este grupo seria **filmes masculinos**, como exemplo posso citar o filme Angels & Demons.


{% highlight r %}
dw2.scaled.km.long %>% 
  filter(.cluster == 3) %>%
    ggplot(aes(x = `variável`, y = valor, group = title, colour = .cluster)) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ .cluster) +
    xlab("Variável") + 
    ylab("Valor") +
    ggtitle("Gráfico de coordenadas paralelas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-12](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-12-1.png)

O grupo 3 é caracterizado por conter mais personagens do sexo feminino do que por personagens de sexo masculino embora ambos os sexos falem a mesma quantidade de palavras. Estes filmes poderiam fazer referência aqueles que buscam evidenciar a imagem e o poder da mulher já que as mulheres parecem ser maioria em cada um deles. Um nome para este grupo seria **filmes feministas**, como exemplo posso citar o filme Final Destination 2.


{% highlight r %}
dw2.scaled.km.long %>% 
  filter(.cluster == 4) %>%
    ggplot(aes(x = `variável`, y = valor, group = title, colour = .cluster)) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ .cluster) +
    xlab("Variável") + 
    ylab("Valor") +
    ggtitle("Gráfico de coordenadas paralelas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-13](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-13-1.png)

O grupo 4 é caracterizado por conter mais personagens do sexo masculino do que personagens do sexo feminino. Neste grupo os homens falaram maiores quantidades de palavras do que as mulheres. Estes filmes poderiam fazer referência aqueles que buscam evidenciar a imagem e o poder do homem. Um nome para este grupo seria **filmes machistas**, como exemplo posso citar o filme Freddy vs. Jason.

Por fim podemos observar como fica a disposição de todos os gráficos de coordenadas paralelas para todos os grupos um sobre o outro.

{% highlight r %}
p <- km %>% 
    augment(dw2.scaled) %>%
    plot_ly(type = 'parcoords',
            line = list(color = ~.cluster, 
                        showScale = TRUE),
            dimensions = list(
                #list(range = c(1, 4), label = "cluster", values = ~cluster),
                list(range = c(-3, 3),
                     label = "N? mulheres", values = ~n_f),
                list(range = c(-3, 3),
                     label = "Palavras mulheres", values = ~words_f),
                list(range = c(-6, 3),
                     label = "N? homens", values = ~n_m),
                list(range = c(-2, 3),
                     label = "Palavras homens", values = ~words_m)
            )
    )
p
{% endhighlight %}

![plot of chunk unnamed-chunk-14](/knitr-jekyll-ad1/figure/source/2017-07-07-postagem-prob3_cp2/unnamed-chunk-14-1.png)

Ainda é possível interagir com o mesmo mudando a disposição dos eixos e dos grupos que são apresentados.

Copyright © 2017 [Marcos Nascimento](https://github.com/marcosasn/AD1/blob/master/problema3/R/prob3_cpoint2.Rmd)
