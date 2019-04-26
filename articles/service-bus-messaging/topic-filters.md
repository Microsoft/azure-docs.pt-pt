---
title: Filtros de tópico do Service Bus do Azure | Documentos da Microsoft
description: Filtrar tópicos do Service bus do Azure
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 41af53dbfbb5c863007a332445a2f184fcbcbf81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332232"
---
# <a name="topic-filters-and-actions"></a>Filtros de tópico e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas sob a forma de uma ou mais regras de subscrição com nome. Cada regra é composta por uma condição de que seleciona mensagens específicas e uma ação que annotates a mensagem selecionada. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente.

Cada subscrição de tópico recém-criado tem uma regra de subscrição inicial predefinido. Se não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o **true** filtro que permite que todas as mensagens ser selecionado para a subscrição. A regra predefinida não tem nenhuma ação de anotação associados.

Service Bus suporta três condições de filtro:

-   *Filtros Boolianos* – a **TrueFilter** e **FalseFilter** podem causar a todas as mensagens que são recebidas (**verdadeiro**) ou nenhuma das mensagens que são recebidas (**false**) a ser selecionado para a subscrição.

-   *Filtros de SQL* - uma **SqlFilter** contém uma expressão condicional de tipo SQL que é avaliada no Mediador contra as mensagens que são recebidas propriedades definidas pelo utilizador e as propriedades do sistema. Todas as propriedades do sistema tem de ter o prefixo `sys.` na expressão condicional. O [subconjunto de linguagem de SQL para condições de filtro](service-bus-messaging-sql-filter.md) testes a existência de propriedades (`EXISTS`), bem como para valores nulos (`IS NULL`), lógico não/AND/OR, operadores relacionais, aritmética numérico simple, e correspondência de padrões de texto simples com `LIKE`.

-   *Filtros de correlação* - uma **CorrelationFilter** contém um conjunto de condições comparados com uma ou mais das propriedades de utilizador e o sistema de uma mensagem que são recebidos. Um uso comum é a correspondência com o **CorrelationId** propriedade, mas o aplicativo também pode optar por correspondência com **ContentType**, **etiqueta**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **para**e qualquer definido pelo utilizador Propriedades. Quando o valor de uma mensagem que são recebidos para uma propriedade é igual ao valor especificado no filtro de correlação, existir uma correspondência. Para expressões de cadeia de caracteres, a comparação diferencia maiúsculas de minúsculas. Ao especificar várias propriedades de correspondência, o filtro combina-o como uma condição lógica e, para o filtro de acordo com o que significa, todas as condições têm de corresponder.

Todos os filtros de avaliam as propriedades da mensagem. Filtros não é possível avaliar o corpo da mensagem.

Regras do filtro complexos exigem a capacidade de processamento. Em particular, o uso de regras do filtro de SQL resulta num débito de mensagem global inferior ao nível da subscrição, o tópico e o espaço de nomes. Sempre que possível, aplicativos devem escolher os filtros de correlação mais filtros de tipo SQL, uma vez que eles são muito mais eficientes no processamento e, portanto, tem menos impacto sobre o débito.

## <a name="actions"></a>Ações

Com condições de filtro SQL, pode definir uma ação que pode anotar a mensagem por adicionar, remover ou substituir as propriedades e os respetivos valores. A ação [utiliza uma expressão de tipo SQL](service-bus-messaging-sql-filter.md) que livremente apresenta a sintaxe de instrução SQL UPDATE. A ação é executada na mensagem depois foi encontrada e antes da mensagem será selecionada para a subscrição. As alterações às propriedades da mensagem são privadas para a mensagem copiada para a subscrição.

## <a name="usage-patterns"></a>Padrões de utilização

O cenário mais simples de utilização para um tópico é que cada subscrição recebe uma cópia de cada mensagem enviada para um tópico, que permite que um padrão de difusão.

Filtros e as ações permitem dois grupos adicionais de padrões: criação de partições e o encaminhamento.

Criação de partições de filtros de utilizações para distribuir as mensagens por várias subscrições de tópicos existentes de forma previsível e mutuamente exclusiva. O padrão de criação de partições é utilizado quando um sistema é aumentado horizontalmente para lidar com vários contextos diferentes em compartimentos funcionalmente idênticos que cada conter um subconjunto de dados global; Por exemplo, informações de perfil de cliente. Com a criação de partições, um publicador envia a mensagem num tópico, sem a necessidade de qualquer conhecimento do modelo de criação de partições. A mensagem, em seguida, é movida para a subscrição correta da qual pode, em seguida, ser obtida pelo manipulador de mensagens da partição.

Encaminhamento utiliza filtros para distribuir mensagens através de subscrições de tópicos de maneira previsível, mas não necessariamente exclusivo. Em conjunto com o [reencaminhamento automático](service-bus-auto-forwarding.md) tópico filtros podem ser usados para criar o encaminhamento complexo de funcionalidade, gráficos num espaço de nomes do Service Bus para distribuição de mensagem dentro de uma região do Azure. Com as funções do Azure ou do Azure Logic Apps, que atua como uma ponte entre espaços de nomes do Service bus do Azure, pode criar topologias complexas de global com a integração direta em aplicativos de linha de negócio.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Sintaxe de SQLFilter](service-bus-messaging-sql-filter.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)