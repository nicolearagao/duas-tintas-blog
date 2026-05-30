---
title: "Programação orientada a objetos para newbies"
description: "Engatinhando em POO"
pubDate: 2020-08-06
category: "professional"
lang: "pt"
tags: ["java", "POO", "iniciante"]
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

A programação orientada a objetos é um modelo de análise, projeto e programação de software, que se baseia na composição e interação entre diversas unidades que são chamadas de "objetos". A POO é um dos paradigmas mais difundidos na atualidade e pode ser encontrado em várias linguagens populares como Java, Python, C++, C#, Ruby, Scala e etc.

## Qual a diferença entre programação estruturada e programação orientada a objetos?

Vamos supor que você queira criar um programa simples onde você tenha três figuras de formas geométricas independentes e que giram 360 graus para a direita e tocam um som de buzina toda vez que um usuário clicar em cima de uma.

O programador que utiliza um paradigma estruturado vai pensar nos procedimentos do programa e o código ficará relativamente simples pois ele só teria que se preocupar com a rotação e em tocar o som da buzina.

Por outro lado, o programador utilizando POO teria que pensar "Quais são as 'coisas' que existem nesse programa? Se eu clicar onde acontece o que?". Seguindo esse raciocínio podemos concluir que as formas geométricas em si são os pontos chaves do programa, as figuras são portanto os nossos objetos (o clique do mouse, o usuário, o som também são objetos, mas não vamos complicar). Teríamos que criar os objetos no nosso sistema e atribuir a eles ações.

Até aí beleza, ainda não dá para ver muita vantagem em OO comparado ao outro paradigma. Mas e se no meio do processo de desenvolvimento eu mudasse de ideia e resolvesse que quero adicionar uma quarta forma geométrica, que vai girar apenas 180 graus para a direita e vai tocar um miado de gato quando selecionada?

Pro programador de OO vai ser molezinha, pois basta que ele adicione mais um objeto. Ele não terá que fazer nenhuma modificação ao código já escrito e testado.

Para o programador de linguagem estruturada, também não vai ser tão difícil, pois basta que ele adicione um (if … else) e vai estar tudo resolvido.

E se mais para frente eu quisesse que cada forma geométrica tocasse um som diferente?(!!!) E que cada forma girasse para diferentes ângulos e direções? O programador que usa programação estruturada teria que reformar o código inteiro, tendo cuidado para não quebrar o que já estava funcionando. O programador de OO vai apenas modificar cada objeto individualmente sem se preocupar se a ação de um irá afetar a ação de outros. Está aí a grande beleza da programação orientada a objetos. Ela funciona bem para ambientes de desenvolvimento onde os requisitos podem mudar a qualquer momento. Ela te permite isolar um problema e resolvê-lo individualmente.

Mas perae, não fica repetitivo isso ae não? Você não tá duplicando código?

Sim! E é exatamente por isso que em POO temos o que chamamos de classes.

## Classes

De acordo com a Wikipedia, uma classe é uma estrutura que abstrai um conjunto de objetos com características similares. Uma classe define o comportamento de seus objetos — através de métodos — e os estados possíveis destes objetos — através de atributos. No exemplo acima podemos perceber que todos os 4 objetos tem coisas em comum, todos rodam e todos tocam som. Quando falamos em abstração estamos falando justamente da capacidade de analisar os objetos como um todo e tentarmos descobrir quais características eles dividem, de modo a agrupá-los em grupos(classes). A abstração busca apenas as características relevantes para o programa que você está escrevendo. Seria relevante para nós levar em consideração as cores das nossas formas geométricas? Não! Se estivéssemos escrevendo um programa infantil onde temos apenas círculos de várias cores. Um som de miado toca apenas se a criança clicar no círculo vermelho. Nesse caso específico, a cor tem alguma relevância? Sim! Portanto temos sempre que analisar o que é relevante.

