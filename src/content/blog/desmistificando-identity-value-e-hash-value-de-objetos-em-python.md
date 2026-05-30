---
title: "Desmistificando identity, value e hash value de objetos em Python"
description: "id(), hash() e operadores == e is"
pubDate: 2022-06-11
category: "professional"
lang: "pt"
tags: ["python", "objetos", "hashable"]
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

Essa semana, enquanto eu cumpria minha rotina de estudos em estruturas de dados em Python, percebi que a minha dificuldade em entender e memorizar determinados conceitos estava diretamente relacionada à falta de um conhecimento detalhado dos objetos de Python.

Os objetos em Python muitas vezes se comportam de maneira inesperada, e a compreensão sobre a diferença entre igualdade e identidade dos mesmos ocasionalmente se apresenta nebulosa.

Vamos entender primeiro o que são os objetos em Python e depois discutiremos a respeito das diferenças entre identity, value e hash value.

## Objetos em Python

Objetos são abstrações do Python para dados. Todos os dados em um programa Python, são representados por objetos ou por relações entre objetos.

Conceitualmente, objetos são como componentes de um sistema. Pense em um programa como uma linha de produção de uma indústria. A cada passo da linha de montagem, um componente do sistema manipula algum material, de modo a transformar o material bruto em um produto finalizado.

Diferentes linguagens de programação definem o conceito de objeto de maneiras distintas. Em algumas, todos os objetos precisam ter atributos e métodos, em outras todos os objetos precisam ser subclasses. Em Python, essa definição é um pouco mais flexível. O que queremos dizer com a frase "Tudo é um objeto em Python." é que todos os componentes desse sistema são first-class. Todos os objetos que podem ser nomeados na linguagem (integers, strings, modules, métodos, funções, classes…) tem status idêntico. Isso significa que eles podem ser atribuídos a variáveis, usados em listas e dicionários, passados como argumentos em funções e etc.

Todo objeto tem uma identidade, um tipo e um valor.

## Identidade de um objeto

Todos os objetos possuem uma identidade que nunca muda a partir do momento em que é criada. A identidade de um objeto identifica onde este está sendo armazenado na memória.

> .. a função id() retorna um inteiro representando sua identidade.

Pense no seu computador como um hotel com muitos quartos e os objetos de Python como hóspedes desse hotel. No início da temporada de verão (quando você começa o programa Python) todos os quartos estão livres. Porém, quando um hóspede novo chega ao hotel (um novo objeto em Python), você o encaminha para um dos quartos vagos. Todos os quartos têm um número de identificação e a função `id()` nos diz em qual quarto o hóspede (Mr objeto) está ficando.

*Pergunta do Enem: No Overlook, hotel de O iluminado, os fantasmas que habitavam a construção podem ser considerados objetos?*

Se dois objetos que existem simultaneamente tem a mesma identidade, significa na realidade que ambos são referências ao mesmo objeto em memória. O operador `is` compara itens por identidade, se eu digo que `a is b`, é equivalente a dizer que `id(a) == id(b)`.

```python
a = ["Luna", "Bilbo", "Pepito"]
c = a
id(a) == id(c)  # True - ambos referenciam uma lista de cachorros fofinhos
```

## Valor de um objeto

O valor de um objeto é o que você atribui a ele (ex: Na imagem acima adicionei strings de nomes de cachorros foficos às listas a e b). O valor de um objeto se refere ao que o objeto carrega consigo, algo como a mala de viagem de um hóspede utilizando a metáfora acima.

O valor de alguns objetos pode mudar. Objetos cujos valores podem mudar são descritos como mutáveis, objetos cujo valor não pode ser mudado uma vez que foram criados são chamados imutáveis.

O que vai determinar se um objeto é mutável ou não é o seu tipo. Pense em um objeto mutável como a cápsulas Hoi-Poi que a Bulma carregava (se você não sabe quem diabos é Bulma, estou falando de Dragon Ball). A cápsula poderia virar carro, casa, moto e etc.

**Objetos Mutáveis:**

list, dict, set, bytearray

**Objetos imutáveis:**

int, float, complex, string, tuple, frozenset [versão imutável do set], bytes

Valores podem ser comparados através do operador `==`, então toda vez que `a == b`, quer dizer que ambos tem o mesmo valor. Objetos container (como lists) tem o valor que é definido por seu conteúdo, enquanto outros tipos de objeto terão valores baseados em seus atributos.

