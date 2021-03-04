---
title: Contadores de desempenho em | de Insights de Aplicação Microsoft Docs
description: Monitorar o sistema e os contadores de desempenho .NET personalizados em Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 423e17ef2b44286c28b464836075284929d8644c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031366"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema em Insights de Aplicações

O Windows fornece uma grande variedade de [contadores de desempenho](/windows/desktop/perfctrs/about-performance-counters), como a ocupação da CPU, memória, disco e utilização da rede. Também pode definir os seus próprios contadores de desempenho. A recolha de contadores de desempenho é suportada desde que a sua aplicação esteja a decorrer no IIS num anfitrião no local, ou numa máquina virtual à qual tem acesso administrativo. Embora as aplicações que executam como Azure Web Apps não tenham acesso direto aos contadores de desempenho, um subconjunto de contadores disponíveis são recolhidos pelo Application Insights.

## <a name="view-counters"></a>Ver balcões

O painel métrica mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho reportados em Insights de Aplicação](./media/performance-counters/performance-counters.png)

Os contadores predefinidos atuais que são configurados para serem recolhidos para ASP.NET aplicações web são:
- % \\ Tempo do processador de processo
- % Tempo de processador de processo \\ normalizado
- \\Bytes disponíveis para memória
- Pedidos de ASP.NET/Sec
- .NET CLR Exceções lançadas /seg
- ASP.NET Aplicações Requere Tempo de Execução
- Processe \\ Bytes Privados
- Processe \\ IO Data Bytes/seg
- pedidos de ASP.NET \\ pedidos na fila de aplicações
- Tempo de processador (_Total) \\ % % tempo de processador

Os contadores predefinidos atuais que são configurados para serem recolhidos para ASP.NET aplicações web Core são:
- % \\ Tempo do processador de processo
- % Tempo de processador de processo \\ normalizado
- \\Bytes disponíveis para memória
- Processe \\ Bytes Privados
- Processe \\ IO Data Bytes/seg
- Tempo de processador (_Total) \\ % % tempo de processador

## <a name="add-counters"></a>Adicionar balcões

Se o contador de desempenho que deseja não estiver incluído na lista de métricas, pode adicioná-lo.

1. Descubra quais os contadores disponíveis no seu servidor utilizando este comando PowerShell no servidor local:

    `Get-Counter -ListSet *`

    (Ver [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter) .)
2. Abra ApplicationInsights.config.

   * Se adicionou Insights de Aplicação à sua aplicação durante o desenvolvimento, edite ApplicationInsights.config no seu projeto e, em seguida, reenfectue-o para os seus servidores.
3. Editar a diretiva relativa ao colecionador de desempenho:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET aplicações Core não `ApplicationInsights.config` têm, e, portanto, o método acima não é válido para ASP.NET Aplicações Core.

Pode capturar os contadores padrão e os que implementou a si mesmo. `\Objects\Processes` é um exemplo de um contador padrão que está disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado num serviço web.

O formato é `\Category(instance)\Counter"` , ou para categorias que não têm instâncias, apenas `\Category\Counter` .

`ReportAs` é necessário para os nomes de contadores que não `[a-zA-Z()/-_ \.]+` correspondem - isto é, contêm caracteres que não estão nos seguintes conjuntos: letras, suportes redondos, barra para a frente, hífen, sublinhado, espaço, ponto.

Se especificar um caso, será recolhido como uma dimensão "CounterInstanceName" da métrica relatada.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Recolha de contadores de desempenho em código para aplicações web ASP.NET ou aplicações de consola .NET/.NET Core
Para recolher os contadores de desempenho do sistema e enviá-los para o Application Insights, pode adaptar o corte abaixo:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou pode fazer a mesma coisa com as métricas personalizadas que criou:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Recolha de contadores de desempenho em código para ASP.NET Aplicações Web Core

Modifique `ConfigureServices` o método na sua classe como `Startup.cs` abaixo.

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

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho em Analytics
Pode pesquisar e exibir relatórios de contador de desempenho em [Analytics](../logs/log-query-overview.md).

O **esquema do PerformanceCounters** expõe o `category` nome e o nome de cada contador de `counter` `instance` desempenho.  Na telemetria para cada aplicação, verá apenas os contadores para essa aplicação. Por exemplo, para ver que contadores estão disponíveis: 

![Contadores de desempenho em análise de Insights de Aplicação](./media/performance-counters/analytics-performance-counters.png)

('Instance' aqui refere-se à instância do contador de desempenho, não à função, ou à instância da máquina do servidor. O nome do contador de desempenho normalmente segmenta contadores como o tempo do processador pelo nome do processo ou aplicação.)

Para obter um gráfico de memória disponível durante o período recente: 

![Timechart de memória na análise de Insights de Aplicação](./media/performance-counters/analytics-available-memory.png)

Tal como outras telemetrias, **o PerformanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor anfitrião em que a sua aplicação está a ser executada. Por exemplo, para comparar o desempenho da sua aplicação nas diferentes máquinas: 

![Desempenho segmentado por instância de função na análise de Insights de Aplicação](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET e Insights de Aplicação contam

*Qual é a diferença entre as métricas de exceção e exceções?*

* *A taxa de exceção* é um contador de desempenho do sistema. O CLR conta todas as exceções manuseadas e não manipuladas que são lançadas, e divide o total num intervalo de amostragem pelo comprimento do intervalo. O Application Insights SDK recolhe este resultado e envia-o para o portal.

* *Exceções* é uma contagem dos relatórios TrackException recebidos pelo portal no intervalo de amostragem do gráfico. Inclui apenas as exceções manuseadas onde escreveu chamadas TrackException no seu código, e não inclui todas as [exceções não manipuladas](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contadores de desempenho para aplicações em execução em Azure Web Apps

As aplicações ASP.NET e ASP.NET Core implementadas para a Azure Web Apps funcionam num ambiente especial de sandbox. Este ambiente não permite o acesso direto aos balcões de desempenho do sistema. No entanto, um subconjunto limitado de contadores são expostos como variáveis ambientais, conforme [descrito aqui.](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables) Application Insights SDK for ASP.NET and ASP.NET Core recolhe contadores de desempenho a partir de Azure Web Apps a partir destas variáveis ambientais especiais. Apenas um subconjunto de contadores está disponível neste ambiente, e a lista completa pode ser encontrada [aqui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicações core ASP.NET

Suporte para contadores de desempenho em ASP.NET Core é limitado:

* [As versões SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 e, posteriormente, recolhem contadores de desempenho se a aplicação estiver a ser executada em Azure Web Apps (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver a ser executada no Windows e alvos `NETSTANDARD2.0` ou posteriormente.
* Para aplicações direcionadas para o Quadro .NET, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posterior suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistema em Linux (e outros ambientes não-Windows) é usando [EventCounters](eventcounters.md)

## <a name="alerts"></a>Alertas
Tal como outras métricas, pode [definir um alerta](../alerts/alerts-log.md) para o avisar se um contador de desempenho sair do limite especificado. Abra o painel de alertas e clique em Adicionar Alerta.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Rastreio de dependência](./asp-net-dependencies.md)
* [Rastreio de exceções](./asp-net-exceptions.md)

