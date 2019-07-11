---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712598"
---
O `ApplicationInsights` definição permite que adicione [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor. O Application Insights fornece monitorização detalhada do seu contentor. Pode monitorizar facilmente o seu contentor de disponibilidade, desempenho e utilização. Pode também rapidamente identificar e diagnosticar erros no seu contentor.

A tabela seguinte descreve as definições de configuração suportadas ao abrigo do `ApplicationInsights` secção.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | Cadeia | A chave de instrumentação da instância do Application Insights à qual telemetria são enviados dados para o contentor. Para obter mais informações, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|

