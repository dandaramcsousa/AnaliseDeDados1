---
layout: post
title:  Atividade 3 - Agrupamento em duas dimensões
date: 2017-07-09 16:39:28
author: Dandara
published: true
tags: [htmlwidgets, r]
---



 

**Tipos de filme de Jim Carrey**

Nessa página analisaremos os filmes feitos por Jim Carrey utilizando os dados do [Rotten Tomataoes](https://www.rottentomatoes.com/).A ideia é agrupar para encontrar padrões nos filmes analisando a bilheteria e a avaliação do público. Os dados após serem extraídos foram limpos e organizados em um tibble. Os filmes são:


{% highlight text %}
##  [1] "Kick-Ass 2"                                     
##  [2] "The Incredible Burt Wonderstone"                
##  [3] "Mr. Popper's Penguins"                          
##  [4] "I Love You Phillip Morris"                      
##  [5] "Disney's A Christmas Carol"                     
##  [6] "Under the Sea 3D"                               
##  [7] "Yes Man"                                        
##  [8] "Dr. Seuss' Horton Hears a Who!"                 
##  [9] "The Number 23"                                  
## [10] "Fun With Dick and Jane"                         
## [11] "Lemony Snicket's A Series of Unfortunate Events"
## [12] "Eternal Sunshine Of The Spotless Mind"          
## [13] "Bruce Almighty"                                 
## [14] "Dr. Seuss' How the Grinch Stole Christmas"
{% endhighlight %}



**Sobre agrupamento**

Agrupar é uma forma de observar as semelhanças num conjunto de dados. Os grupos são feitos por elementos que possuem mais algo em comum com os outros elementos do grupo do que com os que estão fora. 

Primeiramente, analisaremos os possíveis grupos pela **bilheteria** (box office) do filme de forma logarítimica que mostra melhor as informações de maior volume, sem focar tanto nos extremos.


{% highlight r %}
filmes %>% 
    ggplot(aes(x = `Bilheteria`)) + 
    geom_histogram(bins = 20, fill= '#20289b') + 
    scale_x_log10() + 
    geom_rug()
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-4-1.png)

Através desse gráfico observamos claramente quatro grupos de valores de bilheteria, ou seja, os filmes quando analisados pela bilheteria se concentram em quatro partes.

Agora, analisando a **avaliação** do público, temos:


{% highlight r %}
filmes %>% 
    ggplot(aes(x = Avaliacao)) + 
    geom_histogram(bins = 16, fill = '#20289b') + 
    geom_rug()
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-5-1.png)

Visualmente é fácil perceber que há cinco montantes de valores indicando a porcentagem de aprovação de cada filme.

**Agrupamento em duas dimensões**

Observaremos agora que agrupando as duas dimensões (x como avaliação e y como bilheteria) temos a seguinte relação para os filmes:


{% highlight r %}
p = filmes %>% 
    ggplot(aes(x = Avaliacao, y = `Bilheteria`, color = TITLE)) + 
    geom_point() + theme(legend.position = "none") + scale_y_log10() 
ggplotly(p, width = 1000,height = 500)
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-6-1.png)


Fazendo o agrupamento pelo dendrograma, observamos o que a o espaço na largura indica a dissimilaridade dos pontos. Ou seja, quanto maior o espaço menos chances há dos subgrupos estarem relacionados.Traçamos, então, uma linhha onde os espaços se tornar muito grandes e mostra que possivelmente esse é o melhor número de grupos para se trabalhar. No nosso caso, como visto abaixo, quatro.


{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(Avaliacao, Bilheteria) %>%
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")

ggdendrogram(agrupamento_h_2d, rotate = TRUE) + geom_hline(yintercept = 41, colour = "red")
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-7-1.png)

A partir disso, então, veremos os possíveis agrupamentos para decidir se realmente 4 é um bom número. Vale lembrar que o agrupamento utilizado é aglomerativo e nem sempre o resultado obtido é o melhor possível.


{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(Avaliacao, `Bilheteria`) %>% 
    mutate(`Bilheteria` = log10(`Bilheteria`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")

ggdendrogram(agrupamento_h_2d, rotate = TRUE)
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-8-1.png)

{% highlight r %}
filmes2 = filmes %>% mutate(`Bilheteria` = log10(`Bilheteria`))
plota_hclusts_2d(agrupamento_h_2d, 
                 filmes2, 
                 c("Avaliacao", "`Bilheteria`"), 'TITLE',
                 linkage_method = "ward.D", ks = 1:6) + scale_y_log10()
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-8-2.png)


Analisamos também a distância entre os pontos de um grupo, para reafirmar a melhor opção. Quantos mais próximo de 1 mais próximo do seu próprio grupo um ponto está.

Primeiro, observando 3 grupos:

{% highlight r %}
distancias = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(Avaliacao, `Bilheteria`) %>% 
    mutate(`Bilheteria` = log10(`Bilheteria`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean")

colour = RColorBrewer::brewer.pal(3, "Set2")

plot(silhouette(cutree(agrupamento_h_2d, k = 3), distancias), col = colour, border = NA)
{% endhighlight %}

![plot of chunk unnamed-chunk-9](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-9-1.png)

Agora, observando 4 grupos:


{% highlight r %}
distancias = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(Avaliacao, `Bilheteria`) %>% 
    mutate(`Bilheteria` = log10(`Bilheteria`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean")
colourq = RColorBrewer::brewer.pal(4, "Set2")

plot(silhouette(cutree(agrupamento_h_2d, k = 4), distancias),col = colourq, border = NA)
{% endhighlight %}

![plot of chunk unnamed-chunk-10](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-10-1.png)

A partir daí, é fácil observar que 4 é realmente o melhor número de grupos e podemos observar melhor estes grupos:


{% highlight r %}
names(filmes)[names(filmes)=="Bilheteria"] <- "LogBilheteria"

filmes2 = filmes %>% mutate(LogBilheteria = log10(LogBilheteria))

grupos_q <- plota_hclusts_2d(agrupamento_h_2d, 
                 filmes2, 
                 c("Avaliacao", "LogBilheteria"),
                 'TITLE', 
                 linkage_method = "ward.D", ks = 4) + scale_y_log10()


ggplotly(grupos_q, width = 800, height = 500) 
{% endhighlight %}

![plot of chunk unnamed-chunk-11](/AnaliseDeDados1/figure/source/quarta-postagem/2017-07-09-quarto-post/unnamed-chunk-11-1.png)


1. O primeiro podemos definir como o de renda pequena e avaliação pequena, ou seja, os filmes que foram pouco vistos e foram considerados ruins pelo público. *The Number 23* é o filme de maior bilheteria nesse grupo e, ainda assim, foi o pior avaliado.

2. O segundo grupo é o de bilheteria alta. É o que tem a faixa de avaliação mais dispersa, por exemplo temos *Fun With Dick and Jane* que obteve cerca de 29% na avaliação e também *Dr Seuss' Horton Hears a Who!* que obteve 79% de aprovação. O que eles tem em comum é, na verdade a bilheteria próximo/acima de 100.

3. O terceiro como o de bilheteria baixa e com avaliação mediana é formado apenas por *I Love You Philip Morris*.  

4. O último como o filme de bilheteria média mas com uma avaliação ótima é formado por *Under the Sea 3D* e *Eternal Sunshine Of The Spotless Mind* que tiveram avaliações parecidas,94% e 92% respectivamente.
