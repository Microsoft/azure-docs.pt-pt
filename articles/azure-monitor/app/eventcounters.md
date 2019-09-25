---
title: Contadores de eventos em Application Insights | Microsoft Docs
description: Monitore o sistema e o .NET Core EventCounters personalizado no Application Insights.
services: application-insights
documentationcenter: ''
author: cithomas
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: cithomas
ms.openlocfilehash: fc9148d4f4c5920210b9218ca70f270bae3b663b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273928"
---
# <a name="eventcounters-introduction"></a>Introdução ao EventCounters

`EventCounter`é o mecanismo .NET/.NET Core para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornece uma visão geral `EventCounters` e exemplos de como publicá-los e consumi-los. EventCounters têm suporte em todas as plataformas de sistema operacional: Windows, Linux e macOS. Pode ser pensado como um equivalente de plataforma cruzada para os [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) que só tem suporte em sistemas Windows.

Embora os usuários possam publicar qualquer `EventCounters` personalizado para atender às suas necessidades, o tempo de execução do .NET Core 3,0 publica um conjunto desses contadores por padrão. O documento examinará as etapas necessárias para coletar e exibir `EventCounters` (definido pelo sistema ou definido pelo usuário) no insights aplicativo Azure.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usando Application Insights para coletar EventCounters

Application insights dá suporte `EventCounters` à coleta `EventCounterCollectionModule`com seu, que faz parte do pacote NuGet recentemente lançado [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`é habilitado automaticamente quando o [AspNetCore](asp-net-core.md) ou o [WorkerService](worker-service.md)é usado. `EventCounterCollectionModule`coleta contadores com uma frequência de coleta não configurável de 60 segundos. Não há permissões especiais necessárias para coletar EventCounters.

## <a name="default-counters-collected"></a>Contadores padrão coletados

Para aplicativos em execução no .NET Core 3,0, os contadores a seguir são coletados automaticamente pelo SDK. O nome dos contadores estará no formato "categoria | Counter ".

|Category | Contador|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Os contadores da categoria Microsoft. AspNetCore. Hosting são adicionados somente em aplicativos do Asp.Net Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizando os contadores a serem coletados

O exemplo a seguir mostra como adicionar/remover contadores. Essa personalização seria feita no `ConfigureServices` método de seu aplicativo depois que Application insights coleção `AddApplicationInsightsTelemetry()` de telemetria é habilitada usando `AddApplicationInsightsWorkerService()`ou. Veja a seguir um exemplo de código de um aplicativo ASP.NET Core. Para outros tipos de aplicativos, consulte [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Gerenciador de métricas

Para exibir as métricas do EventCounter no [Gerenciador de métricas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), selecione Application insights recurso e escolha métricas baseadas em log como namespace de métrica. Em seguida, as métricas EventCounter são exibidas na categoria PerformanceCounter.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de eventos no Analytics

Você também pode pesquisar e exibir relatórios de contador de eventos na [análise](../../azure-monitor/app/analytics.md), na tabela **PerformanceCounters** .

Por exemplo, execute a consulta a seguir para ver quais contadores são coletados e estão disponíveis para consulta:

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um contador específico (por exemplo: `ThreadPool Completed Work Item Count`) no período recente, execute a consulta a seguir.

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único contador no Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Assim como outras telemetrias, os **PerformanceCounters** também têm uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host na qual seu aplicativo está sendo executado. A consulta acima mostra o valor do contador por instância e pode ser usada para comparar o desempenho de diferentes instâncias de servidor.

## <a name="alerts"></a>Alertas
Assim como outras métricas, você pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de eventos ficar fora de um limite especificado. Abra o painel alertas e clique em adicionar alerta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver EventCounters em métricas ao vivo?

As métricas ao vivo não mostram EventCounters a partir de hoje. Use o Gerenciador de métricas ou a análise para ver a telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Quais plataformas posso ver a lista padrão de contadores do .NET Core 3,0?

O EventCounter não exige nenhuma permissão especial e tem suporte em todas as plataformas o .NET Core 3,0 tem suporte. Isto inclui:

* **Sistema operacional**: Windows, Linux ou macOS.
* **Método de hospedagem**: Em andamento ou fora do processo.
* **Método de implantação**: Dependente da estrutura ou independente.
* **Servidor Web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: O recurso de aplicativos Web do serviço de Azure App, VM do Azure, Docker, serviço kubernetes do Azure (AKS) e assim por diante.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Habilitei Application Insights do portal do aplicativo Web do Azure. Mas não consigo ver EventCounters.?

 [Application insights extensão](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) para ASP.NET Core ainda não dá suporte a esse recurso. Este documento será atualizado quando houver suporte para esse recurso.

## <a name="next"></a>Passos seguintes

* [Acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md)