Objetos de tipos diferentes podem às vezes apresentar os mesmos valores:

```python
1 == 1.0  # True - comparando objetos de tipos diferentes mas que possuem o mesmo valor
```

## Hash value (A.K.A hash code) de um objeto

Além de um valor, alguns objetos possuem um hash value, se esses forem considerados hashable. O hash de um objeto deve permanecer o mesmo durante a vida útil do objeto (durante o runtime de determinado interpretador), então faz sentido que para um objeto ser considerado hashable, seu valor deve ser imutável.

> Tipos numéricos, tipos imutáveis como str e bytes são todos hashable. Objetos tipo contâiner são hashable se forem imutáveis e se todos os objetos contidos nele forem imutáveis também. Um frozenset é sempre hashable, porque todo elemento contido nele precisa ser hashable por definição. Uma tupla é hashable apenas se todos os seus itens forem hashable. — Tradução livre página 84 do Fluent Python

Hash values são apenas integers, que podem ser usadas para comparar chaves de um dicionário rapidamente durante um lookup. O hash value será obtido através da função `hash()`, cujo objetivo é converter uma peça de informação em um hash codificado.

### Beleza, para que serve isso aí jovem?

Dicionários e sets são implementados utilizando uma hash table, um bloco de memória contíguo, que é gerado dos hash values de seus elementos. Isso significa que podemos inserir, procurar e remover elementos muito mais rapidamente já que já sabemos o seu índice no array de memória.

É possível que diversos objetos diferentes possuam o mesmo hash value. Isso não significa necessariamente que eles possuam o mesmo valor. Isso é chamado de hash collision.

A função `hash()` estoura um TypeError quando uma lista é passada como argumento:

```python
hash([1, 2, 3])  # TypeError: unhashable type: 'list'
```

## Objetos se comportando inesperadamente

Não seria maravilhoso se não houvessem exceções? Vamos dar uma olhada em mais um exemplo:

```python
idade_bilbo = 7
idade_luna = 7
```

Acima criamos duas variáveis (idade_bilbo e idade_luna) e dois objetos que possuem o mesmo número integer como valor. Você pode imaginar que estes são dois novos hóspedes e cada um ganhou um quarto separado.

Ambos objetos possuem o mesmo valor. Utilizando o operador de igualdade, obtemos o seguinte resultado:

```python
idade_bilbo == idade_luna  # True - as duas variáveis possuem o mesmo valor
```

Qual você acha que será o retorno ao comparamos as identidades das variáveis acima?

```python
idade_bilbo is idade_luna  # True - PERA, QUÊ??
```

Nós criamos dois objetos distintos e os atribuímos a duas variáveis idade_bilbo e idade_luna. Ambas tem o mesmo valor então `idade_bilbo == idade_luna` retorna True. Por que diabos então o Python nos diz que eles são o mesmo objeto? Nós não especificamos `idade_luna = idade_bilbo` para que fossem referências ao mesmo objeto em memória, mas em vez disso criamos dois objetos independentes.

Por que o Python criou apenas um objeto e não dois, apesar de termos pedido explicitamente que ele criasse dois?

> Os tipos afetam quase todos os aspectos do comportamento do objeto. Até mesmo a importância da identidade do objeto é afetada em algum sentido: para tipos imutáveis, as operações que calculam novos valores podem realmente retornar uma referência a qualquer objeto existente com o mesmo tipo e valor, enquanto para objetos mutáveis isso não é permitido. Por exemplo, após `a = 1; b = 1`, a e b podem ou não se referir ao mesmo objeto com o valor um, dependendo da implementação, mas após `c = []; d = []`, c e d têm a garantia de referir-se a duas listas vazias diferentes e únicas. (Observe que `c = d = []` atribui o mesmo objeto para c e d.)

Então o que isso significa exatamente? Apesar de termos pedido para o Python criar dois objetos separadamente, "dependendo da implementação", Python poderia criar apenas um objeto e ambas variáveis apontando para o mesmo objeto. O mesmo pode ocorrer com pequenas strings também. Valeu Python!

Espero que esse artigo tenha te ajudado a entender um pouco mais sobre a diferença entre igualdade e identidade. Qualquer observação ou sugestão, podem mandar abaixo nos comentários.
