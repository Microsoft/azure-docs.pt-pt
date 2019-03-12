---
title: Descrição geral dos escalões de preços das Mensagens Standard e Premium do Azure Service Bus | Microsoft Docs
description: Escalões de Mensagens Standard e Premium do Service Bus
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 9e9c8918556b7ff003bcfed062ea1e15233b2845
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761961"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Escalões de mensagens Standard e Premium do Service Bus

As Mensagens do Service Bus, que incluem entidades como filas e tópicos, combinam capacidades de mensagens empresariais com semântica de publicação-subscrição avançada à escala da cloud. As Mensagens do Service Bus são utilizadas como base de comunicação para várias soluções de cloud sofisticadas.

O escalão *Premium* das Mensagens do Service Bus atende pedidos de cliente comuns relativamente a dimensionamento, desempenho e disponibilidade para aplicações fundamentais. O escalão Premium é recomendado para os cenários de produção. Embora os conjuntos de funcionalidades sejam praticamente idênticos, estes dois escalões de Mensagens do Service Bus foram concebidos para serem utilizados em situações diferentes.

Na tabela seguinte, destacam-se algumas das principais diferenças.

| Premium | Standard |
| --- | --- |
| Débito elevado |Débito variável |
| Desempenho previsível |Latência variável |
| Preços fixos |Preços variáveis de utilização |
| Possibilidade de aumentar e reduzir verticalmente a carga de trabalho |N/D |
| Tamanho da mensagem até 1 MB |Tamanho da mensagem até 256 KB |

As **Mensagens Premium do Service Bus** fornecem isolamento de recursos no nível de CPU e memória para que cada carga de trabalho do cliente seja executada de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades de mensagens para cada espaço de nomes Premium do Service Bus. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora a faturação seja realizada com base em tarifas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus.

Este desempenho não só é mais previsível e repetível, como também mais rápido. As Mensagens do Service Bus Premium baseiam-se no motor de armazenamento introduzido nos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Com as Mensagens Premium, obter o máximo rendimento é muito mais rápido do que com o escalão Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas das Mensagens Premium

As seguintes secções abordam as diferenças entre as camadas de mensagens Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

As filas e tópicos particionados não são suportados nas Mensagens Premium. Para mais informações sobre a criação de partições, consulte o artigo [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Uma vez que as mensagens Premium se executam num ambiente de tempo de execução completamente isolado, as entidades expressas deixam de ser suportadas nos espaços de nome Premium. Para obter mais informações sobre a funcionalidade Express, veja a propriedade [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se tiver código em execução nas mensagens Standard e quiser transportar para o escalão Premium, certifique-se de que a propriedade [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) está definida como **falso** (o valor predefinido).

## <a name="premium-messaging-resource-usage"></a>Utilização de recursos de mensagens Premium
Em geral, qualquer operação numa entidade pode causar a utilização da CPU e memória. Aqui estão algumas dessas operações: 

- Operações de gestão, tais como CRUD (Create, Retrieve, Update e Delete) operações em filas, tópicos e subscrições.
- Operações de tempo de execução (enviar e receber mensagens)
- Operações de monitorização e alertas

A utilização de memória adicional da CPU e não o preço é adicionalmente entanto. Para o escalão de mensagens Premium, existe um preço individual para a unidade de mensagens.

A utilização de CPU e memória sejam rastreadas e apresentadas para si, pelos seguintes motivos: 

- Forneça transparência para os aspectos internos do sistema
- Compreenda a capacidade de recursos comprados.
- Planeamento de capacidade ajuda-o a optar por aumentar/reduzir verticalmente.

## <a name="get-started-with-premium-messaging"></a>Introdução às Mensagens Premium

A introdução às Mensagens Premium é simples e o processo é semelhante ao das Mensagens Standard. Comece por [criar um espaço de nomes](service-bus-create-namespace-portal.md) no [portal do Azure](https://portal.azure.com). Certifique-se de que seleciona **Premium**, em **Escalão de preço**. Clique em **Ver informações completas de preços** para obter mais informações sobre cada escalão.

![create-premium-namespace][create-premium-namespace]

Também pode criar [espaços de nomes Premium com modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Mensagens do Service Bus, veja as ligações seguintes:

* [Introdução às Mensagens Premium do Azure Service Bus (mensagem de blogue)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução às Mensagens Premium do Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Descrição geral de Mensagens do Service Bus](service-bus-messaging-overview.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
