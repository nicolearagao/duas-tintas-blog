---
title: "Da Autoria à Exegese: O Código como Ato de Linguagem na Era da IA"
description: "A IA tornou visível uma dimensão da profissão que sempre subestimamos: a engenharia de software tem uma natureza profundamente linguística."
pubDate: 2026-04-20
category: "professional"
lang: "pt"
tags: ["IA", "carreira", "linguística", "engenharia de software"]
---

Com o uso diário de IA no trabalho, venho percebendo uma mudança que no começo parecia só desconforto: leio muito mais código do que escrevo. E quanto mais isso se repete, mais questiono o que exatamente mudou - não na ferramenta, mas no meu papel. A sensação de que, se não estou digitando, não estou produzindo, é persistente, e sei que muitos engenheiros têm sentido o mesmo. Recentemente, numa conversa com meu colega Rafael Jeffman, percebi que o desconforto tinha nome e raiz.

Antes da engenharia, estudei Letras - sempre fui a garota de humanas, cercada de livros. Meu escritório até hoje tem mais estante do que monitor. Linguística era a minha matéria favorita, e fazia anos que esse conhecimento parecia guardado. Até que a IA o tornou relevante de novo.

Olhando com essa lente - depois de meses achando que a profissão ia desaparecer - o que vejo é quase o oposto. A IA tornou visível uma dimensão da profissão que sempre subestimamos: a engenharia de software tem uma natureza profundamente linguística. Agora que a máquina assume o rascunho, nos cabe o trabalho que sempre foi o mais difícil: garantir que o código diz o que precisa dizer.

Na linguística e na teoria literária, esse trabalho tem nome: exegese - a interpretação crítica de um texto, buscando significado que não está na superfície. É exatamente o que fazemos ao ler código gerado por IA.

## O Código como Ato Performativo

Para entender por que revisar código de IA é tão diferente de revisar um documento qualquer, vale recorrer a uma distinção da filosofia da linguagem.

J.L. Austin, em Quando Dizer é Fazer (How to Do Things with Words), separou dois tipos de expressão. Uma constativa descreve a realidade: "a porta está aberta". Uma performativa altera a realidade: "declaro a sessão aberta". O performativo não é verdadeiro ou falso - ele é bem-sucedido ou fracassado. E o seu sucesso depende de contexto, autoridade e intenção.

Código é performativo. Um UPDATE no banco não descreve uma mudança - ele causa a mudança. Um deploy não relata que o sistema mudou - ele muda o sistema. Cada linha de código que chega à produção é um ato que altera o estado do mundo.

Austin distinguiu duas camadas em todo ato de fala: o locutório - a frase em si, gramaticalmente correta - e o ilocucionário - a intenção por trás dela, o que o falante pretende causar.

É aqui que a IA se torna interessante. Um LLM produz atos locutórios com competência impressionante: código sintaticamente correto, idiomático, que compila e frequentemente roda. Mas um LLM não tem intenção. Ele não pretende nada ao gerar um UPDATE - ele calcula a sequência mais provável de tokens. O ato ilocucionário está ausente.

Quem atribui a intenção somos nós. Quando revisamos o código gerado por IA e decidimos que aquele UPDATE deve ir para produção, somos nós que assumimos a força ilocucionária do ato: "este comando expressa a minha intenção, e eu me responsabilizo pelo que ele vai causar no sistema".

## Frames: Quando a Sintaxe Está Certa e o Significado Está Errado

Se Austin explica o que o código faz (performar), falta entender como o significado funciona dentro do código. E já que estamos nessa de desenterrar a faculdade de Letras, trago mais um: a Semântica de Frames de Charles Fillmore.

Fillmore argumentou que nós não entendemos palavras isoladas - entendemos palavras dentro de cenários. A palavra "comprar" ativa automaticamente um frame completo: comprador, vendedor, mercadoria, preço, transação. Você não precisa mencionar todos esses elementos; eles estão implícitos no frame. Quem ouve "ela comprou" já pressupõe que havia um vendedor, um preço e algo sendo vendido.

