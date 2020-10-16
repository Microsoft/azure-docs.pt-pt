---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793436"
---
Para estimar custos de consumo mais precisos, considere o possível número de mensagens ou eventos que possam chegar num determinado dia, em vez de basear os seus cálculos apenas no intervalo de votação. Quando um evento ou mensagem satisfaz os critérios do gatilho, muitos gatilhos tentam imediatamente ler todos e quaisquer outros eventos de espera ou mensagens que satisfaçam os critérios. Este comportamento significa que mesmo quando se seleciona um intervalo de votação mais longo, o gatilho dispara com base no número de eventos de espera ou mensagens que se qualificam para iniciar fluxos de trabalho. Os gatilhos que seguem este comportamento incluem o Azure Service Bus e o Azure Event Hub.

Então, por exemplo, suponha que você configura o gatilho que verifica um ponto final todos os dias. Quando o gatilho verifica o ponto final e encontra 15 eventos que satisfazem os critérios, o gatilho dispara e executa o fluxo de trabalho correspondente 15 vezes. A Logic Apps medi todas as ações que esses 15 fluxos de trabalho realizam, incluindo os pedidos de gatilho. Para calcular os seus custos potenciais, experimente a [calculadora de preços da Azure.](https://azure.microsoft.com/pricing/calculator/)