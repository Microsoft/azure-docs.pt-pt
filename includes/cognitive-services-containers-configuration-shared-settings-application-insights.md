---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67712598"
---
A `ApplicationInsights` definição permite-lhe adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente. O Application Insights fornece uma monitorização aprofundada do seu recipiente. Pode monitorizar facilmente o seu recipiente para obter disponibilidade, desempenho e utilização. Também pode identificar e diagnosticar rapidamente erros no seu recipiente.

A tabela seguinte descreve as definições de configuração suportadas na `ApplicationInsights` secção.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | String | A chave de instrumentação da instância Application Insights para a qual são enviados os dados de telemetria do recipiente. Para obter mais informações, consulte [Insights de Aplicação para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|

