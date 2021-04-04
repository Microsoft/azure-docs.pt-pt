---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001200"
---
A `ApplicationInsights` definição permite-lhe adicionar suporte de telemetria [Azure Application Insights](/azure/application-insights) ao seu recipiente. O Application Insights fornece uma monitorização aprofundada do seu recipiente. Pode monitorizar facilmente o seu recipiente para obter disponibilidade, desempenho e utilização. Também pode identificar e diagnosticar rapidamente erros no seu recipiente.

A tabela seguinte descreve as definições de configuração suportadas na `ApplicationInsights` secção.

|Necessário| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | String | A chave de instrumentação da instância Application Insights para a qual são enviados os dados de telemetria do recipiente. Para obter mais informações, consulte [Insights de Aplicação para ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|