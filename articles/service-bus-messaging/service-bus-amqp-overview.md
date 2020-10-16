---
title: Visão geral do AMQP 1.0 em Azure Service Bus
description: Saiba como o Azure Service Bus suporta o Protocolo avançado de Fila de Mensagens (AMQP), um protocolo padrão aberto.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: c91c7965b94216f3f3bcb47e0cb652ce22a0217a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066343"
---
# <a name="amqp-10-support-in-service-bus"></a>Suporte AMQP 1.0 em Service Bus
Tanto o serviço de nuvem de autocarros Azure Service e no [local, Service Bus for Windows Server (Service Bus 1.1)](/previous-versions/service-bus-archive/dn282144(v=azure.100)) suportam o Protocolo avançado de fila de mensagens (AMQP) 1.0. A AMQP permite-lhe construir aplicações híbridas e inter-plataforma usando um protocolo padrão aberto. Pode construir aplicações utilizando componentes que são construídos usando diferentes idiomas e quadros, e que funcionam em diferentes sistemas operativos. Todos estes componentes podem ligar-se ao Service Bus e trocar sem problemas mensagens de negócio estruturadas de forma eficiente e plena.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: O que é AMQP 1.0 e por que é importante?
Tradicionalmente, os produtos de middleware orientados por mensagens têm usado protocolos proprietários para comunicação entre aplicações de clientes e corretores. Isto significa que uma vez selecionado um corretor de mensagens de um determinado fornecedor, deve usar as bibliotecas desse fornecedor para ligar as aplicações do seu cliente a esse corretor. Isto resulta num grau de dependência do fornecedor, uma vez que a aplicação a um produto diferente requer alterações de código em todas as aplicações conectadas. 

Além disso, ligar corretores de mensagens de diferentes fornecedores é complicado. Isto normalmente requer pontes ao nível da aplicação para mover mensagens de um sistema para outro e traduzir entre os seus formatos de mensagens proprietárias. Trata-se de um requisito comum; por exemplo, quando deve fornecer uma nova interface unificada para sistemas diferentes mais antigos, ou integrar sistemas de TI após uma fusão.

A indústria de software é um negócio em rápida mudança; novas linguagens de programação e quadros de aplicação são introduzidos a um ritmo por vezes desconcertante. Da mesma forma, os requisitos dos sistemas de TI evoluem ao longo do tempo e os desenvolvedores querem tirar partido das funcionalidades mais recentes da plataforma. No entanto, por vezes, o fornecedor de mensagens selecionados não suporta estas plataformas. Como os protocolos de mensagens são proprietários, não é possível que outros forneçam bibliotecas para estas novas plataformas. Por isso, deve utilizar abordagens como construir portais ou pontes que lhe permitam continuar a utilizar o produto de mensagens.

O desenvolvimento do Protocolo avançado de Filamento de Mensagens (AMQP) 1.0 foi motivado por estas questões. Originou-se na JP Morgan Chase, que, tal como a maioria das empresas de serviços financeiros, são utilizadores pesados de middleware orientado para mensagens. O objetivo era simples: criar um protocolo de mensagens de padrão aberto que permitisse construir aplicações baseadas em mensagens utilizando componentes construídos com diferentes idiomas, quadros e sistemas operativos, todos utilizando componentes de melhor raça de uma série de fornecedores.

## <a name="amqp-10-technical-features"></a>Características técnicas AMQP 1.0
AMQP 1.0 é um protocolo de mensagens eficiente, fiável e de nível de fio que você pode usar para construir aplicações de mensagens robustas, inter-plataformas e de mensagens. O protocolo tem um objetivo simples: definir a mecânica da transferência segura, fiável e eficiente de mensagens entre duas partes. As próprias mensagens são codificadas usando uma representação de dados portátil que permite que remetentes e recetores heterogéneos troquem mensagens de negócios estruturadas em plena fidelidade. Segue-se um resumo das características mais importantes:

