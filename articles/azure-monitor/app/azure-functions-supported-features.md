---
title: Insights de aplicação Azure - Funcionalidades suportadas por funções azure
description: Funcionalidades suportadas por insights de aplicação para funções azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655655"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funcionalidades suportadas por Insights de Aplicação para Funções Azure

A Azure Functions oferece [integração integrada](../../azure-functions/functions-monitoring.md) com os Insights de Aplicação, que está disponível através da Interface ILogger. Abaixo está a lista de funcionalidades atualmente suportadas. Reveja o guia de funções azure para [começar](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Para obter mais informações sobre as versões functions runtime, consulte [aqui](../../azure-functions/functions-versions.md).

Para obter mais informações sobre versões compatíveis de Insights de Aplicação, consulte [Dependências](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Funcionalidades suportadas

| Funções do Azure                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Recolha automática de**        |                 |                   |               
| &bull;Pedidos                     | Sim             | Sim               | 
| &bull;Exceções                   | Sim             | Sim               | 
| &bull;Contadores de Desempenho         | Sim             | Sim               |
| &bull;Dependências                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;HTTP      |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBus|                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub  |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash;Rio SQL       |                 | Sim               | 
| | | | 
| **Funcionalidades suportadas**                |                   |                   |               
| &bull;QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash;Canal de Controlo Seguro|                 | Sim               | 
| &bull;Amostragem                     | Sim             | Sim               | 
| &bull;Batimentos cardíacos                   |                 | Sim               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull;ServiceBus                     |                   | Sim               | 
| &bull;EventHub                       |                   | Sim               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Totalmente configurável.<br/>Consulte [as funções Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Consulte [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Sim                   | 


## <a name="performance-counters"></a>Contadores de Desempenho

A recolha automática de Contadores de Desempenho funciona apenas com máquinas Windows.


## <a name="live-metrics--secure-control-channel"></a>As métricas vivas & canal de controlo seguro

Os critérios de filtro personalizados que especifica são enviados de volta para o componente Métricas Vivas no SDK de Insights de Aplicação. Os filtros podem potencialmente conter informações sensíveis, tais como iDs de clientes. Pode tornar o canal seguro com uma chave API secreta. Consulte [A segurança do canal de controlo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>Amostragem

Funções Azure permite a amostragem por padrão na sua configuração. Para mais informações, consulte [Configure Sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Se o seu projeto assumir uma dependência do SDK de Insights de Aplicação para fazer rastreio de telemetria manual, poderá experimentar um comportamento estranho se a configuração de amostragem for diferente da configuração de amostragem das Funções. 

Recomendamos a utilização da mesma configuração que as Funções. Com **funções v2,** pode obter a mesma configuração utilizando a injeção de dependência no seu construtor:

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
