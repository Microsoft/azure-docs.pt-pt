---
title: Contadores de desempenho no Application Insights | Microsoft Docs
description: Monitore os contadores de desempenho .NET personalizados e de sistema no Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: c681b58b01979b95e35ae57cefde38c56a787543
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68360247"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights

O Windows fornece uma grande variedade de [contadores de desempenho](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), como a ocupação da CPU, memória, disco e utilização da rede. Você também pode definir seus próprios contadores de desempenho. A coleta de contadores de desempenho tem suporte, desde que seu aplicativo esteja sendo executado no IIS em um host local ou em uma máquina virtual para a qual você tenha acesso administrativo. Embora os aplicativos executados como aplicativos Web do Azure não tenham acesso direto aos contadores de desempenho, um subconjunto de contadores disponíveis é coletado pelo Application Insights.

## <a name="view-counters"></a>Exibir contadores

O painel métricas mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho relatados no Application Insights](./media/performance-counters/performance-counters.png)

Os contadores padrão atuais que estão configurados para serem coletados para aplicativos Web ASP.NET/ASP.NET Core são:
- % De\\tempo do processador de processo
- % De\\tempo do processador de processo normalizado
- Memória\\-bytes disponíveis
- Solicitações ASP.NET/s
- Exceções do .NET CLR lançadas/s
- ASP.NET ApplicationsRequest tempo de execução
- Processar\\bytes particulares
- Processar\\bytes de dados de es/s
- Solicitações de\\aplicativos ASP.net na fila de aplicativos
- Processador (_ total\\)% tempo do processador

## <a name="add-counters"></a>Adicionar contadores

Se o contador de desempenho desejado não estiver incluído na lista de métricas, você poderá adicioná-lo.

1. Descubra quais contadores estão disponíveis no servidor usando este comando do PowerShell no servidor local:

    `Get-Counter -ListSet *`

    (Consulte [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Abra ApplicationInsights. config.

   * Se você adicionou Application Insights ao seu aplicativo durante o desenvolvimento, edite ApplicationInsights. config em seu projeto e, em seguida, reimplante-o nos servidores.
3. Edite a diretiva do coletor de desempenho:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core aplicativos não têm `ApplicationInsights.config`e, portanto, o método acima não é válido para aplicativos ASP.NET Core.

Você pode capturar os contadores padrão e os que você implementou por conta própria. `\Objects\Processes`é um exemplo de um contador padrão que está disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold`é um exemplo de um contador personalizado que pode ser implementado em um serviço Web.

O formato é `\Category(instance)\Counter"`, ou para categorias que não têm instâncias, apenas `\Category\Counter`.

`ReportAs`é necessário para nomes de contadores que não correspondem `[a-zA-Z()/-_ \.]+` , ou seja, eles contêm caracteres que não estão nos seguintes conjuntos: letras, colchetes, barra invertida, hífen, sublinhado, espaço, ponto.

Se você especificar uma instância, ela será coletada como uma "cominstancename" de dimensão da métrica relatada.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Coleta de contadores de desempenho no código para aplicativos Web ASP.NET ou aplicativos de console .NET/.NET Core
Para coletar contadores de desempenho do sistema e enviá-los para Application Insights, você pode adaptar o trecho abaixo:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou você pode fazer a mesma coisa com métricas personalizadas que você criou:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Coleta de contadores de desempenho no código para aplicativos ASP.NET Core Web

Modifique `ConfigureServices` o método em `Startup.cs` sua classe, como mostrado abaixo.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho no Analytics
Você pode pesquisar e exibir relatórios de contador de desempenho no [Analytics](../../azure-monitor/app/analytics.md).

O esquema PerformanceCounters expõe o `category`, `counter` o nome e `instance` o nome de cada contador de desempenho.  Na telemetria para cada aplicativo, você verá apenas os contadores para esse aplicativo. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho no Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(' Instance ' aqui refere-se à instância do contador de desempenho, não à função ou à instância do computador do servidor. O nome da instância do contador de desempenho normalmente segmenta contadores, como tempo do processador, pelo nome do processo ou do aplicativo.)

Para obter um gráfico de memória disponível durante o período recente: 

![Gráfico de timememory na análise de Application Insights](./media/performance-counters/analytics-available-memory.png)

Assim como outras telemetrias, os PerformanceCounters `cloud_RoleInstance` também têm uma coluna que indica a identidade da instância do servidor host na qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em computadores diferentes: 

![Desempenho segmentado por instância de função na análise de Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Contagens de ASP.NET e Application Insights

*Qual é a diferença entre as métricas de taxa de exceção e exceções?*

* A *taxa de exceção* é um contador de desempenho do sistema. O CLR conta todas as exceções manipuladas e não tratadas que são geradas e divide o total em um intervalo de amostragem pelo comprimento do intervalo. O SDK do Application Insights coleta esse resultado e o envia para o Portal.

* *Exceções* é uma contagem dos relatórios trackexception recebidos pelo portal no intervalo de amostragem do gráfico. Ele inclui apenas as exceções manipuladas em que você escreveu as chamadas Trackexception em seu código e não inclui todas as [exceções sem tratamento](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contadores de desempenho para aplicativos em execução em aplicativos Web do Azure

Os aplicativos ASP.NET e ASP.NET Core implantados nos aplicativos Web do Azure são executados em um ambiente de área restrita especial. Esse ambiente não permite acesso direto aos contadores de desempenho do sistema. No entanto, um subconjunto limitado de contadores é exposto como variáveis de ambiente, conforme descrito [aqui](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK para ASP.NET e ASP.NET Core coleta contadores de desempenho de aplicativos Web do Azure a partir dessas variáveis de ambiente especiais. Somente um subconjunto de contadores está disponível nesse ambiente e a lista completa pode ser encontrada [aqui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicativos ASP.NET Core

* [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.4.1 e superior coleta contadores de desempenho se o aplicativo estiver em execução no aplicativo Web do Azure (Windows)

* O SDK versão 2.7.0-Beta3 e superior coleta contadores de desempenho se o aplicativo estiver em execução no Windows e `NETSTANDARD2.0` direcionamento ou superior.
* Para aplicativos direcionados à .NET Framework, os contadores de desempenho têm suporte em todas as versões do SDK.
* Este artigo será atualizado quando o suporte ao contador de desempenho no não Windows for adicionado.

## <a name="alerts"></a>Alertas
Assim como outras métricas, você pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de desempenho ficar fora de um limite especificado. Abra o painel alertas e clique em adicionar alerta.

## <a name="next"></a>Passos seguintes

* [Acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md)
* [Acompanhamento de exceção](../../azure-monitor/app/asp-net-exceptions.md)