Retângulo, Triângulo, Estrela e Círculo são todos Formas Geométricas, e todos rodam e tocam algum tipo de som. Podemos afirmar que esses objetos herdam de Formas Geométricas. Portanto, não teríamos que escrever o código de cada um individualmente. Os atos de tocar som e rodar são o que chamamos de método.

Tá, mas como então que o círculo vai tocar miado de gato se ele herda as características da classe Formas Geométricas, que tem por default o som da buzina?????

Através do @overriding, podemos atribuir métodos específicos a um objeto, mesmo que um outro método já tenha sido atribuído anteriormente na classe-mãe.

Todos os objetos irão herdar o tocarSom buzina, exceto o círculo pois o miado de gato vai sobrepor o método definido anteriormente.

## Ainda em dúvida?

Luna, Pepito e Bilbo pertencem a classe Cachorros Foficos. Todos latem, correm e comem, portanto podemos afirmar que todos os três apresentam familiaridades suficientes para que pertençam a mesma classe.

Agora tá claro, tá não?

## Atributos e métodos

Ao designar uma classe você precisa pensar nas coisas que o objeto "sabe" e nas coisas que o objeto pode "fazer".

As coisas que o objeto sabe sobre si mesmo são chamadas de atributos. Atributos são dados e também conhecidos como variáveis. As coisas que um objeto pode fazer são chamadas de métodos.

Voltando a classe de cachorros foficos. Os atributos do Pepito por exemplo seriam: Basset, 8 quilos, Pepito, Pato Donald Murcho, Marrom-bombom. Os atributos da Luna e do Bilbo são diferentes, pois cada um tem a sua individualidade. Os métodos porém são herdados da classe-mãe, e todos os três são capazes de latir, correr, comer e serfoficos.

## Os quatro pilares da POO

### Abstração

A abstração é um ponto essencial em POO como já mencionei acima. Nada mais é do que concentrar nos aspectos essenciais de um contexto, ignorando características menos importantes ou acidentais. A abstração é importantíssima para a organização do nosso código.

### Herança

Quando mencionei acima que os objetos (tanto as formas geométricas quanto cachorros foficos) herdaram os métodos e atributos da classe-mãe, estava me referindo a esse pilar. Na prática, é a possibilidade de construir objetos especializados que herdam as características de objetos mais generalistas. A herança é uma maneira de reutilizar o código a medida que podemos aproveitar atributos e métodos que já existiam anteriormente.

### Polimorfismo

Polimorfismo está intimamente conectado a herança. É a capacidade de atribuir comportamentos distintos ao mesmo método. Por exemplo, Luna, Bilbo e Pepito latem. Os três herdaram o método latir da classe cachorros foficos. Porém Luna late estridente, Bilbo late desesperado e Pepito late igual cachorro grande. Apesar do método ser o mesmo, o polimorfismo propicia a "customização" se necessário.

### Encapsulamento

Encapsulamento é uma técnica que faz com que detalhes internos do funcionamento dos métodos de uma classe permaneçam ocultos para os objetos. No caso dos nossos cachorrosFoficos, a Luna não precisa saber a anatomia canina em detalhe para ser capaz de latir. Ela faz AU e pronto. Podemos encapsular os atributos de uma classe também. Encapsulando o atributo cor da pelagem: branquinha, garantimos assim que nosso objeto se mantenha coeso. Luna não pode decidir que um dia quer ser cor champagne igual o irmão.

## Concluindo…

Ao longo desse artigo, cobrimos as características básicas da POO. Entendemos um pouquinho como o paradigma funciona e nas vantagens que apresenta aos programadores nos dias de hoje. Porém POO vai muito, muito, muitooooo além desse artigo e cada linguagem de programação tem as suas próprias regras em suas aplicações.

Para quem quer saber mais:

- Orientação a objetos — Thiago Leite e Carvalho
- Domain-driven design — Eric Evans
- Use a cabeça Padrão de objetos — Eric Freeman
- Padrões de Projeto: soluções reutilizáveis de Software — Eric Gamma, Richard Helm, Ralph Johnson, John Vlissides
