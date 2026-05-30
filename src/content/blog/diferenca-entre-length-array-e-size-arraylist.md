---
title: "Diferença entre length de uma Array e size de uma ArrayList"
description: "Os Arrays possuem a propriedade Length, ao contrário dos ArrayLists."
pubDate: 2020-10-09
category: "professional"
lang: "pt"
tags: ["java", "iniciante"]
series: "início de jornada"
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

Os Arrays possuem a propriedade Length, ao contrário dos ArrayLists. Arrays são estáticas, ou seja, não podem mudar de tamanho depois de terem sido criadas. Length nada mais é do que o espaço máximo alocado na memória quando as Arrays são criadas.

Quando criamos uma Array de tamanho n, então n blocos serão criados de tipo Array e JVM inicializa cada bloco de acordo com um valor default. Por exemplo, se você criar uma array int de 5 elementos, 5 blocos serão criados com o valor 0. O mesmo aconteceria se você criasse uma array double de 5 elementos, mas os blocos teriam como valor default o 0.0.

Em contrapartida, ArrayList não possui um Length, e seu tamanho e espaço que ocupa na memória podem ser alterados de acordo com a necessidade. Ele utiliza o método `.size()` que fornece o número total de objetos na coleção.
