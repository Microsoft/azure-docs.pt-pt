---
title: Insights de aplicação Azure - Funcionalidades suportadas por funções azure
description: Funcionalidades suportadas por insights de aplicação para funções azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ce8fcc827afdf0102a459f2c6b58db45eff72fa0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467392"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funcionalidades suportadas por Insights de Aplicação para Funções Azure

A Azure Functions oferece [integração integrada](../../azure-functions/functions-monitoring.md) com os Insights de Aplicação, que está disponível através da Interface ILogger. Abaixo está a lista de funcionalidades atualmente suportadas. Reveja o guia de funções azure para [começar](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Para mais informações sobre versões de tempo de execução, consulte [aqui](../../azure-functions/functions-versions.md).

## <a name="supported-features"></a>Funcionalidades suportadas

| Funções do Azure                       | V1                | V2 e V3   | 
|-----------------------------------    |---------------    |------------------ |
| **Insights de aplicação .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Recolha automática de**        |                 |                   |               
| Pedidos &bull;                     | Sim             | Sim               | 
| exceções &bull;                   | Sim             | Sim               | 
| contadores de desempenho &bull;         | Sim             | Sim               |
| Dependências &bull;                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Sim               | 
| | | | 
| **Características suportadas**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de Controlo Seguro|                 | Sim               | 
| Amostragem &bull;                     | Sim             | Sim               | 
| &bull; Heartbeats                   |                 | Sim               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Sim               | 
| &bull; EventHub                       |                   | Sim               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;totalmente configurável.<br/>Consulte [as funções Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Consulte [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Sim                   | 


## <a name="performance-counters"></a>Contadores de Desempenho

A recolha automática de Contadores de Desempenho funciona apenas com máquinas Windows.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics e Secure Control Channel

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
