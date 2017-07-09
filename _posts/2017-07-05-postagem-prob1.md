---
layout: post
title: "Descrevendo e explorando Sense8, Black Mirror e 13 Reasons Why"
date: 2017-07-09 10:19:12
published: true
tags: [htmlwidgets, r]
author: Marcos Antonio Silva Nascimento (marcos.nascimento@ccc.ufcg.edu.br)
---


{% highlight r %}
library("dplyr")
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'dplyr'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:stats':
## 
##     filter, lag
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
{% endhighlight %}



{% highlight r %}
library("tidyr")
library("ggplot2")
library("readr")

dados = read_csv(file = "dados/series_from_imdb.csv")
{% endhighlight %}



{% highlight text %}
## Parsed with column specification:
## cols(
##   series_name = col_character(),
##   series_ep = col_integer(),
##   season = col_integer(),
##   url = col_character(),
##   Episode = col_character(),
##   UserRating = col_double(),
##   UserVotes = col_double(),
##   r1 = col_double(),
##   r10 = col_double(),
##   r2 = col_double(),
##   r3 = col_double(),
##   r4 = col_double(),
##   r5 = col_double(),
##   r6 = col_double(),
##   r7 = col_double(),
##   r8 = col_double(),
##   r9 = col_double(),
##   season_ep = col_integer()
## )
{% endhighlight %}



{% highlight r %}
dados = dados %>% filter(series_name %in% c("13 Reasons Why", "Sense8", "Black Mirror"))
{% endhighlight %}
# Sense8
**Descricao da variavel classificacao do usuario (UserRating)**

{% highlight r %}
dados %>%
  filter(series_name == "Sense8") %>%
    ggplot(aes(x = series_name, y = UserRating)) + 
    geom_jitter(width = .1, color = "red") +
  labs(title = "Distribuicao da classificacao do usuario", x="Nome da serie", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-2-1.png)

Podemos perceber que segundo a distribuicao da classificacao de usuarios no grafico acima, Sense8 tem apenas um valor estranho(~7.5). Existe variacao e entre os valores frequentes tem-se a classificacao 9. Isto fica claro se observarmos o histograma logo abaixo com a contagem de frequencia das classificacoes.

{% highlight r %}
dados %>%
  filter(series_name == "Sense8") %>%
    ggplot(aes(x = UserRating)) + 
    geom_histogram(binwidth = .5, fill = "red", color = "black") + 
    geom_rug() +
  labs(title = "Histograma da classificacao do usuario", x="Classificacao do usuario", y = "Frequencia")
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-3-1.png)

Como a frequencia da classificacao 9 e maior entao espera-se que a mediana(9) e a media(8.891304) estejam neste entorno.

