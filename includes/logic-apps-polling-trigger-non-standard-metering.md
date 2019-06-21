---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184852"
---
Para estimar os custos de consumo mais precisos, considere o número possível de mensagens ou eventos que podem chegar num determinado dia, em vez de basear os seus cálculos em apenas o intervalo de consulta. Quando um evento ou uma mensagem cumpre os critérios de Acionador, muitos acionadores imediatamente a tentar ler quaisquer e todos os outros eventos de espera ou mensagens que cumprem os critérios. Este comportamento significa que, mesmo quando selecionar um intervalo de consulta mais tempo, o acionador é acionado com base no número de eventos de espera ou de mensagens que se qualificar para a partir de fluxos de trabalho. Acionadores que se seguem esse comportamento incluem Service bus do Azure e o Hub de eventos do Azure.

Então, por exemplo, suponha que configurou o acionador verifica todos os dias de um ponto de extremidade. Quando o acionador verifica o ponto final e localiza 15 eventos que cumprem os critérios, o acionador é acionado e executa o fluxo de trabalho correspondente 15 vezes. O Logic Apps medidores de todas as ações que executam de 15 esses fluxos de trabalho, incluindo os pedidos de Acionador. Para calcular os custos potenciais, experimente o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).