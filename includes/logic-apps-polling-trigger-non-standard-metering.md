---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793436"
---
Para estimar custos de consumo mais precisos, considere o número possível de mensagens ou eventos que podem chegar em qualquer determinado dia, em vez de basear seus cálculos somente no intervalo de sondagem. Quando um evento ou uma mensagem atende aos critérios do gatilho, muitos gatilhos tentam imediatamente ler todos e todos os outros eventos ou mensagens em espera que atendem aos critérios. Esse comportamento significa que mesmo quando você seleciona um intervalo de sondagem mais longo, o gatilho é acionado com base no número de eventos de espera ou mensagens que se qualificam para iniciar fluxos de trabalho. Os gatilhos que seguem esse comportamento incluem o barramento de serviço do Azure e o Hub de eventos do Azure.

Portanto, por exemplo, suponha que você configure o gatilho que verifica um ponto de extremidade todos os dias. Quando o gatilho verifica o ponto de extremidade e localiza 15 eventos que atendem aos critérios, o gatilho é acionado e executa o fluxo de trabalho correspondente 15 vezes. Os aplicativos lógicos medem todas as ações que esses 15 fluxos de trabalho executam, incluindo as solicitações de gatilho. Para calcular seus custos potenciais, experimente a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).