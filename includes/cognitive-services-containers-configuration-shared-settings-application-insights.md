---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116708"
---
O `ApplicationInsights` definição permite que adicione [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor. O Application Insights fornece monitorização detalhada do seu contentor. Pode monitorizar facilmente o seu contentor de disponibilidade, desempenho e utilização. Pode também rapidamente identificar e diagnosticar erros no seu contentor.

A tabela seguinte descreve as definições de configuração suportadas ao abrigo do `ApplicationInsights` secção.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | Cadeia | A chave de instrumentação da instância do Application Insights à qual telemetria são enviados dados para o contentor. Para obter mais informações, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|

