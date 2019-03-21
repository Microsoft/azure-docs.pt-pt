---
title: Descrição geral do AMQP 1.0 no Azure Service Bus | Documentos da Microsoft
description: Saiba como utilizar o Advanced Message colocação Protocol (AMQP) 1.0 no Azure.
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
ms.openlocfilehash: 34829482e570354c1ab1e1fd6cec0c96b993cd83
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167036"
---
# <a name="amqp-10-support-in-service-bus"></a>Suporte de AMQP 1.0 no Service Bus
O serviço de nuvem do Azure Service Bus e no local [Service Bus para Windows Server (1.1 do barramento de serviço)](https://msdn.microsoft.com/library/dn282144.aspx) suportam o Advanced Message colocação em fila Protocol (AMQP) 1.0. AMQP permite-lhe criar para várias plataformas, aplicações híbridas com um protocolo de norma aberta. Pode construir aplicativos usando componentes que são criadas através de linguagens e estruturas diferentes e que executam sistemas operacionais. Todos esses componentes podem ligar-se ao Service Bus e perfeitamente trocam mensagens comerciais estruturados com eficiência e em total fidelidade.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: O que é o AMQP 1.0 e por que é importante?
Tradicionalmente, os produtos de middleware orientado a mensagens usaram protocolos proprietários para comunicação entre aplicativos de cliente e mediadores. Isso significa que depois de selecionar Mediador de mensagens de um fornecedor específico, tem de utilizar bibliotecas esse fornecedor para ligar as suas aplicações de cliente para esse agente. Isso resulta num grau de dependência do fornecedor, uma vez que portar um aplicativo para outro produto requer alterações de código em todos os aplicativos conectados. 

Além disso, a conexão mediadores de mensagens de diferentes fornecedores é complicado. Isso normalmente requer a nível de aplicativo ponte para mover as mensagens a partir de um sistema para outro e a tradução entre os formatos de mensagem proprietárias. Este é um requisito comum; Por exemplo, quando deve fornecer uma nova interface unificada de mais antigos sistemas diferentes, ou integrar sistemas de TI uma fusão a seguir.

O setor de software é uma empresa de rápida evolução; linguagens de programação novos e estruturas de aplicativo são introduzidas num ritmo por vezes desconcertante. Da mesma forma, os requisitos dos sistemas de TI evoluam ao longo do tempo e os desenvolvedores querem tirar partido das funcionalidades de plataforma mais recente. No entanto, às vezes, o fornecedor de mensagens selecionado não suporta estas plataformas. Como os protocolos de mensagens são proprietários, não é possível que outras pessoas fornecer bibliotecas para essas novas plataformas. Por conseguinte, tem de utilizar abordagens, tais como a criação de gateways ou pontes que permitem-lhe continuar a utilizar o produto de mensagens.

O desenvolvimento do Advanced Message colocação Protocol (AMQP) 1.0 foi motivado por esses problemas. Foi gerado em JP Morgan ponto, o que, como as empresas de serviços financeiras mais, são usuárias que fazem intenso de middleware orientado a mensagens. O objetivo era simple: para criar um protocolo de mensagens de padrão aberto que tenha possibilitado a criar aplicativos baseados em mensagens usando componentes criados com diferentes idiomas, arquiteturas e sistemas operacionais, tudo isso usando componentes de melhor entre uma gama de fornecedores.

## <a name="amqp-10-technical-features"></a>Recursos técnicos do AMQP 1.0
AMQP 1.0 é um protocolo de mensagens eficiente, fiável e ao nível da transmissão que pode usar para criar robusta e para várias plataformas, aplicações de mensagens. O protocolo tem um objetivo simple: definir a mecânica da transferência segura, fiável e eficiente de mensagens entre duas partes. As mensagens propriamente ditas são codificadas usando uma representação de dados portátil que permite a heterogêneos remetentes e recetores trocar mensagens comerciais estruturados em total fidelidade. Segue-se um resumo dos recursos mais importantes:

* **Eficiente**: AMQP 1.0 é um protocolo orientado por conexão que utiliza um binário de codificação para as instruções de protocolo e as mensagens de negócios transferida sobre ele. Ele incorpora a esquemas de controlo de fluxo sofisticadas para maximizar a utilização de rede e os componentes conectados. Dito isso, o protocolo foi projetado para encontrar o equilíbrio entre a eficiência, flexibilidade e interoperabilidade.
* **Fiável**: O protocolo AMQP 1.0 permite que as mensagens a serem trocados com uma variedade de garantias de confiabilidade, de disparar e esquecer para fiável, exatamente-uma vez confirmadas entrega.
* **Flexível**: AMQP 1.0 é um protocolo flexível que pode ser usado para oferecer suporte a diferentes topologias. O mesmo protocolo pode ser utilizado para comunicações de cliente a cliente, o agente de cliente e o Mediador de Mediador.
* **Independente de modelo de Mediador**: A especificação de AMQP 1.0 não faz quaisquer requisitos no modelo de mensagens utilizado por um mediador. Isso significa que é possível adicionar facilmente o suporte de AMQP 1.0 para mediadores de mensagens existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é um padrão (com uma capital ")
AMQP 1.0 é um padrão internacional, aprovado por ISO e IEC como ISO/IEC 19464:2014.

AMQP 1.0 foi no desenvolvimento desde 2008 por um grupo de núcleo de mais de 20 empresas, os fornecedores de tecnologia e as empresas de utilizador final. Durante esse tempo, as empresas de utilizador têm contribuído seus requisitos comerciais do mundo real e os fornecedores de tecnologia se desenvolveram o protocolo para atender a esses requisitos. Ao longo do processo, os fornecedores têm participado em workshops em que eles colaborou para validar a interoperabilidade entre suas implementações.

Em Outubro de 2011, o trabalho de desenvolvimento transitado para um comitê técnico dentro da organização para o avanço do estruturados informações padrões OASIS () e o padrão OASIS AMQP 1.0 foi lançado em Outubro de 2012. As empresas a seguintes participaram no comitê técnico durante o desenvolvimento do padrão:

* **Fornecedores de tecnologia**: Software de Axway, Huawei tecnologias, IIT Software, INETCO sistemas, Kaazing, Microsoft, Mitre Corporation, Primeton tecnologias, progresso Software, Red Hat, SITA, Software AG, Solace sistemas, VMware, WSO2, Zenika.
* **As empresas de utilizador**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Alguns dos benefícios citado com freqüência de padrões abertos incluem:

* Menos probabilidade de bloqueio-in do fornecedor
* Interoperabilidade
* Ampla disponibilidade de bibliotecas e ferramentas
* Proteção contra a obsolescência
* Disponibilidade da Equipe experiente
* Inferior e gerenciável de risco

## <a name="amqp-10-and-service-bus"></a>Barramento AMQP 1.0 e de serviço
Suporte de AMQP 1.0 no Azure Service Bus significa que pode agora tirar partido da colocação do Service Bus e de publicação/subscrição funcionalidades de mensagens mediadas entre uma variedade de plataformas através de um protocolo binário eficiente. Além disso, pode criar aplicativos compostos por componentes criados usando uma combinação de linguagens, arquiteturas e sistemas operativos.

A figura a seguir ilustra um exemplo de implementação em que os clientes de Java em execução no Linux, escrito usando o padrão Java Message Service (JMS) API e os clientes de .NET em execução no Windows, trocam mensagens através do Service Bus através de AMQP 1.0.

![][0]

**Figura 1: Cenário de implementação de exemplo que mostra várias plataformas, mensagens usando o Service Bus e AMQP 1.0**

Neste momento, as seguintes bibliotecas de cliente são conhecidas para trabalhar com o Service Bus:

| Idioma | Biblioteca |
| --- | --- |
| Java |Cliente de Apache Qpid Java Message Service (JMS)<br/>Cliente de IIT Software SwiftMQ Java |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |.NET de AMQP Lite |

**Figura 2: Tabela de bibliotecas de cliente AMQP 1.0**

## <a name="summary"></a>Resumo
* AMQP 1.0 é um protocolo de mensagens confiável, aberto, que pode utilizar para criar para várias plataformas, aplicações híbridas. AMQP 1.0 é um padrão OASIS.
* Suporte de AMQP 1.0 já está disponível no Azure Service Bus, bem como o Service Bus para Windows Server (1.1 do barramento de serviço). O preço é igual para os protocolos existentes.

## <a name="next-steps"></a>Passos Seguintes
Pronto para saber mais? Visite as seguintes ligações:

* [Utilizar o Service Bus do .NET com AMQP]
* [Utilizar o Service Bus do Java com AMQP]
* [Instalar o Apache Qpid Proton-C numa VM do Linux do Azure]
* [AMQP no Service Bus para o Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Utilizar o Service Bus do .NET com AMQP]: service-bus-amqp-dotnet.md
[Utilizar o Service Bus do Java com AMQP]: service-bus-amqp-java.md
[Instalar o Apache Qpid Proton-C numa VM do Linux do Azure]: service-bus-amqp-apache.md
[AMQP no Service Bus para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