{% highlight r %}
median((dados %>%  filter(series_name == "Sense8"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 9
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "Sense8"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 8.873913
{% endhighlight %}
Podemos tambem observar, no grafico abaixo, a distribuicao de classificacao por temporada. A segunda temporada e melhor classificada se comparada a primeira temporada, em termos de mediana, e a variacao da classificacao da primeira foi maior do que na segunda.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
    ggplot(aes(x = as.character(season), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot da classificacao do usuario por temporada da serie", x="Temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-5-1.png)

Com o passar dos episodios da primeira temporada a classificacao so subiu. A classificacao da segunda temporada teve o mesmo comportamento estando com a classificacao ainda superior a da primeira temporada como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point()+
  scale_x_continuous(breaks=seq(1, 12, 1)) +
  labs(title = "Distribuicao da classificacao ao longo das temporadas", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-6-1.png)

Podemos ainda verificar a existencia de uma correlacao entre o episodio da temporada e sua classificacao e podemos perceber que a mesma existe. Existe uma correlacao linear, aparentemente forte, entre essas duas variaveis, ou seja, o episodio da temporada influencia diretamente na classificacao do episodio e virse e versa.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserRating, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserRating, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1         0.8277839          0.7385489
## 2      2         0.8255312          0.6617241
{% endhighlight %}
**Descricao da variavel votos dos usuarios (UserVotes)**


{% highlight r %}
dados %>%
  filter(series_name == "Sense8") %>%
    ggplot(aes(x = series_name, y = UserVotes)) + 
    geom_jitter(width = .1, color = "red") +
  labs(title = "Distribuicao de votos do usuario", x="Nome da serie", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-8-1.png)

Podemos perceber que segundo a distribuicao de votos dos usuarios para cada episodio da serie no grafico acima, Sense8 tem dois grupos de valores distintos(votos < 1000 e votos >= 2000). Quanto a mediana temos 2047 e a media 1562.043. O valor indicado para media e pouco represetativo da distribuicao de votos por causa dos grupos de valores dispersos.

{% highlight r %}
median((dados %>%  filter(series_name == "Sense8"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 2090
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "Sense8"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 1669.565
{% endhighlight %}
Podemos tambem observar no grafico abaixo, a distribuicao de votos por temporada. A primeira temporada e melhor votada se comparada a segunda temporada, em termos de mediana, e a variacao de votos da primeira foi maior do que na segunda.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
    ggplot(aes(x = as.character(season), y = UserVotes)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot de votos do usuario por temporada da serie", x="Temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-10](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-10-1.png)

Com o passar dos episodios da primeira temporada o numero de votos so diminuiu. A primeira temporada teve seu pico de votos no primeiro episodio. O numero de votos da segunda temporada teve o mesmo comportamento estando com o numero de votos ainda inferior a da primeira temporada como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserVotes, color = season)) + 
  geom_line() + 
  geom_point()+
  scale_x_continuous(breaks=seq(1, 12, 1)) +
  labs(title = "Distribuicao de votos ao longo das temporadas", x="Episodio da temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-11](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-11-1.png)

Podemos ainda verificar a existencia de uma fraca correlacao negativa entre o episodio da temporada e seu numero de votos.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserVotes, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserVotes, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1        -0.3881809         -0.2727273
## 2      2        -0.5337060         -0.4181818
{% endhighlight %}
# Black Mirror
**Descricao da variavel classificacao do usuario (UserRating)**

{% highlight r %}
dados %>%
  filter(series_name == "Black Mirror") %>%
    ggplot(aes(x = series_name, y = UserRating)) + 
    geom_jitter(width = .1, color = "red") +
  labs(title = "Distribuicao da classificacao do usuario", x="Nome da serie", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-13](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-13-1.png)

Podemos perceber que segundo a classificacao de usuarios IMDB acima, Black Mirror tem apenas um valor estranho(~7.0). Existe variacao e dentre os valores frequentes temos as classificacoes de 8.0 e 8.5. Isto fica claro se observar-mos o histograma abaixo.

{% highlight r %}
dados %>%
  filter(series_name == "Black Mirror") %>%
    ggplot(aes(x = UserRating)) + 
    geom_histogram(binwidth = .5, fill = "red", color = "black") + 
    geom_rug() +
  labs(title = "Histograma da classificacao do usuario", x="Classificacao do usuario", y = "Frequencia")
{% endhighlight %}

![plot of chunk unnamed-chunk-14](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-14-1.png)

Como a frequencia da classificacao concentra-se em 8.0 e 8.5 entao espera-se que a mediana esteja neste entorno. Neste caso, houve a coincidencia da mediana e da media(8.3).

{% highlight r %}
median((dados %>%  filter(series_name == "Black Mirror"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 8.3
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "Black Mirror"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 8.307692
{% endhighlight %}
Podemos tambem observar a distribuicao de classificacao por temporada de Black Mirror. A terceira temporada e melhor classificada se comparada a primeira e a segunda temporada, em termos de mediana, e a variacao da classificacao da segunda temporada foi maior do que na primeira e terceira. Pode-se observar tambem que a temporada com menor classificacao dentre as tres foi a segunda temporada como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
    ggplot(aes(x = as.character(season), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot da classificacao do usuario por temporada da serie", x="Temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-16](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-16-1.png)

Com o passar dos episodios de Black Mirror a unica temporada que a classificacao so subiu foi a primeira, enquanto que na segunda e terceira temporada vemos uma variacao da classificacao. Dentre as variacoes a temporada que mais teve queda na classificacao sendo a segunda temporada (o episodio 3 chegou a ter a classificacao mais baixa da serie) como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point() +
labs(title = "Distribuicao da classificacao ao longo das temporadas", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-17](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-17-1.png)

Quanto a correlacao entre o episodio da temporada e sua classificacao podemos perceber a existencia de uma correlacao linear, aparentemente forte, entre essas duas variaveis apenas na primeira temporada.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserRating, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserRating, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1         0.9819805          1.0000000
## 2      2         0.2247333          0.1825742
## 3      3         0.2236068          0.2000000
{% endhighlight %}
**Descricao da variavel votos dos usuarios (UserVotes)**

{% highlight r %}
dados %>%
  filter(series_name == "Black Mirror") %>%
    ggplot(aes(x = series_name, y = UserVotes)) + 
    geom_jitter(width = .1, color = "red") +
  labs(title = "Distribuicao de votos do usuario", x="Nome da serie", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-19](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-19-1.png)

Podemos perceber que segundo a distribuicao de votos dos usuarios do IMDB acima, Black Mirror tem votos dispersos. Em termos de media temos 12336.62 e de mediana 11844.

{% highlight r %}
median((dados %>%  filter(series_name == "Black Mirror"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 12024
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "Black Mirror"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 12519.85
{% endhighlight %}
Podemos tambem observar a distribuicao de votos por temporada de Black Mirror. A primeira temporada e melhor votada se comparada a segunda e a terceira temporada, em termos de mediana, e a variacao da votos da terceira temporada foi maior do que na primeira e segunda. Pode-se observar tambem que ocorre um empate entre a mediana de votos da segunda e da terceira temporada como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
    ggplot(aes(x = as.character(season), y = UserVotes)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot de votos do usuario por temporada da serie", x="Temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-21](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-21-1.png)

Com o passar dos episodios de Black Mirror a unica temporada que manteve o numero de votos foi a primeira(episodios 1 a 2), enquanto que na segunda e terceira temporada vemos uma variacao do numero de votos. Dentre as variacoes a temporada que mais teve queda no numero de votos sendo a terceira temporada (o episodio 5 chegou a ter o menor numero de votos da serie) como podemos ver abaixo.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserVotes, color = season)) + 
  geom_line() + 
  geom_point() +
  labs(title = "Distribuicao de votos ao longo das temporadas", x="Episodio da temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-22](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-22-1.png)

Quanto a correlacao entre o episodio da temporada e seu numero de votos podemos perceber a existencia de uma correlacao linear negativa, aparentemente forte, entre essas duas variaveis apenas na primeira temporada.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserVotes, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserVotes, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1        -0.8885269         -1.0000000
## 2      2         0.3197616          0.0000000
## 3      3        -0.6849419         -0.4666667
{% endhighlight %}
# 13 Reasons Why
**Descricao da variavel classificacao do usuario (UserRating)**

{% highlight r %}
dados %>%
  filter(series_name == "13 Reasons Why") %>%
    ggplot(aes(x = series_name, y = UserRating)) + 
    geom_jitter(width = .1, color = "red") +
  labs(title = "Distribuicao da classificacao do usuario", x="Nome da serie", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-24](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-24-1.png)

Podemos perceber que segundo a classificacao de usuarios IMDB acima, 13 Reasons Why nao possui valores estranhos. Existe pouca variacao se comparado as series Sense8 e Black Mirror e dentre os valores frequentes temos as classificacoes de 8.5. Isto fica ainda mais claro no histograma abaixo.

{% highlight r %}
dados %>%
  filter(series_name == "13 Reasons Why") %>%
    ggplot(aes(x = UserRating)) + 
    geom_histogram(binwidth = .5, fill = "red", color = "black") + 
    geom_rug() +
  labs(title = "Histograma da classificacao do usuario", x="Classificacao do usuario", y = "Frequencia")
{% endhighlight %}

![plot of chunk unnamed-chunk-25](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-25-1.png)

Como a frequencia da classificacao concentra-se em 8.5 entao espera-se que a mediana esteja neste entorno. Neste caso, quase houve a coincidencia da mediana(8.5) e da media(8.669231).

{% highlight r %}
median((dados %>%  filter(series_name == "13 Reasons Why"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 8.5
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "13 Reasons Why"))$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 8.661538
{% endhighlight %}
Podemos tambem observar a distribuicao de classificacao por temporada de 13 Reasons Why. A primeira temporada, unica presente nos dados, tem classificacao em termos de mediana de 8.5 como vemos no grafico abaixo.

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
    ggplot(aes(x = as.character(season), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot da classificacao do usuario por temporada da serie", x="Temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-27](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-27-1.png)

Com o passar dos episodios de 13 Reasons Why da primeira temporada a classificacao mais baixa foi registrada antes do episodio 5 como podemos ver no grafico abaixo.

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point() +
  labs(title = "Distribuicao da classificacao ao longo da temporada", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-28](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-28-1.png)

Quanto a correlacao entre o episodio da temporada e sua classificacao podemos perceber a existencia de uma correlacao linear aparentemente forte(~1).

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserRating, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserRating, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 1 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1         0.8734228          0.7190925
{% endhighlight %}
**Descricao da variavel votos dos usuarios (UserVotes)**

{% highlight r %}
dados %>%
  filter(series_name == "13 Reasons Why") %>%
    ggplot(aes(x = series_name, y = UserVotes)) + 
    geom_jitter(width = .1, color = "red") +
labs(title = "Distribuicao de votos do usuario", x="Nome da serie", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-30](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-30-1.png)

Podemos perceber que segundo a distribuicao de votos dos usuarios do IMDB acima, 13 Reasons Why possui valores estranhos(>3500). Existe variacao tanto quanto se comparado as series Sense8 e Black Mirror. Como a frequencia da votos concentra-se entorno de 2500 entao espera-se que a media e a mediana esteja neste entorno. Neste caso, a mediana e de 2445 e a media 2632.923.

{% highlight r %}
median((dados %>%  filter(series_name == "13 Reasons Why"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 2632
{% endhighlight %}



{% highlight r %}
mean((dados %>%  filter(series_name == "13 Reasons Why"))$UserVotes)
{% endhighlight %}



{% highlight text %}
## [1] 2856.538
{% endhighlight %}
Podemos tambem observar a distribuicao dos votos por temporada de 13 Reasons Why. A primeira temporada, unica presente nos dados, tem numero de votos em termos de mediana de 2445 como vemos no grafico abaixo.

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
    ggplot(aes(x = as.character(season), y = UserVotes)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot de votos do usuario por temporada da serie", x="Temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-32](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-32-1.png)

Com o passar dos episodios de 13 Reasons Why da primeira temporada o episodio com menor numero de votos foi registrado no episodio 8. Felizmente, logo apos o episodio 12 e registrado o maior numero de votos da serie como podemos ver no grafico abaixo.

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserVotes, color = season)) + 
  geom_line() + 
  geom_point() +
  labs(title = "Distribuicao de votos ao longo das temporadas", x="Episodio da temporada", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-33](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-33-1.png)

Quanto a correlacao entre o episodio da temporada e seu numero de votos podemos perceber a desistencia de uma correlacao linear  forte.

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
    group_by(season) %>% 
    summarise(correlacao_linear = cor(season_ep, UserVotes, 
                                      method = "pearson"), 
              correlacao_kendall = cor(season_ep, UserVotes, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 1 x 3
##   season correlacao_linear correlacao_kendall
##    <int>             <dbl>              <dbl>
## 1      1         0.1427493         -0.1538462
{% endhighlight %}

# `Pergunta 1`
**Qual das series que voce escolheu no checkpoint 1 e mais votada pelos usuarios do IMDB? E a menos votada? A diferenca e grande? Pequena?**
*Possivel resposta: Acredito que a mais votada seja Sense8 e a menos votada seja Black Mirror com diferenca grande.*

Aparentemente a serie mais bem votada e a serie Black Mirror se observar-mos o grafico de dispersao abaixo e levarmos em consideracao os votos do usuario (UserVotes) e a mediana como metrica de comparacao.

{% highlight r %}
dados %>% 
    ggplot(aes(x = series_name, y = UserVotes)) + 
    geom_jitter(width = .1, color = "red")  +
  labs(title = "Distribuicao de votos do usuario", x="Nome da serie", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-35](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-35-1.png)

Para observar melhor qual delas e mais bem votada segundo os votos do usuario vamos olhar para o grafico de box plot de cada uma das series.

{% highlight r %}
dados %>% 
    ggplot(aes(x = series_name, y = UserVotes)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot de votos do usuario por serie", x="Nome da serie", y="Votos do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-36](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-36-1.png)

Olhando para o grafico acima e utilizando a mediana como metrica de comparacao entre as series, fica claro que Black Mirror e a serie mais bem votada e a menos votada fica sendo Sense 8(por uma diferenca muito pequena com relacao a 13 Reasons Why). A mediana e uma boa metrica porque ajuda a perceber onde fica a maior concentracao de votos do usuario diminuindo o vies de outliers. Neste caso em particular, independente de ser utilizado a metrica mediana ou media os resultados iriam ser os mesmos.

Ainda sobre a diferenca de cada uma das series vemos que a mediana de votos das series 13 Reasons Why e Sense8 esta entre 0 e 2500, podendo se considerar que, e pequena a diferenca de votos entre elas e grande entre Black Mirror e 13 Reasons Why e Black Mirror e Sense8 como vemos no grafico acima.

Conclusao: O senso comum leva a crer que pelo fato de Sense8 ser a serie mais bem classificada pelos usuarios segundo o IMDB (Checkpoint 1) entao, certamente, ela seria a serie mais votada pelos usuarios(altos votos levam a alta classificacao). Isto significa que a a classificacao dos usuarios segundo o IMDB leva em consideracao algo alem do numero de votos que os usuarios dao a um episodio ja que a serie mais bem votada foi Black Mirror e nao Sense8.

Nova pergunta: Com o intuito de certificar se existe ou n?o uma rela??o entre a classificacao dos usuarios segundo o IMDB (UserRating) e o numero de votos dos usuarios (UserVotes), qual o coeficiente de corelacao linear entre essas duas variaveis?

{% highlight r %}
dados %>% group_by(series_name) %>% 
    summarise(correlacao_linear = cor(UserVotes, UserRating, 
                                      method = "pearson"), 
              correlacao_kendall = cor(UserVotes, UserRating, 
                                       method = "kendall"))
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 3
##      series_name correlacao_linear correlacao_kendall
##            <chr>             <dbl>              <dbl>
## 1 13 Reasons Why         0.5088530          0.1438185
## 2   Black Mirror         0.4876892          0.2252891
## 3         Sense8        -0.6666444         -0.4075222
{% endhighlight %}
Como podemos visualiar na tabela acima o coeficiente de correlacao linear entre as variaveis, em cada uma das series, esta distante tanto de 1, quanto de -1. Logo, pode-se dizer que existe uma correlacao linear fraca entre a classificacao dos usuarios e os votos dos usuarios para um dado episodio, em outras palavras: o numero de votos de um episodio nao ira significar que o episodio sera bem classificado ou mal classificado segundo o IMDB necessariamente.

# `Pergunta 2`
**Qual o episodio, das series que voce escolheu no checkpoint 1, que foi mais bem avaliado segundo o IMDB? E o que foi menos avaliado? A diferenca e grande? Pequena?**
*Possivel resposta: Acredito que os mais bem avaliado seja o primeiro episodio em cada uma das series e que o menos avaliado seja os episodios do meio ou decorrer.*

O episodio de Sense8 mais bem avaliados foi o episodios 11 da segunda temporada e o menos avaliado foi o episodio 1 da primeira temporada como podemos ver no grafico abaixo. Quanto a diferenca pode-se dizer que e grande, o episodio 11 teve avaliacao superior a 9.5 enquanto o episodio 1 teve 7.5(diferenca de pelo menos 2 pontos). 

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point()+
  scale_x_continuous(breaks=seq(1, 12, 1)) +
  labs(title = "Distribuicao da classificacao ao longo das temporadas", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-38](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-38-1.png)

O episodio de Black Mirror mais bem avaliado foi o episodios 4 da segunda temporada e o menos avaliado foi o episodio 3 da mesma temporada como podemos ver no grafico abaixo. Quanto a diferenca pode-se dizer que e grande, o episodio 4 teve avaliacao superior a 9.0 enquanto o episodio 3 teve avaliacao 7.0(diferenca de pelo menos 2 pontos). 

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point() +
labs(title = "Distribuicao da classificacao ao longo das temporadas", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-39](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-39-1.png)

O episodio de 13 Reasons Why mais bem avaliados foi o episodios 13 da primeira temporada e o menos avaliado foi o episodio 3 da mesma temporada como podemos ver no grafico abaixo. Quanto a diferenca pode-se dizer que a diferenca razoavelmente grande, o episodio 13 teve avaliacao proxima a 10 enquanto o episodio 3 teve avaliacao proxima de 8.2(diferenca de quase 2 pontos). 

{% highlight r %}
dados %>%  filter(series_name == "13 Reasons Why") %>% 
  mutate(season = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = season)) + 
  geom_line() + 
  geom_point() +
  labs(title = "Distribuicao da classificacao ao longo da temporada", x="Episodio da temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-40](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-40-1.png)

Conclusao: Como podemos ver o primeiro episodio de cada uma das series nao e o mais bem avaliado e apenas as series 13 Reasons Why e Black Mirror tiveram o episodio menos bem avaliado no decorrer da temporada.

Nova pergunta: Para as series que possuem mais de uma temporada, a avaliacao dos episodios de uma nova temporada cresceu ou decresceu quando comparados a avaliacao dos episodios da temporada anterior?

As unicas series dentre as 3 escolhidas possuidoras de mais de uma temporada sao Sense8 e Black Mirror.

{% highlight r %}
dados %>%
  filter(series_name != "13 Reasons Why") %>%
  ggplot(aes(x = series_ep, y = UserRating, color = series_name)) +
  geom_line() +
  geom_point() +
  scale_y_continuous() +
  scale_x_continuous(breaks=seq(1,23,1)) +
  facet_grid(series_name ~ .) +

  xlab("Episodio da serie") + 
  ylab("Classificacao do usuario") +
  ggtitle("Distribuicao da classificacao do episodio ao longo das temporadas") +
  theme(plot.title = element_text(hjust = 0.5))
{% endhighlight %}

![plot of chunk unnamed-chunk-41](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-41-1.png)

Olhando para o grafico acima fica facil observar se a avaliacao dos episodios da nova temporada cresce ou decresce quando comparados a temporada anterior. Black Mirror apresenta uma variacao da avaliacao dos episodios com o passar das temporadas mas fica dificil afirmar com precisao se a avaliacao dos episodios cresceu ou decresceu.

Observando Sense8 podemos observar um crescimento da avaliacao dos episodios.

{% highlight r %}
dados %>%  filter(series_name == "Black Mirror") %>% 
    ggplot(aes(x = as.character(season), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot da classificacao do usuario por temporada da serie", x="Temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-42](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-42-1.png)

Observando o grafico acima fica facil perceber como se comporta a avaliacao dos episodios com o passar das temporadas de Black Mirror. Percebe-se claramente que a serie mantem a avaliacao dos episodios se utilizamos a mediana para comparar cada temporada. Ou seja, a serie manteve a avaliacao dos episodios.

{% highlight r %}
dados %>%  filter(series_name == "Sense8") %>% 
    ggplot(aes(x = as.character(season), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "red")+
  labs(title = "Box-plot da classificacao do usuario por temporada da serie", x="Temporada", y="Classificacao do usuario")
{% endhighlight %}

![plot of chunk unnamed-chunk-43](/knitr-jekyll-ad1figure/source/2017-07-05-postagem-prob1/unnamed-chunk-43-1.png)

Observando o grafico acima fica facil perceber como se comporta a avaliacao dos episodios com o passar das temporadas de Sense8. Percebe-se claramente o crescimento da avaliacao dos episodios se utilizamos a mediana para comparar cada temporada. Ou seja, houve um crescimento da avaliacao dos episodios.

Respondendo a pergunta: a avaliacao dos episodios das novas temporadas de Black Mirror foi mantida variando sob uma razao de decimos, ja a avaliacao dos episodios da nova temporada de Sense8 houve um crescimento quando comparados a avaliacao dos episodios da temporada anterior.

Copyright © 2017 [Marcos Nascimento](https://github.com/marcosasn/AD1/blob/master/problema1/R/prob1_cpoint4.Rmd)
