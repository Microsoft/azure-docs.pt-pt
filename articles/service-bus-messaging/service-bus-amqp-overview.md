---
title: Visão geral da AMQP 1.0 no ônibus de serviço azure
description: Saiba como o Azure Service Bus suporta o Protocolo de Fila de Mensagens Avançadas (AMQP), um protocolo padrão aberto.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759317"
---
# <a name="amqp-10-support-in-service-bus"></a>Suporte AMQP 1.0 em Ônibus de serviço
Tanto o serviço de nuvem de ônibus de serviço Azure e o ônibus de serviço no local [para o Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) suportam o Protocolo avançado de fila de mensagens (AMQP) 1.0. A AMQP permite-lhe construir aplicações híbridas e de plataforma cruzada utilizando um protocolo padrão aberto. Pode construir aplicações utilizando componentes que são construídos utilizando diferentes línguas e quadros, e que funcionam em diferentes sistemas operativos. Todos estes componentes podem ligar-se ao Service Bus e trocar mensagens de negócio estruturadas de forma eficiente e plena.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: O que é AMQP 1.0 e por que é importante?
Tradicionalmente, os produtos de middleware orientados para mensagens têm usado protocolos proprietários para comunicação entre aplicações de clientes e corretores. Isto significa que, uma vez selecionado um corretor de mensagens de um determinado fornecedor, deve utilizar as bibliotecas desse fornecedor para ligar as suas aplicações de cliente a esse corretor. Isto resulta num grau de dependência desse fornecedor, uma vez que a aplicação de um produto diferente requer alterações de código em todas as aplicações conectadas. 

Além disso, ligar corretores de mensagens de diferentes fornecedores é complicado. Isto normalmente requer pontes de nível de aplicação para mover mensagens de um sistema para outro e traduzir entre os seus formatos de mensagem proprietária. Trata-se de um requisito comum; por exemplo, quando deve fornecer uma nova interface unificada a sistemas díspares mais antigos ou integrar sistemas informáticos após uma fusão.

A indústria de software é um negócio em rápida mudança; novas linguagens de programação e quadros de aplicação são introduzidos a um ritmo por vezes desconcertante. Da mesma forma, os requisitos dos sistemas de TI evoluem ao longo do tempo e os desenvolvedores querem aproveitar as funcionalidades mais recentes da plataforma. No entanto, por vezes, o fornecedor de mensagens selecionado não suporta estas plataformas. Como os protocolos de mensagens são proprietários, não é possível que outros forneçam bibliotecas para estas novas plataformas. Por isso, deve utilizar abordagens como construir gateways ou pontes que lhe permitam continuar a utilizar o produto de mensagens.

O desenvolvimento do Protocolo avançado de Fila de Mensagens (AMQP) 1.0 foi motivado por estas questões. Teve origem no JP Morgan Chase, que, tal como a maioria das empresas de serviços financeiros, são utilizadores pesados de middleware orientado para mensagens. O objetivo era simples: criar um protocolo de mensagens de padrão aberto que possibilitasse a construção de aplicações baseadas em mensagens utilizando componentes construídos utilizando diferentes línguas, quadros e sistemas operativos, todos utilizando componentes de melhor raça de uma série de fornecedores.

## <a name="amqp-10-technical-features"></a>Características técnicas AMQP 1.0
AmQP 1.0 é um protocolo de mensagens eficiente, fiável e de nível de fio que pode usar para construir aplicações de mensagens robustas, interplataformas e de mensagens. O protocolo tem um objetivo simples: definir a mecânica da transferência segura, fiável e eficiente de mensagens entre duas partes. As mensagens em si são codificadas usando uma representação de dados portáteis que permite que remetedores e recetores heterogéneos troquem mensagens de negócio estruturadas com plena fidelidade. Segue-se um resumo das características mais importantes:

