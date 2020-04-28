---
title: Contadores de eventos em Insights de Aplicação / Microsoft Docs
description: Monitorize o sistema e custom .NET/.NET Core EventCounters em Insights de Aplicação.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e1037766587f58a30c20f614726e1241c16e5a16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187100"
---
# <a name="eventcounters-introduction"></a>Introdução de EventCounters

`EventCounter`é o mecanismo de núcleo .NET/.NET para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento dá uma `EventCounters` visão geral e exemplos sobre como publicá-los e consumi-los. Os EventCounters são suportados em todas as plataformas de OS - Windows, Linux e macOS. Pode ser considerado como um equivalente de plataforma cruzada para os [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) que só é suportado nos sistemas Windows.

Embora os utilizadores `EventCounters` possam publicar qualquer costume para satisfazer as suas necessidades, o tempo de execução .NET Core 3.0 publica um conjunto destes contadores por padrão. O documento passará pelos passos necessários `EventCounters` para recolher e visualizar (sistema definido ou definido pelo utilizador) em Insights de Aplicação Azure.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usando insights de aplicação para recolher Contadores de Eventos

Application Insights suporta `EventCounters` a `EventCounterCollectionModule`recolha com o seu , que faz parte do pacote de nuget recém-lançado [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`é ativado automaticamente quando utilizar [aspNetCore](asp-net-core.md) ou [WorkerService](worker-service.md). `EventCounterCollectionModule`recolhe contadores com uma frequência de recolha não configurável de 60 segundos. Não são necessárias permissões especiais para recolher Balcões de Eventos.

## <a name="default-counters-collected"></a>Contadores padrão recolhidos

Para aplicações em .NET Core 3.0, os seguintes contadores são recolhidos automaticamente pelo SDK. O nome dos balcões será da forma "Categoria" Contador".

|Categoria | Contador|
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
> Os contadores da categoria Microsoft.AspNetCore.Hosting são adicionados apenas em aplicações ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizando balcões a recolher

O exemplo que se segue mostra como adicionar/remover contadores. Esta personalização seria feita `ConfigureServices` no método da sua aplicação após a `AddApplicationInsightsTelemetry()` recolha `AddApplicationInsightsWorkerService()`de telemetria Application Insights ser ativada usando ou . Segue-se um código de exemplo de uma aplicação ASP.NET Core. Para outro tipo de aplicações, consulte [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

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

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Explorador Métrico

Para ver as métricas de Contador de Eventos no [Explorador Métrico,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)selecione o recurso Application Insights e escolheu as métricas baseadas em Log como espaço de nomemétrico. Em seguida, as métricas eventCounter são exibidas na categoria Custom.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos reportados em Insights de Aplicação](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de eventos em Analytics

Também pode pesquisar e exibir relatórios de contra-eventos no [Analytics,](../../azure-monitor/app/analytics.md)na tabela **CustomMetrics.**

Por exemplo, executar a seguinte consulta para ver que contadores são recolhidos e disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos reportados em Insights de Aplicação](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um `ThreadPool Completed Work Item Count`contador específico (por exemplo: ) durante o período recente, faça a seguinte consulta.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único contador em Insights de Aplicação](./media/event-counters/analytics-completeditems-counters.png)

Tal como outras telemetrias, `cloud_RoleInstance` a **CustomMetrics** também tem uma coluna que indica a identidade da instância do servidor anfitrião em que a sua aplicação está a funcionar. A consulta acima mostra o valor de contra-valor por instância, e pode ser usada para comparar o desempenho de diferentes instâncias do servidor.

## <a name="alerts"></a>Alertas
À semelhança de outras métricas, pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de eventos sair de um limite que especifica. Abra o painel de alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver EventCounters em Métricas Ao Vivo?

As Métricas Ao Vivo não mostram EventCounters a partir de hoje. Utilize o Metric Explorer ou o Analytics para ver a telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Que plataformas posso ver a lista padrão de contadores .NET Core 3.0?

EventCounter não requer nenhuma permissão especial, e é suportado em todas as plataformas .NET Core 3.0 é suportado. Isto inclui:

* **Sistema operativo**: Windows, Linux ou macOS.
* Método de **hospedagem**: Em processo ou fora de processo.
* **Método de implantação**: Dependente da estrutura ou independente.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: A funcionalidade de Aplicações Web do Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Ativei insights de aplicação do Portal da Aplicação Web Azure. Mas não consigo ver eventcounters.?

 [A extensão de Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) de Aplicação para ASP.NET Core ainda não suporta esta funcionalidade. Este documento será atualizado quando esta funcionalidade for suportada.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Rastreio de dependência](../../azure-monitor/app/asp-net-dependencies.md)
