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
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837614"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights

O Windows fornece uma grande variedade de [contadores de desempenho](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), como a ocupação da CPU, memória, disco e utilização da rede. Também pode definir seus próprios contadores de desempenho. Desde que seu aplicativo está em execução sob o IIS num anfitrião no local ou máquina virtual para o qual tem acesso administrativo.

## <a name="view-counters"></a>Contadores do Vista

O painel de métricas mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho comunicados no Application Insights](./media/performance-counters/performance-counters.png)

Os contadores de predefinição atual, que são recolhidos para aplicações web .NET são:

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
   * Se tiver utilizado o Monitor de estado para instrumentar uma aplicação web em tempo de execução, encontre o applicationinsights. config no diretório de raiz da aplicação no IIS. Atualize-o aí em cada instância de servidor.
3. Edite a diretiva de recoletor de desempenho:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Pode capturar contadores padrão tanto quanto os que implementou por conta própria. `\Objects\Processes` é um exemplo de um contador padrão que está disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado num serviço web. 

O formato é `\Category(instance)\Counter"`, ou para as categorias que não têm instâncias, just `\Category\Counter`.

`ReportAs` é necessário para nomes de contador que se neshodují `[a-zA-Z()/-_ \.]+` -ou seja, contêm carateres que não estão a ser os seguintes conjuntos: letras, gira entre colchetes, barra, hífen, caráter de sublinhado, espaço, ponto.

Se especificar uma instância, serão recolhido como uma dimensão "CounterInstanceName" da métrica comunicada.

### <a name="collecting-performance-counters-in-code"></a>Recolha de contadores de desempenho no código
Para recolher contadores de desempenho do sistema e enviá-los para o Application Insights, pode adaptar o trecho a seguir:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Ou pode fazer a mesma coisa com métricas personalizadas que criou:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
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

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicativos do ASP.NET Core
Contadores de desempenho são suportados apenas se o aplicativo se destina o .NET Framework completo. Não existe nenhuma capacidade para recolher contadores de desempenho para aplicações de .NET Core.

## <a name="alerts"></a>Alertas
Como outras métricas, pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de desempenho sair de um limite que especificar. Abrir o painel de alertas e clique em Adicionar alerta.

## <a name="next"></a>Passos seguintes
* [Rastreamento de dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Controlo de exceções](../../azure-monitor/app/asp-net-exceptions.md)

