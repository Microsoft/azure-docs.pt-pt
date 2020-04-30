---
title: Filtros tópicos azure service bus / Microsoft Docs
description: Este artigo explica como os assinantes podem definir quais as mensagens que querem receber de um tópico, especificando filtros.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81482053"
---
# <a name="topic-filters-and-actions"></a>Filtros de tópico e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas na forma de uma ou mais regras de subscrição denominadas. Cada regra consiste numa condição que seleciona mensagens específicas e uma ação que anota a mensagem selecionada. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente.

Cada subscrição de tópico recém-criada tem uma regra inicial de subscrição padrão. Se não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o **verdadeiro** filtro que permite que todas as mensagens sejam selecionadas para a subscrição. A regra padrão não tem nenhuma ação de anotação associada.

O Ônibus de serviço suporta três condições de filtro:

-   *Filtros booleanos* - O **TrueFilter** e **o FalseFilter** ou fazem com que todas as mensagens que chegam **(verdadeiras)** ou nenhuma das mensagens que chegam **(falsas**) sejam selecionadas para a subscrição.

-   *Filtros SQL* - Um **SqlFilter** contém uma expressão condicional semelhante a SQL que é avaliada no corretor contra as propriedades e propriedades do sistema definidas pelo utilizador das mensagens que chegam. Todas as propriedades do `sys.` sistema devem ser pré-fixadas na expressão condicional. O [subconjunto em língua SQL para](service-bus-messaging-sql-filter.md) os ensaios de condições de filtro para a existência de propriedades`EXISTS`( valores nulos ),`IS NULL` `LIKE`valores lógicoS NÃO/AND/OR, operadores relacionais, aritmética numérica simples e padrão de texto simples que corresponda a .

-   *Filtros* de Correlação - Um **CorrelationFilter** contém um conjunto de condições que são correspondidas a uma ou mais propriedades do utilizador e do sistema de uma mensagem que chega. Um uso comum é combinar com a propriedade **CorrelationId,** mas a aplicação também pode optar por combinar com as seguintes propriedades:

    - **Tipo de conteúdo**
     - **Etiqueta**
     - **MensagemId**
     - **Resposta**
     - **RespostaToSessionid**
     - **SessãoId** 
     - **Para**
     - quaisquer propriedades definidas pelo utilizador. 
     
     Existe uma correspondência quando o valor de uma mensagem de chegada para uma propriedade é igual ao valor especificado no filtro de correlação. Para expressões de cordas, a comparação é sensível ao caso. Ao especificar várias propriedades de correspondência, o filtro combina-as como uma condição lógica e condição, o que significa que para o filtro corresponder, todas as condições devem coincidir.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não conseguem avaliar o corpo da mensagem.

Regras complexas de filtro requerem capacidade de processamento. Em particular, a utilização de regras de filtro SQL causa uma menor entrada de mensagem global no nível de subscrição, tópico e espaço de nome. Sempre que possível, as aplicações devem escolher filtros de correlação em vez de filtros semelhantes ao SQL porque são muito mais eficientes no processamento e têm menos impacto na entrada.

## <a name="actions"></a>Ações

Com as condições do filtro SQL, pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [usa uma expressão semelhante a SQL](service-bus-messaging-sql-filter.md) que se inclina vagamente sobre a sintaxe de declaração de atualização SQL. A ação é feita na mensagem depois de ter sido correspondida e antes de a mensagem ser selecionada para a subscrição. As alterações às propriedades da mensagem são privadas da mensagem copiada na subscrição.

## <a name="usage-patterns"></a>Padrões de utilização

O cenário de utilização mais simples para um tópico é que cada subscrição recebe uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de transmissão.

Filtros e ações permitem mais dois grupos de padrões: partição e encaminhamento.

A partição utiliza filtros para distribuir mensagens através de várias subscrições de tópicos existentes de forma previsível e mutuamente exclusiva. O padrão de partição é utilizado quando um sistema é dimensionado para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que cada um tem um subconjunto dos dados globais; por exemplo, informações sobre o perfil do cliente. Com a partilha, uma editora submete a mensagem a um tópico sem necessitar de qualquer conhecimento do modelo de partição. A mensagem é então transferida para a subscrição correta a partir da qual pode ser recuperada pelo manipulador de mensagens da partição.

O encaminhamento utiliza filtros para distribuir mensagens através de subscrições de tópicos de forma previsível, mas não necessariamente exclusiva. Em conjunto com a funcionalidade de [reencaminhamento automático,](service-bus-auto-forwarding.md) os filtros tópicos podem ser usados para criar gráficos de encaminhamento complexos dentro de um espaço de nome service Bus para distribuição de mensagens dentro de uma região do Azure. Com as Funções Azure ou as Aplicações Lógicas Azure a funcionarem como uma ponte entre os espaços de nome sinuosidade do Azure Service Bus, pode criar topologas globais complexas com integração direta em aplicações de linha de negócio.


> [!NOTE]
> Atualmente, o portal Azure não permite especificar regras de filtro para subscrições. Pode utilizar qualquer um dos modelos sDKs suportados ou do Gestor de Recursos Azure para definir regras de subscrição. 

## <a name="next-steps"></a>Passos seguintes
Consulte as seguintes amostras: 

- [.NET - Envio básico e receber tutorial com filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtros tópicos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Escrever script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


