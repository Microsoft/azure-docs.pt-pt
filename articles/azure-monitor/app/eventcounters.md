---
title: Contadores de eventos em | de Insights de Aplicação Microsoft Docs
description: Monitor e personalizado .NET/.NET Core EventCounters in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579526"
---
# <a name="eventcounters-introduction"></a>Introdução do EventCounters

[`EventCounter`](/dotnet/core/diagnostics/event-counters) é .NET/.NET Mecanismo central para publicar e consumir contadores ou estatísticas. Os EventCounters são suportados em todas as plataformas DE - Windows, Linux e macOS. Pode ser considerado como um equivalente transversal para os [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) que só é suportado em sistemas Windows.

Embora os utilizadores possam publicar qualquer costume `EventCounters` para satisfazer as suas necessidades, .NET Core 3.0 e o tempo de execução mais elevado publica um conjunto destes contadores por padrão. Este documento percorrerá os passos necessários para recolher e visualizar `EventCounters` (definido pelo sistema ou definido pelo utilizador) em Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Utilização de Insights de Aplicações para recolher EventCounters

A Application Insights suporta a recolha `EventCounters` com o seu , que faz parte do `EventCounterCollectionModule` recém-lançado pacote NuGet [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` é automaticamente ativado quando se utiliza [o AspNetCore](asp-net-core.md) ou [o WorkerService](worker-service.md). `EventCounterCollectionModule` recolhe balcões com uma frequência de recolha não configurável de 60 segundos. Não são necessárias permissões especiais para recolher o EventCounters.

## <a name="default-counters-collected"></a>Balcões predefinidos recolhidos

A partir da versão 2.15.0 do [AspNetCore SDK](asp-net-core.md) ou [do WorkerService SDK,](worker-service.md)não são recolhidos contadores por padrão. O módulo em si está ativado, para que os utilizadores possam simplesmente adicionar os contadores desejados para os recolher.

Para obter uma lista de contadores bem conhecidos publicados pelo .NET Runtime, consulte o documento ['Contadores Disponíveis'.](/dotnet/core/diagnostics/event-counters#available-counters)

## <a name="customizing-counters-to-be-collected"></a>Personalizar balcões a serem recolhidos

O exemplo a seguir mostra como adicionar/remover contadores. Esta personalização seria feita no método da sua aplicação após a `ConfigureServices` recolha de telemetria Application Insights ser ativada utilizando `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()` . Segue-se um código de exemplo de uma aplicação Core ASP.NET. Para outro tipo de aplicações, consulte [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Módulo de recolha de Eventos Desativado

`EventCounterCollectionModule` pode ser desativado utilizando `ApplicationInsightsServiceOptions` . Um exemplo ao utilizar ASP.NET Core SDK é mostrado abaixo.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Uma abordagem semelhante também pode ser usada para o WorkerService SDK, mas o espaço de nome deve ser alterado como mostrado no exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Metric Explorer

Para ver as métricas do EventCounter no [Metric Explorer,](../essentials/metrics-charts.md)selecione application insights e escolha métricas baseadas em Log como espaço de nome métrico. Em seguida, as métricas EventCounter são exibidas na categoria Custom.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights Metric Explorer](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de eventos em Analytics

Também pode pesquisar e exibir relatórios de contadores de [eventos](../logs/log-query-overview.md)em Analytics, na tabela **CustomMetrics.**

Por exemplo, executar a seguinte consulta para ver que contadores são recolhidos e disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights Analytics](./media/event-counters/analytics-event-counters.png)

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
Tal como outras métricas, pode [definir um alerta](../alerts/alerts-log.md) para o avisar se um contador de eventos não for um limite especificado. Abra o painel de alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver o EventCounters em Métricas Ao Vivo?

As Métricas Ao Vivo não mostram o EventCounters a partir de hoje. Utilize o Metric Explorer ou Analytics para ver a telemetria.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Tenho ativado insights de aplicações a partir do Portal de Aplicações Web Azure. Mas não posso ver o EventCounters.?

 [A extensão de Insights de Aplicação](./azure-web-apps.md) para ASP.NET Core ainda não suporta esta funcionalidade. Este documento será atualizado quando esta funcionalidade for suportada.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Rastreio de dependência](./asp-net-dependencies.md)

