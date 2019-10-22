---
title: Informações do Aplicativo Azure-Azure Functions recursos com suporte | Microsoft Docs
description: Application Insights recursos com suporte para Azure Functions
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 31f962ca96ca5c47d18f9250e567abb8f4024e6f
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677561"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights para Azure Functions recursos com suporte

O Azure Functions oferece [integração interna](../../azure-functions/functions-monitoring.md) com o Application insights, que está disponível por meio da interface ILogger. Abaixo está a lista de recursos com suporte no momento. Examine o guia Azure Functions ' para [começar](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

## <a name="supported-features"></a>Funcionalidades suportadas

| Funções do Azure                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **SDK do .NET Application Insights**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Coleção automática de**        |                 |                   |               
| Solicitações &bull;                     | Sim             | Sim               | 
| Exceções &bull;                   | Sim             | Sim               | 
| Contadores de desempenho &bull;         | Sim             | Sim               |
| Dependências &bull;                   |                   |                   |               
| &nbsp; &nbsp; &nbsp; &mdash; HTTP      |                 | Sim               | 
| &nbsp; &nbsp; &nbsp; &mdash; ServiceBus|                 | Sim               | 
| &nbsp; &nbsp; &nbsp; &mdash; EventHub  |                 | Sim               | 
| &nbsp; &nbsp; &nbsp; &mdash; SQL       |                 | Sim               | 
| | | | 
| **Recursos com suporte**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp; &nbsp; &nbsp; &mdash; canal de controle seguro|                 | Sim               | 
| Amostragem de &bull;                     | Sim             | Sim               | 
| Pulsações de &bull;                   |                 | Sim               | 
| | | | 
| **Exata**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Sim               | 
| &bull; EventHub                       |                   | Sim               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Fully configurável.<br/>Consulte [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Consulte [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Sim                   | 


## <a name="performance-counters"></a>Contadores de Desempenho

A coleta automática de contadores de desempenho só funcionam em computadores Windows.


## <a name="live-metrics--secure-control-channel"></a>Métricas ao vivo & canal de controle seguro

Os critérios de filtros personalizados especificados são enviados de volta ao componente de métricas em tempo real no SDK do Application Insights. Potencialmente, os filtros podem conter informações confidenciais, como customerIDs. Você pode tornar o canal seguro com uma chave de API secreta. Consulte [proteger o canal de controle](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>Amostragem

Azure Functions habilita a amostragem por padrão em sua configuração. Para obter mais informações, consulte [Configurar a amostragem](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Se o seu projeto usar uma dependência no SDK do Application Insights para fazer o acompanhamento manual de telemetria, você poderá enfrentar um comportamento estranho se a configuração de amostragem for diferente da configuração de amostragem das funções. 

É recomendável usar a mesma configuração que as funções. Com o **Functions v2**, você pode obter a mesma configuração usando a injeção de dependência em seu construtor:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
