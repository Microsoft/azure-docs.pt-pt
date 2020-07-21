---
title: Contadores de eventos em Application Insights / Microsoft Docs
description: Monitor e personalizado .NET/.NET Core EventCounters in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 06bf15bf60b1ee5e2c301935a30b3981d5233a08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539946"
---
# <a name="eventcounters-introduction"></a>Introdução do EventCounters

`EventCounter`é .NET/.NET Mecanismo central para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento dá uma visão geral `EventCounters` e exemplos sobre como publicá-los e consumi-los. Os EventCounters são suportados em todas as plataformas DE - Windows, Linux e macOS. Pode ser considerado como um equivalente transversal para os [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) que só é suportado em sistemas Windows.

Embora os utilizadores possam publicar qualquer costume `EventCounters` para satisfazer as suas necessidades, o prazo de execução .NET Core 3.0 publica um conjunto destes contadores por padrão. O documento percorrerá os passos necessários para recolher e visualizar `EventCounters` (definido pelo sistema ou definido pelo utilizador) em Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Utilização de Insights de Aplicações para recolher EventCounters

A Application Insights suporta a recolha `EventCounters` com o seu , que faz parte do pacote de `EventCounterCollectionModule` nugets recém-lançado [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`é automaticamente ativado quando se utiliza [o AspNetCore](asp-net-core.md) ou [o WorkerService](worker-service.md). `EventCounterCollectionModule`recolhe balcões com uma frequência de recolha não configurável de 60 segundos. Não são necessárias permissões especiais para recolher o EventCounters.

## <a name="default-counters-collected"></a>Balcões predefinidos recolhidos

Para aplicações em execução em .NET Core 3.0, os seguintes contadores são recolhidos automaticamente pelo SDK. O nome dos balcões será do formulário "Categoria/ Contador".

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
> Os contadores da categoria Microsoft.AspNetCore.Hosting são adicionados apenas em ASP.NET Aplicações Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizar balcões a serem recolhidos

O exemplo a seguir mostra como adicionar/remover contadores. Esta personalização seria feita no método da sua aplicação após a `ConfigureServices` recolha de telemetria Application Insights ser ativada utilizando `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()` . Segue-se um código de exemplo de uma aplicação Core ASP.NET. Para outro tipo de aplicações, consulte [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

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

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Metric Explorer

Para ver as métricas do EventCounter no [Metric Explorer,](../platform/metrics-charts.md)selecione application insights e escolha métricas baseadas em Log como espaço de nome métrico. Em seguida, as métricas EventCounter são exibidas na categoria Custom.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados em Insights de Aplicação](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de eventos em Analytics

Também pode pesquisar e exibir relatórios de contadores de [eventos](../log-query/log-query-overview.md)em Analytics, na tabela **CustomMetrics.**

Por exemplo, executar a seguinte consulta para ver que contadores são recolhidos e disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados em Insights de Aplicação](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um contador específico (por exemplo: `ThreadPool Completed Work Item Count` ) durante o período recente, executar a seguinte consulta.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único balcão em Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Tal como outras **telemetrias, a customMetrics** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor anfitrião em que a sua aplicação está a ser executada. A consulta acima mostra o valor de contador por instância, e pode ser usada para comparar desempenho de diferentes instâncias do servidor.

## <a name="alerts"></a>Alertas
Tal como outras métricas, pode [definir um alerta](../../azure-monitor/platform/alerts-log.md) para o avisar se um contador de eventos não for um limite especificado. Abra o painel de alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver o EventCounters em Métricas Ao Vivo?

As Métricas Ao Vivo não mostram o EventCounters a partir de hoje. Utilize o Metric Explorer ou Analytics para ver a telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Que plataformas posso ver a lista padrão de contadores .NET Core 3.0?

O EventCounter não necessita de permissões especiais e é suportado em todas as plataformas .NET Core 3.0 é suportado. O que está incluído:

* **Sistema operativo**: Windows, Linux ou macOS.
* **Método de hospedagem**: Em processo ou fora do processo.
* **Método de implantação**: Quadro dependente ou autossuficiente.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: As Aplicações Web apresentam o Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Tenho ativado insights de aplicações a partir do Portal de Aplicações Web Azure. Mas não posso ver o EventCounters.?

 [A extensão de Insights de Aplicação](./azure-web-apps.md) para ASP.NET Core ainda não suporta esta funcionalidade. Este documento será atualizado quando esta funcionalidade for suportada.

## <a name="next-steps"></a><a name="next"></a>Próximos passos

* [Rastreio de dependência](../../azure-monitor/app/asp-net-dependencies.md)
