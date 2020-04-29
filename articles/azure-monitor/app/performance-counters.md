---
title: Contadores de desempenho em Insights de Aplicação / Microsoft Docs
description: Monitorize sistema e contadores de desempenho personalizados .NET em Insights de Aplicação.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669884"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema em Insights de Aplicação

O Windows fornece uma grande variedade de [contadores de desempenho](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), como a ocupação da CPU, memória, disco e utilização da rede. Também pode definir os seus próprios contadores de desempenho. A recolha de contadores de desempenho é suportada desde que a sua aplicação esteja em execução sob o IIS num hospedeiro no local ou numa máquina virtual à qual tenha acesso administrativo. Embora as aplicações que executam como As Aplicações Web Do Azure não tenham acesso direto aos contadores de desempenho, um subconjunto de contadores disponíveis é recolhido pela Application Insights.

## <a name="view-counters"></a>Ver balcões

O painel das Métricas mostra o conjunto padrão dos contadores de desempenho.

![Contadores de desempenho reportados em Insights de Aplicação](./media/performance-counters/performance-counters.png)

Os contadores padrão atuais que estão configurados para serem recolhidos para ASP.NET/ASP.NET aplicações web core são:
- Tempo\\de processador de processo %
- Tempo\\de processador de processo % normalizado
- Bytes de Memória\\Disponíveis
- pedidos ASP.NET/Sec
- .NET CLR Exceções Lançadas / seg
- ASP.NET AplicaçõesSolicitar o tempo de execução
- Processo\\Bytes Privados
- Processar\\Bytes/sede de dados IO
- pedidos de\\pedidos de ASP.NET na fila de candidaturas
- Tempo de\\processador(_Total) % do processador

## <a name="add-counters"></a>Adicionar contadores

Se o contador de desempenho que deseja não estiver incluído na lista de métricas, pode adicioná-lo.

1. Descubra quais os contadores disponíveis no seu servidor utilizando este comando PowerShell no servidor local:

    `Get-Counter -ListSet *`

    (Ver.) [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)
2. Open ApplicationInsights.config.

   * Se adicionou Insights de Aplicação à sua aplicação durante o desenvolvimento, edite ApplicationInsights.config no seu projeto e, em seguida, recoloque-o para os seus servidores.
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
> ASP.NET aplicações Core `ApplicationInsights.config`não têm , e por isso o método acima não é válido para aplicações centrais ASP.NET.

Podecapturar os dois contadores padrão e os que implementou. `\Objects\Processes`é um exemplo de um contador padrão que está disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold`é um exemplo de um contador personalizado que pode ser implementado num serviço web.

O formato é `\Category(instance)\Counter"`, ou para categorias que `\Category\Counter`não têm instâncias, apenas .

`ReportAs`é necessário para contra-nomes `[a-zA-Z()/-_ \.]+` que não correspondam - isto é, eles contêm caracteres que não estão nos seguintes conjuntos: letras, parênteses redondos, barra para a frente, hífen, sublinhado, espaço, ponto.

Se especificar uma instância, será recolhida como uma dimensão "CounterInstanceName" da métrica reportada.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Recolha de contadores de desempenho em código para aplicações web ASP.NET ou .NET/.NET Aplicações de consola seleções
Para recolher contadores de desempenho do sistema e enviá-los para Application Insights, pode adaptar o corte abaixo:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou pode fazer a mesma coisa com métricas personalizadas que criou:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Recolha de contadores de desempenho em código para aplicações Web ASP.NET Core

Modifique `ConfigureServices` o `Startup.cs` método na sua classe como abaixo.

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
Pode pesquisar e exibir relatórios de contador de desempenho no [Analytics](../../azure-monitor/app/analytics.md).

O **esquema de desempenhoCounters** `category`expõe `counter` o `instance` nome e o nome de cada contador de desempenho.  Na telemetria para cada aplicação, você verá apenas os contadores para essa aplicação. Por exemplo, para ver que contadores estão disponíveis: 

![Contadores de desempenho na análise de Insights de Aplicação](./media/performance-counters/analytics-performance-counters.png)

('Instance' aqui refere-se à contra-instância de desempenho, não à função, ou à instância da máquina do servidor. O nome da contra-instância de desempenho normalmente segmenta contadores como o tempo do processador pelo nome do processo ou aplicação.)

Para obter um gráfico de memória disponível durante o período recente: 

![Gráfico de memória na análise de Insights de Aplicação](./media/performance-counters/analytics-available-memory.png)

Tal como outras telemetrias, `cloud_RoleInstance` o **desempenhoCounters** também tem uma coluna que indica a identidade da instância do servidor anfitrião em que a sua aplicação está a funcionar. Por exemplo, comparar o desempenho da sua aplicação nas diferentes máquinas: 

![Desempenho segmentado por exemplo de função na análise de Insights de Aplicação](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET e Informações de Aplicação conta

*Qual é a diferença entre a taxa de exceção e as métricas de exceções?*

* *A taxa de exceção* é um contador de desempenho do sistema. O CLR conta todas as exceções manipuladas e não manipuladas que são lançadas, e divide o total num intervalo de amostragem pelo comprimento do intervalo. O SDK Application Insights recolhe este resultado e envia-o para o portal.

* *Exceções* é uma contagem dos relatórios TrackException recebidos pelo portal no intervalo de amostragem do gráfico. Inclui apenas as exceções manuseadas quando tiver escrito chamadas TrackException no seu código, e não inclui todas as [exceções não tratadas](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contadores de desempenho para aplicações em execução em Web Apps Azure

Tanto as ASP.NET como as ASP.NET aplicações Core implementadas para Web Apps Azure funcionam num ambiente especial de caixa de areia. Este ambiente não permite o acesso direto aos contadores de desempenho do sistema. No entanto, um subconjunto limitado de contadores é exposto como variáveis ambientais como descrito [aqui](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK for ASP.NET and ASP.NET Core recolhe contadores de desempenho de Web Apps Azure a partir destas variáveis ambientais especiais. Apenas um subconjunto de contadores está disponível neste ambiente, e a lista completa pode ser consultada [aqui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicações ASP.NET Core

O suporte para contadores de desempenho em ASP.NET Core é limitado:

* As versões [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 e posteriormente recolhem contadores de desempenho se a aplicação estiver em execução em Aplicações Web Do Azure (Windows).
* As versões SDK 2.7.1 e posteriormente recolhem contadores de `NETSTANDARD2.0` desempenho se a aplicação estiver a ser recorrida no Windows e alvos ou posteriormente.
* Para aplicações direcionadas para o .NET Framework, todas as versões dos contadores de desempenho de suporte SDK.
* Versões SDK 2.8.0 e posteriormente suporte cpu/contador de memória em Linux. Nenhum outro contador é apoiado em Linux. A forma recomendada de obter contadores de sistemas em Linux (e outros ambientes não Windows) é usando [EventCounters](eventcounters.md)

## <a name="alerts"></a>Alertas
À semelhança de outras métricas, pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de desempenho sair de um limite que especifica. Abra o painel de alertas e clique em Adicionar Alerta.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

* [Rastreio de dependência](../../azure-monitor/app/asp-net-dependencies.md)
* [Rastreio de exceções](../../azure-monitor/app/asp-net-exceptions.md)

