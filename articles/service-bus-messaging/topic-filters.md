---
title: Filtros tópicos azure service bus / Microsoft Docs
description: Este artigo explica como os assinantes podem definir quais as mensagens que querem receber de um tópico, especificando filtros.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: spelluru
ms.openlocfilehash: b8ffbb16763bfe6485ebf2ab770f4537ddbc8569
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774503"
---
# <a name="topic-filters-and-actions"></a>Filtros de tópico e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas sob a forma de uma ou mais regras de subscrição nomeadas. Cada regra consiste numa condição que seleciona mensagens específicas e uma ação que anota a mensagem selecionada. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente.

Cada subscrição de tópico recém-criada tem uma regra inicial de subscrição padrão. Se não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o **verdadeiro** filtro que permite que todas as mensagens sejam selecionadas para a subscrição. A regra padrão não tem nenhuma ação de anotação associada.

O Ônibus de serviço suporta três condições de filtro:

-   *Filtros booleanos* - O **TrueFilter** e **o FalseFilter** ou fazem com que todas as mensagens que chegam **(verdadeiras)** ou nenhuma das mensagens que chegam **(falsas**) sejam selecionadas para a subscrição.

-   *Filtros SQL* - Um **SqlFilter** contém uma expressão condicional semelhante a SQL que é avaliada no corretor contra as propriedades e propriedades do sistema definidas pelo utilizador das mensagens que chegam. Todas as propriedades do sistema devem ser pré-fixadas com `sys.` na expressão condicional. O [subconjunto em língua SQL para](service-bus-messaging-sql-filter.md) os testes de condições de filtro para a existência de propriedades (`EXISTS`), bem como para valores nulos (`IS NULL`), operadores lógicos NÃO/AND/OR, operadores relacionais, aritmética numérica simples e padrão de texto simples que corresponda a `LIKE`.

-   *Filtros* de Correlação - Um **CorrelationFilter** contém um conjunto de condições que são correspondidas a uma ou mais propriedades do utilizador e do sistema de uma mensagem que chega. Uma utilização comum é combinar com a propriedade **CorrelationId,** mas a aplicação também pode optar por combinar com **contentType**, **Label**, **MessageId,** **AnswerTo**, **AnswerToSessionId,** **SessionId,** **To**, e quaisquer propriedades definidas pelo utilizador. Existe uma correspondência quando o valor de uma mensagem de chegada para uma propriedade é igual ao valor especificado no filtro de correlação. Para expressões de cordas, a comparação é sensível ao caso. Ao especificar várias propriedades de correspondência, o filtro combina-as como uma condição lógica e condição, o que significa que para o filtro corresponder, todas as condições devem coincidir.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não podem avaliar o corpo da mensagem.

Regras complexas de filtro requerem capacidade de processamento. Em particular, a utilização de regras de filtro SQL resulta numa menor entrada de mensagem global no nível de subscrição, tópico e espaço de nome. Sempre que possível, as aplicações devem escolher filtros de correlação em vez de filtros semelhantes ao SQL, uma vez que são muito mais eficientes no processamento e, portanto, têm menos impacto na entrada.

## <a name="actions"></a>Ações

Com as condições do filtro SQL, pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [usa uma expressão semelhante a SQL](service-bus-messaging-sql-filter.md) que se inclina vagamente sobre a sintaxe de declaração de atualização SQL. A ação é realizada na mensagem depois de ter sido correspondida e antes de a mensagem ser selecionada para a subscrição. As alterações às propriedades da mensagem são privadas da mensagem copiada na subscrição.

## <a name="usage-patterns"></a>Padrões de utilização

O cenário de utilização mais simples para um tópico é que cada subscrição recebe uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de transmissão.

Filtros e ações permitem mais dois grupos de padrões: partição e encaminhamento.

A partição utiliza filtros para distribuir mensagens através de várias subscrições de tópicos existentes de forma previsível e mutuamente exclusiva. O padrão de partição é utilizado quando um sistema é dimensionado para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que cada um tem um subconjunto dos dados globais; por exemplo, informações sobre o perfil do cliente. Com a partilha, uma editora submete a mensagem a um tópico sem necessitar de qualquer conhecimento do modelo de partição. A mensagem é então transferida para a subscrição correta a partir da qual pode ser recuperada pelo manipulador de mensagens da partição.

O encaminhamento utiliza filtros para distribuir mensagens através de subscrições de tópicos de forma previsível, mas não necessariamente exclusiva. Em conjunto com a funcionalidade de [reencaminhamento automático,](service-bus-auto-forwarding.md) os filtros tópicos podem ser usados para criar gráficos de encaminhamento complexos dentro de um espaço de nome service Bus para distribuição de mensagens dentro de uma região do Azure. Com as Funções Azure ou as Aplicações Lógicas Azure a funcionarem como uma ponte entre os espaços de nome sinuosidade do Azure Service Bus, pode criar topologas globais complexas com integração direta em aplicações de linha de negócio.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Sintaxe de SQLFilter](service-bus-messaging-sql-filter.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)