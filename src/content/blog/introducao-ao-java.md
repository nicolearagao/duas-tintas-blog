---
title: "Introdução ao Java"
description: "História e idiossincrasias da linguagem Java."
pubDate: 2020-10-01
category: "professional"
lang: "pt"
tags: ["java", "iniciante"]
series: "início de jornada"
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

## O começo

A história do Java começa no início dos anos 90, na empresa Sun Microsystems (que foi adquirida pela Oracle em 2009), em um time de inovação tecnológica liderado por James Gosling.

Inicialmente o plano era o desenvolvimento de uma tecnologia moderna que permitisse que eletrodomésticos se comunicassem entre si. Por exemplo, uma torradeira ser acionada logo após o timer do fogão chegar ao final. Acabaram por desenvolver um sistema operacional chamado GreenOS pois a criação de um sistema para cada tipo de aparelho seria simplesmente inviável. James Gosling criou a linguagem de programação Oak, cujo nome foi escolhido devido a uma árvore de carvalho que existia em frente a seu escritório. Esse nome já estava registrado, e depois de uma sessão de brainstorming, o nome Java foi escolhido. Java é o nome de um tipo de grão de café, supostamente o favorito de sua equipe de engenheiros.

Em 1995, Sun viu uma oportunidade na Web, que na época era capaz de exibir apenas páginas estáticas. Nesse ano a Sun lançou seus applets, que eram capazes de prover recursos interativos a conteúdos Web que não eram suportados pelo HTML sozinho. Os Applets hoje em dia foram quase todos substituídos pelo Javascript, mas ainda são usados em algumas aplicações específicas, como o teclado de bancos online.

Hoje em dia Java está presente em diversas aplicações, sendo mais popular como linguagem Back-end e no desenvolvimento de aplicativos móveis para Android.

## O que faz o Java especial?

Ao contrário de linguagens de programação tradicionais que foram criadas ou para serem compiladas para instruções nativas da plataforma ou serem interpretadas do código fonte na hora do runtime, Java foi feito para ser compilado para Bytecode, que depois é rodado pela Java Virtual Machine (JVM).

Java utiliza a metodologia de programação orientada a objetos, cujo principal objetivo é projetar software de modo que os vários tipos de informação que este manipula sejam combinadas com suas operações relevantes.

O conceito de portabilidade é extremamente importante em Java, permitindo que um programa seja executado em múltiplos sistemas operacionais sem que haja a necessidade de ser reescrito.

## JVM

Java Virtual Machine é uma camada intermediária entre o sistema operacional e a aplicação. O código-fonte escrito em Java é compilado por javac e transformado em Bytecode. É importante esclarecer que Bytecode não é uma linguagem de máquina propriamente dita, mas é compreendida pela máquina virtual do Java.

As vantagens do JVM são inúmeras, dentre elas a portabilidade das aplicações, que suportam o conceito de "WORA - Work once, run anywhere". O código-fonte não precisa ser recompilado ou reescrito para aquela plataforma específica.

JVM também oferece um gerenciamento de memória otimizado, através do automático Garbage Collector, que localiza e limpa espaços de memória que não estão sendo utilizados.

JVM é uma especificação, portanto empresas podem criar o seu próprio, desde que sigam determinadas regras. Um Bytecode que roda na JVM Oracle deve ser capaz de rodar na JVM IBM, por exemplo.

## Ramificações Java

**Java SE (Standard Edition)**
Base do Java - ambiente de execução e bibliotecas comuns

**Java EE (Enterprise Edition)**
Aplicações Corporativas e Internet

**Java ME (Micro Edition)**
Dispositivos móveis

## Nomenclaturas Java

### JRE (Java Runtime Environment)

O JRE é uma camada de software que roda em cima do JVM e sistemas operacionais e fornece as bibliotecas de classe e outros recursos que um programa Java precisa para rodar.

### JDK (Java Development Kit)

O JDK é um ambiente de desenvolvimento de software usado para desenvolver aplicações Java e applets. Ele inclui o JRE, um interpretador (java), um compilador (javac), um arquivador (jar), um gerador de documentos (javadoc) e outras ferramentas necessárias no desenvolvimento da linguagem.
