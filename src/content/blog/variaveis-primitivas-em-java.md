---
title: "Variáveis primitivas em Java"
description: "Variável é uma região de memória reservada para armazenar valores de modo temporário."
pubDate: 2020-08-27
category: "professional"
lang: "pt"
tags: ["java", "iniciante"]
series: "início de jornada"
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

Variável é uma região de memória reservada para armazenar valores de modo temporário. Ao pensar em variáveis Java, tente imaginar copos de diferentes tamanhos. Xícaras de café, copos de botequim, aquela caneca de cerveja de 1 litro que você comprou na Oktoberfest em Blumenau. Uma variável nada mais é do que um contâiner que contém alguma coisa. Para uma variável existir ela precisa ter um nome, um tipo e um valor.

## Variáveis Primitivas

Ainda usando a analogia dos copos, uma variável primitiva pode ser comparada com os diferentes copos disponíveis no Starbucks quando você pede um café: short, tall, grande, venti, trenta.

Cada copo suporta uma determinada quantidade de bebida. Em Java, as variáveis primitivas assim como os copos do Starbucks também tem tipos e tamanhos específicos.

As variáveis primitivas em Java são boolean, char, byte, short, int, long, float e double.

Be Careful! Bears Shouldn't Ingest Large Furry Dogs (eles têm muita indigestão tadinhos)

Toda variável além de um tipo, também precisa de um nome e de um valor. Vamos supor que você tem dois copos reutilizáveis da Starbucks de tamanhos distintos, um short e outro Venti. Você nomeou o short como Cherie e o Venti como nopainnogain. Você é uma pessoa de hábitos e sempre toma seu café espresso na Cherie e seu Frappucino de caramelo no nopainnogain.

Seguindo esse raciocínio fica muito fácil declarar variáveis primitivas em Java:

```java
short Cherie = 30;
int nopainnogain = 900;
```

Você não pode colocar 1 litro de café em um copo de 30ml. Ok, você até pode mas com certeza você vai perder bastante do líquido. E é exatamente por isso que um compilador Java não permite que você atribua valores que são fora da gama de valores para aquela variável em específico.

Por exemplo, você não pode enfiar um contâiner int em um contâiner byte de modo algum:

```java
int x = 24;
byte b = x; // não compila!
```

Mas perae, por que isso não vai compilar? O valor de x é 24 e 24 definitivamente é um número pequeno o suficiente para caber em um byte. Porém, o compilador só se importa com o fato de você estar tentando colocar um contâiner grande em um contâiner pequeno, o compilador não enxerga o valor de x.

E quanto a um contâiner pequeno em um contâiner maior? Sem problema!

## Atribuindo valores a sua variável

Existem algumas formas de atribuir um valor a sua variável:

- Digitando um valor literal após o sinal de = (`int x = 24`, `boolean isCodingHard = true`, etc)
- Atribuindo valores de uma variável a outra (desde que elas sejam compatíveis, `int x = 24`, `int y = x`, portanto y=24)
- Usando uma expressão que combine os dois (`int x = 24`, `int y = x + 20`, portanto y=44)

## Nomeando sua variável

Posso usar qualquer nome que eu quiser?

Você pode usar qualquer nome usando as simples regrinhas abaixo. Lembre-se que é boa conduta nomear suas variáveis de maneira que faça sentido para você e seus colegas de equipe. Nomear suas variáveis com os personagens de Dragon Ball Z pode parecer all fun and games agora, mas lembrar o que cada uma faz quando o seu projeto tiver atingido um nível de complexidade maior não sai ser nada divertido. (A menos que você documente tudo muito bem documentadinho, mas vamos convir que seria uma perda de tempo monstro)

Vamos às regrinhas:

- O nome da sua variável deve começar com uma letra, um underscore (_) ou com um símbolo de dólar ($). Você não pode usar números como primeiro caracter do seu nome, mas pode usar depois.
- Não pode utilizar nomes "reservados" como nome de sua variável. A menos que você queira levar seu compilador à loucura.
- Quando o nome da sua variável for de apenas uma palavra, é melhor usar apenas letras minúsculas. ex: `long goku = 9223372036854775807L;`
- Se sua variável tiver mais de um nome, é boa prática escrever a primeira palavra em letras minúsculas e usar letra maiúscula apenas na primeira letra das palavras que seguirem. ex: `boolean numaBatalhaNoMundoRealGokuVenceriaSuperManEVerdadeIssoSimEuFizOsCalculos = true;`

Molezinha né? E quanto as variáveis não-primitivas? É aí que o bicho pega, então fica para a próxima semana.