* **Eficiente**: AMQP 1.0 é um protocolo orientado para a ligação que utiliza uma codificação binária para as instruções do protocolo e as mensagens de negócios transferidas sobre ele. Incorpora sofisticados esquemas de controlo de fluxo para maximizar a utilização da rede e dos componentes conectados. Dito isto, o protocolo foi concebido para encontrar um equilíbrio entre eficiência, flexibilidade e interoperabilidade.
* **Fiável**: O protocolo AMQP 1.0 permite que as mensagens sejam trocadas com uma gama de garantias de fiabilidade, desde o fogo-e-esquecimento até uma entrega fiável e exatamente reconhecida.
* **Flexível**: AMQP 1.0 é um protocolo flexível que pode ser usado para suportar diferentes topologias. O mesmo protocolo pode ser usado para comunicações cliente-a-cliente, cliente-a-corretor e mediador-a-corretor.
* **Modelo de corretor independente**: A especificação AMQP 1.0 não faz quaisquer requisitos no modelo de mensagens utilizado por um corretor. Isto significa que é possível adicionar facilmente suporte AMQP 1.0 a corretores de mensagens existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é um Standard (com um 'S' maiúscula)
AMQP 1.0 é uma norma internacional, aprovada pela ISO e pela IEC como ISO/IEC 19464:2014.

A AMQP 1.0 está em desenvolvimento desde 2008 por um grupo central de mais de 20 empresas, tanto fornecedores de tecnologia como empresas de utilizadores finais. Durante esse período, as empresas utilizadoras contribuíram com os seus requisitos de negócio no mundo real e os fornecedores de tecnologia desenvolveram o protocolo para satisfazer esses requisitos. Ao longo do processo, os fornecedores têm participado em workshops em que colaboraram para validar a interoperabilidade entre as suas implementações.

Em outubro de 2011, o trabalho de desenvolvimento transitou para um comité técnico no âmbito da Organização para o Avanço das Normas Estruturadas de Informação (OASIS) e da Norma OASIS AMQP 1.0 foi lançado em outubro de 2012. As seguintes empresas participaram no comité técnico durante o desenvolvimento da norma:

* **Fornecedores de tecnologia**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Empresas de utilizadores**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Alguns dos benefícios geralmente citados de normas abertas incluem:

* Menos hipóteses de bloqueio do fornecedor
* Interoperabilidade
* Ampla disponibilidade de bibliotecas e ferramentas
* Proteção contra a obsolescência
* Disponibilidade de pessoal experiente
* Risco inferior e manejável

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Autocarro de Serviço
O suporte AMQP 1.0 no Azure Service Bus significa que agora pode aproveitar a fila do Service Bus e publicar/subscrever funcionalidades de mensagens intermediadas a partir de uma série de plataformas usando um protocolo binário eficiente. Além disso, pode construir aplicações compostas por componentes construídos utilizando uma mistura de idiomas, quadros e sistemas operativos.

A seguinte figura ilustra uma implementação de exemplo em que os clientes java que executam no Linux, escritos utilizando os clientes padrão java message Service (JMS) API e .NET que executam no Windows, trocam mensagens através do Service Bus utilizando amQP 1.0.

![Diagrama mostrando um Service Bus trocando mensagens com dois ambientes Linux e dois ambientes Windows.][0]

**Figura 1: Cenário de implantação de exemplo mostrando mensagens trans plataformas usando Service Bus e AMQP 1.0**

Neste momento, as seguintes bibliotecas de clientes são conhecidas por trabalhar com a Service Bus:

| Idioma | Biblioteca |
| --- | --- |
| Java |Cliente do Serviço de Mensagens Apache Qpid Java (JMS)<br/>Cliente do IIT Software SwiftMQ Java |
| C |Apache Qpid Protão-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Proton-Python Apache Qpid |
| C# |AMQP .NET Lite |

**Figura 2: Tabela de bibliotecas de clientes AMQP 1.0**

## <a name="summary"></a>Resumo
* AMQP 1.0 é um protocolo de mensagens aberto e fiável que pode usar para construir aplicações híbridas e inter-plataforma. AMQP 1.0 é um padrão OASIS.
* O suporte AMQP 1.0 já está disponível no Azure Service Bus, bem como no Service Bus para Windows Server (Service Bus 1.1). Os preços são os mesmos que para os protocolos existentes.

## <a name="next-steps"></a>Passos seguintes
Está pronto para saber mais? Visite os seguintes links:

* [Utilização de ônibus de serviço a partir de .NET com AMQP]
* [Ônibus de serviço de Java com AMQP]
* [Instalação apache Qpid Proton-C em um Azure Linux VM]
* [AMQP em ônibus de serviço para windows server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Utilização de ônibus de serviço a partir de .NET com AMQP]: service-bus-amqp-dotnet.md
[Ônibus de serviço de Java com AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md
[Instalação apache Qpid Proton-C em um Azure Linux VM]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)