No código, os mesmos princípios operam. Um `Payment` não é apenas uma classe com atributos. No seu sistema, esse nome carrega um frame inteiro: o que acontece quando dois pagamentos chegam ao mesmo tempo? A mesma requisição pode cobrar duas vezes? Quanto tempo esperar pelo gateway bancário antes de desistir? Como estornar? Quem audita? Nenhum desses elementos precisa estar no nome da classe - mas todo engenheiro experiente que lê `Payment` no seu contexto os ativa instantaneamente.

A IA não faz isso. Ela opera sobre padrões estatísticos do texto de treinamento. Quando gera código para um `Payment`, ela tende a ativar o frame mais comum - o que aparece na maioria dos tutoriais e repositórios públicos. Esse frame é, tipicamente, o mais simples: um objeto com campos, validações básicas, um CRUD. Ele está sintaticamente correto e semanticamente incompleto para o seu sistema.

O resultado é código que passa no compilador mas falha no contexto. A classe `Payment` existe, os tipos estão certos, a API funciona - mas o frame é o de um tutorial, não o de um sistema financeiro real. A concorrência não foi considerada. A idempotência não está lá. O engenheiro que aceita esse código sem checar o frame está importando as premissas de um contexto genérico para um domínio específico.

O trabalho do engenheiro, nesse cenário, é curadoria semântica: verificar se o frame que a IA ativou corresponde ao frame do sistema real. Isso exige conhecimento de domínio que nenhum LLM possui - porque o frame do seu sistema não está nos dados de treinamento. Está na cabeça do time, nas decisões de arquitetura que nunca viraram documentação, na experiência acumulada de quem já viu aquele `Payment` quebrar em produção. Linguagens de programação são lógicas, mas existem para representar um mundo que não é: domínios de negócio são subjetivos, contextuais, cheios de exceções que nenhum tutorial prevê. Esse conhecimento se constrói vivendo o sistema, não treinando sobre código público.

## O Que Isso Muda na Prática

Se código é ato performativo e significado depende de frames, o papel do engenheiro na era da IA se redefine em termos concretos:

**Ler código de IA é mais exigente do que escrever o próprio.** Quando você escreve, externaliza um modelo mental que já possui - a intenção e o frame já estão na sua cabeça. Quando lê código gerado por IA, você precisa reconstruir um modelo mental que nunca existiu. Não há intenção para rastrear, não há autor para consultar.

Você constrói o significado sozinha, e depois valida se esse significado é compatível com o seu sistema. Isso é cognitivamente mais caro - não por incompetência, mas pela natureza do processo.

**Tenho a impressão de que sênioridade, hoje, se mede mais pela capacidade de ler do que de escrever.** A máquina é muito boa em gerar código sintaticamente correto. O que ela não faz é duvidar do próprio output. Um engenheiro júnior e um sênior recebem o mesmo código gerado por IA - a diferença é o que cada um pergunta antes de aceitar. O júnior pergunta "funciona?". O sênior pergunta "funciona no meu contexto?". Essa segunda pergunta é inteiramente linguística: é uma verificação de frame.

**O valor está na precisão semântica, não na velocidade de produção.** A pergunta que define um engenheiro deixou de ser "quão rápido você entrega código?" e passou a ser "quão preciso é o seu entendimento do que esse código faz no sistema?". Escrever rápido virou commodity. Ler com precisão é habilidade rara e difícil de desenvolver.

Não acredito que estamos perdendo relevância. Acredito que estamos sendo forçados a exercer a parte do trabalho que sempre foi a mais difícil e a mais valiosa - e que, por ser invisível, nunca recebeu o reconhecimento que merecia. A IA não nos substituiu. Ela tornou impossível fingir que digitar era a parte que importava.

E se você é como eu, que ainda sente prazer genuíno em escrever código do zero - esse prazer não precisa desaparecer. Mas o que fazemos com ele, e como ele se encaixa numa rotina cada vez mais exegética, é uma conversa para outro texto.
