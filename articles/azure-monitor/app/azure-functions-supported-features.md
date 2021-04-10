---
title: Insights de aplicação Azure - Funções Azure Suportadas
description: Funcionalidades suportadas de Insights de Aplicação para Funções Azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b44279f31aea8fc02130f1c3d7520f42c648bd4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607954"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Insights de Aplicações para Funções Azure suportados

A Azure Functions oferece [integração incorporada](../../azure-functions/functions-monitoring.md) com o Application Insights, que está disponível através da Interface ILogger. Abaixo está a lista de funcionalidades atualmente suportadas. Reveja o guia de Azure Functions para [começar](../../azure-functions/configure-monitoring.md#enable-application-insights-integration).

Para obter mais informações sobre as versões de tempo de execução de Funções, consulte [aqui](../../azure-functions/functions-versions.md).

Para obter mais informações sobre versões compatíveis de Insights de Aplicação, consulte [Dependências](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Funcionalidades suportadas

| Funções do Azure                   | V1            | V2 & V3 | 
|-----------------------------------|---------------|------------------|
| | | | 
| **Recolha automática de**        |               |                  |
| &bull; Pedidos                     | Yes           | Yes              |
| &bull; Exceções                   | Yes           | Yes              |
| &bull; Contadores de desempenho         | Yes           | Yes              |
| &bull; Dependências                 |               |                  |
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |               | Yes              |
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|               | Yes              |
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |               | Yes              |
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |               | Yes              |
| | | | 
| **Funcionalidades suportadas**              |               |                  |
| &bull; QuickPulse/LiveMetrics       | Yes           | Yes              | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de Controlo Seguro |               | Yes | 
| &bull; Amostragem                     | Yes           | Yes              | 
| &bull; Batimentos cardíacos                   | | Yes              | 
| | | |
| **Correlação**                    |               |                  |
| &bull; ServiceBus                  |               | Yes              |
| &bull; EventHub                    |               | Yes              |
| | | | 
| **Configurável**                  |               |                  |           
| &bull;Totalmente configurável.<br/>Consulte [as funções Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Consulte [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.           |               | Yes                 | 

## <a name="performance-counters"></a>Contadores de Desempenho

A recolha automática de Contadores de Desempenho apenas funciona com máquinas Windows.

## <a name="live-metrics--secure-control-channel"></a>Métricas ao vivo & Canal de Controlo Seguro

Os critérios de filtros personalizados especificados são enviados de volta para o componente Métricas Vivas no SDK application Insights. Os filtros podem potencialmente conter informações sensíveis, tais como os ClientesIDs. Pode tornar o canal seguro com uma chave API secreta. Consulte [o canal de controlo](./live-stream.md#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>Amostragem

As funções Azure permitem a amostragem por defeito na sua configuração. Para obter mais informações, consulte [a Amostragem de Configuração.](../../azure-functions/configure-monitoring.md#configure-sampling)

Se o seu projeto assumir uma dependência do SDK Application Insights para fazer rastreio manual de telemetria, poderá experimentar um comportamento estranho se a sua configuração de amostragem for diferente da configuração de amostragem das Funções. 

Recomendamos a utilização da mesma configuração que as Funções. Com **as Funções v2,** pode obter a mesma configuração utilizando a injeção de dependência no seu construtor:

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
