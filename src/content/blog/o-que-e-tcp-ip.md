---
title: "O que é TCP/IP?"
description: "TCP/IP é uma das principais coleções de protocolos para o envio e recebimento de dados entre máquinas conectadas a internet."
pubDate: 2020-08-12
category: "professional"
lang: "pt"
tags: ["redes", "protocolos", "iniciante"]
series: "início de jornada"
---

> *Post migrado do Medium. Algumas imagens do artigo original não foram transferidas, o que pode afetar a compreensão de trechos específicos.*

TCP/IP é uma das principais coleções de protocolos para o envio e recebimento de dados entre máquinas conectadas a internet. TCP é a sigla para Transmission Control Protocol (Protocolo de Controle de Transmissão) e IP Internet Protocol (Protocolo de Internet). TCP/IP foi criado por dois cientistas do Departamento de Defesa dos Estados Unidos, Vint Cerf e Bob Kahn, em 1969.

Protocolo nada mais é do que uma série de procedimentos e convenções adotadas, de modo a padronizar as operações. Podemos comparar o protocolo a uma linguagem. Mesmo que dois computadores estejam conectados a mesma rede, a única maneira de se comunicarem entre si é garantindo que falem a mesma "língua". O protocolo permite às aplicações dos dois computadores a conversarem entre si.

## Pilha de protocolos

TCP/IP na realidade é uma pilha de protocolos. Este modelo é dividido entre 4 camadas, cada uma com sua própria função. Essa divisão em camadas existe de forma a garantir que os dados trafeguem com integridade pela rede. Todas as camadas trabalham ativamente para transmitir os dados em segurança de uma camada a outra.

### Camada Aplicação

Esta camada faz a comunicação entre os programas de software e os protocolos de transporte no TCP/IP. Quando você solicita ao seu servidor de internet para fazer o download de uma página da internet, você está fazendo uma solicitação à camada de aplicação, que nesse caso usa o protocolo http. Mesmo caso quando você solicita ao seu cliente de e-mail para fazer o download de seus e-mails pessoais. Essa solicitação é feita pela camada de aplicação usando um outro protocolo chamado de SMTP.

Exemplos de protocolos na camada de aplicação:

- **HTTP (Hypertext Transfer Protocol)** — É a fundação da WWW. É usado para transferir páginas da internet do servidor web ao cliente. Sempre que você usa Google Chrome ou Firefox, por exemplo, você está usando um servidor web.
- **SMTP (Simple Mail Transfer Protocol)** — Esse protocolo auxilia o envio de dados para outros endereços de e-mail.
- **DNS (Domain Name System)** — DNS é o que permite aos usuários se conectarem a sites usando nomes de domínio em vez de endereços IP. O site 'www.globo.com', por exemplo, é uma DNS. O endereço real desse endereço na rede é o seu endereço IP que consiste de um bando de números. A DNS existe para facilitar a vida do usuário.
- **FTP (File Transfer Protocol)** — Protocolo usado para transmitir arquivos de uma máquina a outra.

### Camada de Transporte

Essa camada é responsável por receber os pacotes enviados pela camada de aplicação e transformá-los em pacotes menores, para serem repassados para a camada Internet logo embaixo. Essa camada verifica a integridade dos dados e se certifica que eles serão repassados sem erros. É formado por dois protocolos, UDP e TCP. Tanto o TCP e o UDP recebem o dado da camada de aplicação e acrescentam um endereço virtual chamado cabeçalho.

### Camada de Internet

Responsável pelo roteamento e endereçamento do pacote, fazendo a conexão entre as redes locais. Adiciona ao pacote o endereço IP de origem e destino para que ele saiba para onde deve ir. A camada de internet quebra os pacotes vindo da camada de transporte em pedaços chamado datagramas. Os datagramas são então enviados para a camada abaixo, interface, e transmitidos pelo cabeamento da rede através de quadros.

### Camada de Interface com a rede

Essa camada é responsável pelo envio do datagrama recebido da camada de internet em forma de quadros através da rede física. O Ethernet é o protocolo mais utilizado.

Elementos Ethernet:

- **Logic Link Control (LLC)** — A LLC trata da comunicação entre as camadas superiores e as camadas inferiores. LLC obtém os dados do protocolo de rede, e adiciona informações de controle para ajudar o pacote a ser entregue ao nó de destino.
- **Media Access Control (MAC)** — Monta o quadro que vai ser enviado pela rede. MAC é o endereço físico da placa de rede.

## Concluindo

Em resumo, o TCP/IP especifica como os dados são trocados pela Internet. Ele fornece comunicações de ponta a ponta. Ele identifica como elas devem ser divididas em pacotes, endereçados, transmitidos, roteados e recebidos no destino. O TCP/IP requer pouco gerenciamento central e é projetado para tornar as redes confiáveis. Com ele, é possível a recuperação automática da falha de qualquer dispositivo na rede.
