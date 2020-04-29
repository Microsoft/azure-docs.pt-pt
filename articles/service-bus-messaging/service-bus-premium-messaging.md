---
title: Azure Service Bus premium e níveis padrão
description: Este artigo descreve níveis standard e premium do Azure Service Bus. Compara estes níveis e proporciona diferenças técnicas.
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
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774557"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Escalões de mensagens Standard e Premium do Service Bus

As Mensagens do Service Bus, que incluem entidades como filas e tópicos, combinam capacidades de mensagens empresariais com semântica de publicação-subscrição avançada à escala da cloud. As Mensagens do Service Bus são utilizadas como base de comunicação para várias soluções de cloud sofisticadas.

O nível *Premium* de Mensagens de Ônibus de Serviço aborda pedidos comuns de clientes em escala, desempenho e disponibilidade para aplicações críticas de missão. O escalão Premium é recomendado para os cenários de produção. Embora os conjuntos de funcionalidades sejam praticamente idênticos, estes dois escalões de Mensagens do Service Bus foram concebidos para serem utilizados em situações diferentes.

Na tabela seguinte, destacam-se algumas das principais diferenças.

| Premium | Standard |
| --- | --- |
| Débito elevado |Débito variável |
| Desempenho previsível |Latência variável |
| Preços fixos |Preços variáveis de utilização |
| Possibilidade de aumentar e reduzir verticalmente a carga de trabalho |N/D |
| Tamanho da mensagem até 1 MB |Tamanho da mensagem até 256 KB |

As **Mensagens Premium do Service Bus** fornecem isolamento de recursos no nível de CPU e memória para que cada carga de trabalho do cliente seja executada de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Pode adquirir 1, 2, 4 ou 8 unidades de mensagens para cada espaço de nome Service Bus Premium. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus.

Este desempenho não só é mais previsível e repetível, como também mais rápido. Service Bus Premium Messaging baseia-se no motor de armazenamento introduzido no [Azure Event Hubs.](https://azure.microsoft.com/services/event-hubs/) Com as Mensagens Premium, obter o máximo rendimento é muito mais rápido do que com o escalão Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas das Mensagens Premium

As seguintes secções abordam as diferenças entre as camadas de mensagens Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

As filas e tópicos particionados não são suportados nas Mensagens Premium. Para mais informações sobre a criação de partições, consulte o artigo [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Uma vez que as mensagens Premium se executam num ambiente de tempo de execução completamente isolado, as entidades expressas deixam de ser suportadas nos espaços de nome Premium. Para obter mais informações sobre a funcionalidade Express, veja a propriedade [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se tiver código em execução nas mensagens Standard e quiser transportar para o escalão Premium, certifique-se de que a propriedade [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) está definida como **falso** (o valor predefinido).

## <a name="premium-messaging-resource-usage"></a>Utilização de recursos de mensagens premium
Em geral, qualquer operação numa entidade pode causar CPU e utilização da memória. Aqui estão algumas destas operações: 

- Operações de gestão como operações CRUD (Create, Retrieve, Update e Delete) em filas, tópicos e subscrições.
- Operações de tempo de execução (enviar e receber mensagens)
- Operações e alertas de monitorização

No entanto, o CPU adicional e o uso da memória não têm um preço adicional. Para o nível de Mensagens Premium, há um preço único para a unidade de mensagem.

O CPU e o uso da memória são rastreados e apresentados ao seu trabalho pelas seguintes razões: 

- Proporcionar transparência nos internos do sistema
- Compreender a capacidade dos recursos adquiridos.
- Planeamento de capacidade que o ajuda a decidir escalar para cima/para baixo.

## <a name="messaging-unit---how-many-are-needed"></a>Unidade de mensagens - Quantas são necessárias?

Ao fornecer um espaço de nome Saque Premium de Serviço Azure, o número de unidades de mensagens atribuídas deve ser especificado. Estas unidades de mensagens são recursos dedicados que são atribuídos ao espaço de nome.

O número de unidades de mensagens atribuídas ao espaço de nome premium do bus de serviço pode ser **ajustado dinamicamente** para fator na mudança (aumento ou diminuição) das cargas de trabalho.

Há uma série de fatores a ter em conta na decisão sobre o número de unidades de mensagens para a sua arquitetura:

- Comece com ***1 ou 2 unidades de mensagens atribuídas*** ao seu espaço de nome.
- Estude as métricas de utilização do CPU dentro das métricas de [utilização](service-bus-metrics-azure-monitor.md#resource-usage-metrics) do Recurso para o seu espaço de nome.
    - Se o uso de CPU for inferior a ***20%,*** poderá ***reduzir*** o número de unidades de mensagens atribuídas ao seu espaço de nome.
    - Se o uso do CPU for superior a ***70%,*** a sua aplicação beneficiará de ***aumentar*** o número de unidades de mensagens atribuídas ao seu espaço de nome.

O processo de dimensionamento dos recursos atribuídos a um espaço de nome saquede irremediatos pode ser automatizado utilizando os Runbooks de [Automação Azure.](../automation/automation-quickstart-create-runbook.md)

> [!NOTE]
> **A escala** dos recursos atribuídos ao espaço de nome pode ser preventiva ou reativa.
>
>  * **Preventiva**: Se for esperada uma carga de trabalho adicional (devido à sazonalidade ou tendências), pode proceder à atribuição de mais unidades de mensagens ao espaço de nome antes de as cargas de trabalho atingirem.
>
>  * **Reativo**: Se forem identificadas cargas de trabalho adicionais através do estudo das métricas de utilização dos recursos, então podem ser atribuídos recursos adicionais ao espaço de nome para incorporar uma procura crescente.
>
> Os contadores de faturação do Autocarro de Serviço são de hora em hora. No caso de escalonamento, só se paga os recursos adicionais pelas horas que foram utilizadas.
>

## <a name="get-started-with-premium-messaging"></a>Introdução às Mensagens Premium

A introdução às Mensagens Premium é simples e o processo é semelhante ao das Mensagens Standard. Comece por [criar um espaço de nomes](service-bus-create-namespace-portal.md) no [portal do Azure](https://portal.azure.com). Certifique-se de que seleciona **Premium**, em **Escalão de preço**. Clique em **Ver informações completas de preços** para obter mais informações sobre cada escalão.

![create-premium-namespace][create-premium-namespace]

Também pode criar [espaços de nomes Premium com modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Mensagens do Service Bus, veja as ligações seguintes:

* [Introdução de Mensagens Premium de Ônibus de Serviço Azure (post de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução às Mensagens Premium do Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral de mensagens de ônibus de serviço](service-bus-messaging-overview.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