* **Eficiente**: AMQP 1.0 é um protocolo orientado para a ligação que utiliza uma codificação binária para as instruções protocolares e as mensagens de negócio transferidas sobre ele. Incorpora esquemas sofisticados de controlo de fluxos para maximizar a utilização da rede e dos componentes conectados. Dito isto, o protocolo foi concebido para encontrar um equilíbrio entre eficiência, flexibilidade e interoperabilidade.
* **Fiável**: O protocolo AMQP 1.0 permite que as mensagens sejam trocadas com uma gama de garantias de fiabilidade, desde fogo e esquecimento a entregas fiáveis e exatamente reconhecidas.
* **Flexível**: AMQP 1.0 é um protocolo flexível que pode ser usado para suportar diferentes topologias. O mesmo protocolo pode ser usado para comunicações cliente-a-cliente, cliente-a-corretor e broker-to-broker.
* **Modelo de corretor independente**: A especificação AMQP 1.0 não faz quaisquer requisitos no modelo de mensagens utilizado por um corretor. Isto significa que é possível adicionar facilmente suporte AMQP 1.0 aos corretores de mensagens existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é um Standard (com um 'S' maiúsculo)
AmQP 1.0 é uma norma internacional, aprovada pela ISO e pela IEC como ISO/IEC 19464:2014.

A AMQP 1.0 está em desenvolvimento desde 2008 por um grupo central de mais de 20 empresas, tanto fornecedores de tecnologia como empresas de utilizadores finais. Durante esse tempo, as empresas utilizadoras contribuíram com os seus requisitos empresariais no mundo real e os fornecedores de tecnologia desenvolveram o protocolo para satisfazer esses requisitos. Ao longo do processo, os fornecedores participaram em workshops em que colaboraram para validar a interoperabilidade entre as suas implementações.

Em outubro de 2011, o trabalho de desenvolvimento transitou para um comité técnico da Organização para o Avanço das Normas estruturadas de Informação (OASIS) e a Norma OASIS AMQP 1.0 foi lançada em outubro de 2012. As seguintes empresas participaram no comité técnico durante o desenvolvimento da norma:

* **Fornecedores de tecnologia**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Empresas de utilizadores**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Alguns dos benefícios geralmente citados de normas abertas incluem:

* Menos hipóteses de bloqueio do vendedor
* Interoperabilidade
* Ampla disponibilidade de bibliotecas e ferramentas
* Proteção contra a obsolescência
* Disponibilidade de pessoal conhecedor
* Risco mais baixo e manejável

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Ônibus de Serviço
Suporte AMQP 1.0 no Ônibus de Serviço Azure significa que agora pode aproveitar as funcionalidades de fila de ônibus de serviço e publicar/subscrever funcionalidades de mensagens intermediadas de uma série de plataformas usando um protocolo binário eficiente. Além disso, pode construir aplicações compostas por componentes construídos utilizando uma mistura de línguas, quadros e sistemas operativos.

A figura que se segue ilustra uma implementação de exemplo em que os clientes da Java em execução no Linux, escritos utilizando os clientes Padrão Java Message Service (JMS) API e .NET que executam no Windows, trocam mensagens através do Service Bus utilizando amqP 1.0.

![][0]

**Figura 1: Exemplo de implementação mostrando mensagens cross-platform usando Service Bus e AMQP 1.0**

Neste momento, as seguintes bibliotecas de clientes são conhecidas por trabalhar com a Service Bus:

| Idioma | Biblioteca |
| --- | --- |
| Java |Cliente apache Qpid Java Message Service (JMS)<br/>IIT Software SwiftMQ Java cliente |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Figura 2: Tabela das bibliotecas de clientes AMQP 1.0**

## <a name="summary"></a>Resumo
* AmQP 1.0 é um protocolo de mensagens aberto e fiável que pode usar para construir aplicações híbridas de plataforma cruzada. AMQP 1.0 é um padrão OASIS.
* O suporte AMQP 1.0 já está disponível no Azure Service Bus, bem como no Ônibus de Serviço para O Servidor windows (Ônibus de Serviço 1.1). Os preços são os mesmos que para os protocolos existentes.

## <a name="next-steps"></a>Passos seguintes
Está pronto para saber mais? Visite os seguintes links:

* [Utilização do ônibus de serviço de .NET com AMQP]
* [Utilização de ônibus de serviço de Java com AMQP]
* [Instalação de Apache Qpid Proton-C num VM Azure Linux]
* [AMQP em ônibus de serviço para servidor windows]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Utilização do ônibus de serviço de .NET com AMQP]: service-bus-amqp-dotnet.md
[Utilização de ônibus de serviço de Java com AMQP]: service-bus-amqp-java.md
[Instalação de Apache Qpid Proton-C num VM Azure Linux]: service-bus-amqp-apache.md
[AMQP em ônibus de serviço para servidor windows]: https://msdn.microsoft.com/library/dn574799.aspx
