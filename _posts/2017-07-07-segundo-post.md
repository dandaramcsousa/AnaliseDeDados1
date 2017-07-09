---
layout: post
title:  Atividade 2
date: 2017-07-08 22:48:03
author: Dandara
published: true
tags: [htmlwidgets, r]
---





Para esta análise foi utilizada um conjunto de dados de séries avaliadas no IMDB. Nele está contido informações de nome da série, nome e número do episódio, a url onde está hospedado o episódio no IMDB, a nota do usuário, o número de votos e a quantidade de votos que cada uma das notas receberam.

{% highlight text %}
##  [1] "series_name" "Episode"     "series_ep"   "season"     
##  [5] "season_ep"   "url"         "UserRating"  "UserVotes"  
##  [9] "r1"          "r2"          "r3"          "r4"         
## [13] "r5"          "r6"          "r7"          "r8"         
## [17] "r9"          "r10"
{% endhighlight %}
    
Nesta análise nem todas as informações de uma série serão utilizadas. O foco será principalmente nas avaliações com base em votações dos usuários do IMDB feitas para séries da [produtora CW](https://en.wikipedia.org/wiki/The_CW).


As atuais séries da CW e suas temporadas são:


{% highlight text %}
## Warning: package 'bindrcpp' was built under R version 3.3.3
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/figure/source/segunda-postagem/2017-07-07-segundo-post/unnamed-chunk-3-1.png)

**Pergunta 1. Qual das séries que você escolheu é mais bem avaliada no IMDB? A diferença é grande? Pequena?**
![plot of chunk unnamed-chunk-4](/figure/source/segunda-postagem/2017-07-07-segundo-post/unnamed-chunk-4-1.png)

A partir do bloxplot com a mediana de cada série pode-se observar que, de forma geral,The Originals é a série mais bem avaliada no IMDB. Porém a maior nota foi dada para um episódio em Arrow. A segunda e terceira série mais bem avaliada respectivamente são Supernatural e Arrow.

**Pergunta 2. As séries antigas são mais bem avaliadas que as novas?**

Considerando a série mais antiga como a que possui mais temporada e a mais nova como a que possui menos, Supernatural é a série mais antiga em exibição na CW enquanto Crazy Ex-Girlfriend é a mais nova. A partir do boxplot mostrado acima (*Avaliações de Série*) é possível observar que a avaliação de Supernatural é maior que a série mais jovem. Isso pode indicar que os fãs da série são tão assíduos que a produtora não vê a necessidade de cancelar a série. 

**Pergunta 3. As notas crescem de acordo com a temporada?**
![plot of chunk unnamed-chunk-5](/figure/source/segunda-postagem/2017-07-07-segundo-post/unnamed-chunk-5-1.png)

De forma geral é possível perceber que apenas Supernatural e The Originals caíram de nota na última temporada. Supernatural teve sua nota quase igualada à de sua décima temporada e The Originals com a de sua segunda. Podemos considerar então a temporada antecedente da última como picos em avaliações. E a partir daí dizer que não há um padrão de crescimento mas as séries que ainda estão sendo produzidas não possuem sequências de quedas de avaliação significante.
