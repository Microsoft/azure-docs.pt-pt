---
title: Contadores de desempenho no Application Insights | Documentos da Microsoft
description: Monitorize o sistema e de contadores de desempenho do .NET personalizados no Application Insights.
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
ms.openlocfilehash: cff4aaaab97fdcecab9cdf1d0dff2786f86b604b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966711"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights

O Windows fornece uma grande variedade de [contadores de desempenho](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), como a ocupação da CPU, memória, disco e utilização da rede. Também pode definir seus próprios contadores de desempenho. Recolha de contadores de desempenho é suportada, desde que seu aplicativo seja executado sob o IIS num anfitrião no local ou máquina virtual para o qual tem acesso administrativo. No entanto aplicativos executados como aplicações Web do Azure não tem acesso direto aos contadores de desempenho, um subconjunto de contadores disponíveis são recolhidos pelo Application Insights.

## <a name="view-counters"></a>Contadores do Vista

O painel de métricas mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho comunicados no Application Insights](./media/performance-counters/performance-counters.png)

Os contadores de predefinição atual que estão configurados para serem recolhidos para aplicativos de web de principais de ASP.NET/ASP.NET são:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Para ver todos os seus gráficos mais úteis num único lugar, crie uma [dashboard](../../azure-monitor/app/app-insights-dashboards.md) e afixá-los à mesma.

## <a name="add-counters"></a>Adicionar contadores

Se pretender que o contador de desempenho não está incluído na lista de métricas, pode adicioná-lo.

1. Descubra quais contadores estão disponíveis no seu servidor, utilizando este comando do PowerShell no servidor local:

    `Get-Counter -ListSet *`

    (Consulte [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Abra o applicationinsights. config.

   * Se tiver adicionado o Application Insights à sua aplicação durante o desenvolvimento, edite applicationinsights. config no seu projeto e, em seguida, voltar a implementá-lo para os seus servidores.
3. Edite a diretiva de recoletor de desempenho:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Não tem aplicações ASP.NET Core `ApplicationInsights.config`, e, por conseguinte, o método acima não é válido para aplicativos do ASP.NET Core.

Pode capturar contadores padrão tanto quanto os que implementou por conta própria. `\Objects\Processes` é um exemplo de um contador padrão que está disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado num serviço web.

O formato é `\Category(instance)\Counter"`, ou para as categorias que não têm instâncias, just `\Category\Counter`.

`ReportAs` é necessário para nomes de contador que se neshodují `[a-zA-Z()/-_ \.]+` -ou seja, contêm carateres que não estão a ser os seguintes conjuntos: letras, gira entre colchetes, barra, hífen, caráter de sublinhado, espaço, ponto.

Se especificar uma instância, serão recolhido como uma dimensão "CounterInstanceName" da métrica comunicada.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Recolha de contadores de desempenho no código para aplicações Web ASP.NET ou aplicativos de Console do.NET/.NET principais
Para recolher contadores de desempenho do sistema e enviá-los para o Application Insights, pode adaptar o trecho a seguir:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou pode fazer a mesma coisa com métricas personalizadas que criou:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Recolha de contadores de desempenho no código para aplicações Web do ASP.NET Core

Modificar `ConfigureServices` método na sua `Startup.cs` classe conforme mostrado a seguir.

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
Pode pesquisar e apresentar relatórios de contador de desempenho na [Analytics](../../azure-monitor/app/analytics.md).

O **performanceCounters** esquema expõe a `category`, `counter` nome, e `instance` nome de cada contador de desempenho.  A telemetria de cada aplicativo, verá apenas os contadores para essa aplicação. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/performance-counters/analytics-performance-counters.png)

('Instance' aqui refere-se para a instância do contador de desempenho, não a função ou instância de máquina do servidor. O nome de instância do contador de desempenho normalmente segmenta contadores como tempo de processador pelo nome do processo ou aplicação.)

Para obter um gráfico de memória disponível ao longo do período recente: 

![Pego de memória no analytics do Application Insights](./media/performance-counters/analytics-available-memory.png)

Como outra telemetria **performanceCounters** também tem uma coluna `cloud_RoleInstance` indica que a identidade de instância do servidor de anfitrião no qual a aplicação está em execução. Por exemplo, para comparar o desempenho da sua aplicação em diferentes computadores: 

![Desempenho segmentados por instância de função no Application Insights analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET e contagens de Application Insights

*O que é a diferença entre a taxa de exceções e métricas de exceções?*

* *Taxa de exceções* é um contador de desempenho do sistema. O CLR conta todas as exceções a processadas e não processadas que são geradas e divide o total num intervalo de amostragem pelo comprimento do intervalo. O SDK do Application Insights recolhe este resultado e envia-os para o portal.

* *Exceções* é uma contagem dos relatórios TrackException recebido pelo portal no intervalo de amostragem do gráfico. Ele inclui apenas as exceções manipuladas em que escreveu TrackException chama em seu código e não incluir a totalidade [exceções não processadas](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contadores de desempenho para aplicações executadas nas aplicações Web do Azure

Aplicativos ASP.NET e ASP.NET Core, implementados nas aplicações Web do Azure são executados num ambiente de área de segurança especiais. Este ambiente não permite acesso direto aos contadores de desempenho do sistema. No entanto, um subconjunto limitado das contadores são expostos como variáveis de ambiente, tal como descrito [aqui](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK para ASP.NET e ASP.NET Core recolhe os contadores de desempenho de aplicações Web do Azure destas variáveis de ambiente especial. Apenas um subconjunto dos contadores estão disponíveis neste ambiente e a lista completa pode ser encontrada [aqui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicativos do ASP.NET Core

* [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.4.1 e acima coletados contadores de desempenho se a aplicação está em execução numa aplicação Web do Azure (Windows)

* SDK versão 2.7.0-beta3 e acima coletados contadores de desempenho se a aplicação está em execução no Windows e direcionamento `NETSTANDARD2.0` ou superior.
* Para aplicativos visando o .NET Framework, os contadores de desempenho são suportados em todas as versões do SDK.
* Este artigo será atualizado quando é adicionado suporte de contador de desempenho no não Windows.

## <a name="alerts"></a>Alertas
Como outras métricas, pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de desempenho sair de um limite que especificar. Abrir o painel de alertas e clique em Adicionar alerta.

## <a name="next"></a>Passos seguintes

* [Rastreamento de dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Controlo de exceções](../../azure-monitor/app/asp-net-exceptions.md)

