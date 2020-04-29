---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712598"
---
A `ApplicationInsights` definição permite-lhe adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente. A Aplicação Insights fornece uma monitorização aprofundada do seu recipiente. Pode monitorizar facilmente o seu recipiente para disponibilidade, desempenho e utilização. Também pode identificar e diagnosticar rapidamente erros no seu recipiente.

A tabela seguinte descreve as definições de configuração suportadas por baixo da `ApplicationInsights` secção.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | String | A chave de instrumentação da instância de Insights de Aplicação para a qual são enviados dados de telemetria para o contentor. Para mais informações, consulte [os Insights de Aplicação para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|

