---
title: "Git, Github e sistemas de controle de versão"
description: "Afinal o que é o que?"
pubDate: 2020-08-20
category: "professional"
lang: "pt"
tags: ["git", "github", "iniciante"]
series: "início de jornada"
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

## O que é controle de versão e sua relevância na vida de um desenvolvedor de software

O controle de versão soluciona um problema que talvez seja familiar a você. Você alguma vez já viu ou salvou um trabalho da escola como trabalho-historia-versao1, trabalho-historia-versao2, trabalho-historia-versao3, trabalho-historia-versaoagoravai? Um sistema de controle de versão, ou VCS (version control system), nos proporciona uma maneira automática de acompanhar as mudanças em projetos de software, dando ao desenvolvedor a possibilidade de rever versões antigas do documento, e a liberdade de poder criar recursos especulativos sem atrapalhar o desenvolvimento do projeto principal. O VCS salva um backup do projeto e todo o seu histórico de mudanças, facilitando assim o trabalho em grupo e salvando seu pescoço no caso de um deslize ocasional hehe

Os sistemas de controle de versão evoluíram muito com o passar do tempo e muitos sistemas similares antecederam o Git, como é o caso do RCS, CVS e Subversion. Existem também alternativas hoje em dia como Bazaar, Mercurial e Perforce. Porém o Git é disparado o maior em popularidade, e uma ferramenta exigida pela maioria dos empregadores atualmente.

## Git e Github são a mesma coisa?

Git foi originalmente desenvolvido por Linus Torvald, papai do Kernel Linux, depois que cortou relações com a empresa Bitkeeper. Cheio de ódio no seu coraçãozinho, Linus estava decidido a criar sua própria ferramenta, e assim o fez, utilizando tudo o que tinha aprendido com o BitKeeper como base. Linus tinha como objetivo a criação de um sistema rápido, com design simples, que suportasse um desenvolvimento não-linear e que fosse distribuído gratuitamente. Git assim como o Linux, foi baseado em sistemas Unix, portanto um bom comando do terminal é essencial para dominar a ferramenta. Git foi desenvolvido em C, Shell e Perl.

Nasceu assim o Git em 2005, fruto da criatividade destrutiva, assim como outras coisas belas da vida.

## Qual a diferença de Git para outros VCS?

A maior diferença entre Git e outros sistemas VCS é a maneira como Git lida com seus dados. Outros sistemas guardam informações em uma lista de arquivos. Eles armazenam diversas versões do mesmo arquivo de acordo com as mudanças feitas com o passar do tempo. Git por outro lado, armazena seus dados com uma série de "fotografias" de um sistema de arquivos miniatura. Toda vez que você der commit ou salvar o estado do seu projeto, Git "tira uma fotografia" da aparência do seu arquivo naquele momento e você pode acessá-lo através de uma referência àquela "fotografia". Git não salva o arquivo de novo e de novo, ele salva apenas um link ao arquivo anterior que já tinha sido armazenado.

## O que diabos é o Github então?

Nessa discussão de Git versus Github, já vi escrito por ae que Github é para o Git o que o Facebook é para o seu rosto (do inglês face) de verdade. E o que isso significa? Significa que o Facebook é como se fosse um banco de dados de "rostos". E o Github é um serviço de hospedagem dos repositórios do Git.

Lembra que eu mencionei que através do Git podemos salvar nossos arquivos e todo o histórico de todas as modificações que foram feitas com o passar do tempo? O Github é um site de hospedagem de repositórios, de modo que outras pessoas que tenham acesso a internet possam ter acesso aos seus arquivos também. O Git existe exclusivamente no seu computador ou servidor local. Github é exclusivamente hospedado na nuvem. Diferente de Git, Github não é de graça, apesar de disponibilizar alguns recursos básicos sem custo.

A grande sacada do Github e sua principal vantagem é que ele apresenta uma interface de usuário extremamente intuitiva e graficamente atraente, ao contrário de Git que é acessado pelo terminal de comando. O fato do Github usar hospedagem em nuvem também é uma vantagem, visto que qualquer programador autorizado pode acessar ao repositório, fazer revisões e modificações, independente do lugar do mundo em que esteja.

Outros hospedeiros de repositórios Git existem por aí: Gitlab, Bitbucket e SourceForge são todas alternativas viáveis ao Github.
