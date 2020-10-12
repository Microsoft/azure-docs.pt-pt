---
title: Filtros tópicos do ônibus da Azure Service / Microsoft Docs
description: Este artigo explica como os assinantes podem definir quais as mensagens que querem receber de um tópico especificando filtros.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5df343ff63c01a7cf10315b758e3d6fba8ac5674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066751"
---
# <a name="topic-filters-and-actions"></a>Filtros de tópico e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas na forma de uma ou mais regras de subscrição denominadas. Cada regra consiste numa condição que seleciona mensagens específicas e uma ação que anota a mensagem selecionada. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente.

Cada subscrição de tópico recentemente criada tem uma regra inicial de subscrição padrão. Se não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o **verdadeiro** filtro que permite selecionar todas as mensagens na subscrição. A regra padrão não tem nenhuma ação de anotação associada.

O Service Bus suporta três condições de filtro:

-   *Filtros Boolean* - O **TrueFilter** e **o FalseFilter** fazem com que todas as mensagens que chegam (**verdadeiras**) ou nenhuma das mensagens que chegam **(falsas**) sejam selecionadas para a subscrição.

-   *FILTROS SQL* - Um **SqlFilter** detém uma expressão condicional semelhante ao SQL que é avaliada no corretor contra as propriedades e propriedades do sistema definidas pelo utilizador das mensagens que chegam. Todas as propriedades do sistema devem ser pré-fixas `sys.` na expressão condicional. O [subconjunto de linguagem SQL para testes](service-bus-messaging-sql-filter.md) de condições de filtração para a existência de propriedades `EXISTS` (, valores nulos ( `IS NULL` ), NÃO/AND/OR lógicos, operadores relacionais, aritmética numérica simples e padrão de texto simples combinando com `LIKE` .

-   *Filtros de correlação* - Um **CorrelationFiltro** contém um conjunto de condições que são compatíveis com uma ou mais das propriedades do utilizador e do sistema de uma mensagem que chega. Um uso comum é corresponder à propriedade **CorrelationId,** mas a aplicação também pode optar por corresponder às seguintes propriedades:

    - **ConteúdoType**
     - **Etiqueta**
     - **MessageId**
     - **RespostaTo**
     - **RespostaToSessionId**
     - **SessionId** 
     - **Para**
     - quaisquer propriedades definidas pelo utilizador. 
     
     Existe uma correspondência quando o valor de uma mensagem de chegada para uma propriedade é igual ao valor especificado no filtro de correlação. Para expressões de cordas, a comparação é sensível a casos. Ao especificar várias propriedades de correspondência, o filtro combina-as como uma condição lógica e, o que significa que para o filtro coincidir, todas as condições devem corresponder.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não conseguem avaliar o corpo da mensagem.

Regras complexas de filtro requerem capacidade de processamento. Em particular, a utilização de regras de filtro SQL causa uma menor produção geral de mensagens ao nível da subscrição, tópico e espaço de nome. Sempre que possível, as aplicações devem escolher filtros de correlação em vez de filtros semelhantes ao SQL, porque são muito mais eficientes no processamento e têm menos impacto na produção.

## <a name="actions"></a>Ações

Com condições de filtragem SQL, pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [utiliza uma expressão semelhante ao SQL](service-bus-messaging-sql-filter.md) que se apoia vagamente na sintaxe de declaração SQL UPDATE. A ação é feita na mensagem depois de ter sido correspondida e antes da mensagem ser selecionada para a subscrição. As alterações às propriedades da mensagem são privadas para a mensagem copiada na subscrição.

## <a name="usage-patterns"></a>Padrões de utilização

O cenário de utilização mais simples para um tópico é que cada subscrição recebe uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de transmissão.

Filtros e ações permitem mais dois grupos de padrões: partição e encaminhamento.

A partição utiliza filtros para distribuir mensagens em várias subscrições de tópicos existentes de forma previsível e mutuamente exclusiva. O padrão de partição é utilizado quando um sistema é dimensionado para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que cada um detém um subconjunto dos dados globais; por exemplo, informações sobre o perfil do cliente. Com a partilha, uma editora submete a mensagem a um tópico sem exigir qualquer conhecimento do modelo de partição. A mensagem é então transferida para a subscrição correta a partir da qual pode ser recuperada pelo manipulador de mensagens da partição.

O encaminhamento utiliza filtros para distribuir mensagens através de subscrições de tópicos de forma previsível, mas não necessariamente exclusivos. Em conjunto com a [função de encaminhamento automático,](service-bus-auto-forwarding.md) os filtros tópicos podem ser usados para criar gráficos de encaminhamento complexos dentro de um espaço de nomes de Service Bus para distribuição de mensagens dentro de uma região de Azure. Com as Azure Functions ou Azure Logic Apps atuando como uma ponte entre os espaços de nomes do Azure Service Bus, pode criar topologias globais complexas com integração direta em aplicações de linha de negócio.


> [!NOTE]
> Como o portal Azure suporta agora a funcionalidade Service Bus Explorer, os filtros de subscrição podem ser criados ou editados a partir do portal. 

## <a name="next-steps"></a>Passos seguintes
Consulte as seguintes amostras: 

- [.NET - Envio básico e receber tutorial com filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtros tópicos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Modelo Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)