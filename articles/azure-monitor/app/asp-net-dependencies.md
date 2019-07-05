---
title: Dependência de controlo no Application Insights do Azure | Documentos da Microsoft
description: Chamadas de dependência de monitor da sua aplicação web do Microsoft Azure com o Application Insights ou no local.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565378"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Dependência de controlo no Azure Application Insights 

R *dependência* é um componente externo que é chamado pela sua aplicação. Normalmente, é um serviço chamado através de HTTP, ou uma base de dados ou um sistema de ficheiros. [O Application Insights](../../azure-monitor/app/app-insights-overview.md) calcula a duração das chamadas de dependência, se seu a falhar ou não, juntamente com informações adicionais, como o nome de dependência e assim por diante. Pode investigar chamadas de dependência específica e correlacioná-los para pedidos e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências automaticamente controladas

SDKs do Application Insights para .NET e .NET Core acompanha o `DependencyTrackingTelemetryModule` que é um módulo de telemetria que recolhe automaticamente as dependências. Esta coleção de dependência é ativada automaticamente para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplicativos, quando configurada de acordo com os documentos oficiais ligados. `DependencyTrackingTelemetryModule` é enviado como [isso](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet e ser colocado automaticamente ao utilizar qualquer um dos pacotes de NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` atualmente controla automaticamente as seguintes dependências:

|Dependências |Detalhes|
|---------------|-------|
|Http/Https | Local ou remoto chamadas http/https |
|Chamadas WCF| Apenas controlados automaticamente se forem utilizados enlaces baseado em Http.|
|SQL | As chamadas feitas com `SqlClient`. Ver [isso](##advanced-sql-tracking-to-get-full-sql-query) para capturar SQL de consulta.  |
|[Armazenamento do Azure (Blob, tabela, fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK de cliente de EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e acima. |
|[SDK de cliente de ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Apenas controlados automaticamente se for utilizado HTTP/HTTPS. Modo TCP não ser capturado pelo Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar dependência automática de controle em aplicativos de Console

Controlar automaticamente as dependências de aplicações de consola.NET/.NET Core, instalar o pacote Nuget `Microsoft.ApplicationInsights.DependencyCollector`e inicializar `DependencyTrackingTelemetryModule` da seguinte forma:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Dependência como automática, monitorização funciona?

As dependências são automaticamente recolhidas, utilizando uma das seguintes técnicas:

* Instrumentação de código de byte em torno de selecionadas métodos a utilizar. (InstrumentationEngine a partir do StatusMonitor ou extensão de aplicação Web do Azure)
* EventSource retornos de chamada
* DiagnosticSource retornos de chamada (nos SDKs de núcleo.NET/.NET mais recente)

## <a name="manually-tracking-dependencies"></a>Dependências de controle manualmente

Seguem-se alguns exemplos de dependências, que não são automaticamente recolhidos e, por conseguinte, requerem o controlo manual.

* O Azure Cosmos DB é controlado automaticamente apenas se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é utilizado. Modo TCP não ser capturado pelo Application Insights.
* Redis

Para essas dependências recolhidas automaticamente pelo SDK, pode controlá-las manualmente com o [TrackDependency API](api-custom-events-metrics.md#trackdependency) que é utilizado por módulos de coleção padrão automaticamente.

Por exemplo, se criar seu código com um assembly que não escrever por conta própria, tempo todas as chamadas para o mesmo, para descobrir o que faz para os tempos de resposta de contribuição. Para que estes dados apresentados nos gráficos de dependência no Application Insights, enviá-la usando `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Em alternativa, `TelemetryClient` fornece métodos de extensão `StartOperation` e `StopOperation` que podem ser utilizadas para controlar manualmente as dependências, como mostra a [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se quiser optar por desativar o módulo de controlo de dependência padrão, remova a referência DependencyTrackingTelemetryModule na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicativos ASP.NET. Para aplicativos do ASP.NET Core, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Controlo de chamadas de AJAX a páginas da Web

Para páginas da web, Application Insights JavaScript SDK recolhe automaticamente as chamadas AJAX como dependências conforme descrito [aqui](javascript.md#ajax-performance). Este documento centra-se as dependências de componentes de servidor.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avançada para obter a consulta SQL completa de controle SQL

Para chamadas SQL, o nome do servidor e a base de dados é sempre recolhido e armazenado como nome do recolhidos `DependencyTelemetry`. Existe um campo adicional chamado "dados", que podem conter o texto da consulta SQL completo.

Para aplicativos do ASP.NET Core, não existe nenhum passo adicional necessário para obter a consulta SQL completa.

Para aplicativos ASP.NET, a consulta SQL completa é recolhida com a ajuda de instrumentação de código de byte, que requer o motor de instrumentação. Específica da plataforma, conforme descrito abaixo, são necessários passos adicionais.

| Plataforma | Passos necessários para obter a consulta SQL completa |
| --- | --- |
| Aplicação Web do Azure |No seu painel de controlo de aplicação web, [abrir o painel do Application Insights](../../azure-monitor/app/azure-web-apps.md) e ativar comandos de SQL em .NET |
| Servidor IIS (a VM do Azure, no local e assim por diante.) | [Instale o Monitor de estado no seu servidor onde a aplicação está em execução](../../azure-monitor/app/monitor-performance-live-website-now.md) e reinicie o IIS.
| Serviço Cloud do Azure | Adicionar [tarefa de arranque para instalar StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> A aplicação deve ser incluído no SDK do Application Insights no momento da compilação ao instalar pacotes de NuGet para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ou [aplicativos do ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Não suportado

Nos casos acima, a forma correta de validar que o motor instrumentação é instalado corretamente é ao validar que a versão do SDK do recolhidos `DependencyTelemetry` é 'rddp'. 'rdddsd' ou "rddf" indica as dependências são coletadas por meio de retornos de chamada DiagnosticSource ou EventSource e, por conseguinte, não seja possível capturar consulta SQL completa.

## <a name="where-to-find-dependency-data"></a>Onde encontrar os dados de dependência

* [Mapa da aplicação](app-map.md) visualiza as dependências entre as suas aplicações e componentes vizinhos.
* [Diagnóstico de transação](transaction-diagnostics.md) mostra unificação, correlacionado dados do servidor.
* [Separador de navegadores](javascript.md#ajax-performance) mostra as chamadas AJAX de browsers dos seus utilizadores.
* Clique em dos pedidos lentos ou com falhas para verificar as suas chamadas de dependência.
* [Análise](#logs-analytics) pode ser usado para consultar dados de dependência.

## <a name="diagnosis"></a> Diagnosticar pedidos lentos

Cada evento do pedido está associado com as chamadas de dependência, exceções e outros eventos que são controlados enquanto a aplicação está a processar o pedido. Portanto, se alguns pedidos estiver fazendo incorretamente, pode descobrir seja devido a respostas lentas do que uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos para dependências

Abra o **desempenho** separador e navegue para o **dependências** separador na parte superior ao lado de operações.

Clique num **nome da dependência** em geral. Depois de selecionar uma dependência de um gráfico de distribuição dessa dependência das durações será exibido à direita.

![No desempenho separador, clique no separador na parte superior, em seguida, um nome de dependência no gráfico de dependência](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique na azul **amostras** botão à direita na parte inferior e, em seguida, de uma amostra para ver os detalhes de transação de ponta a ponta.

![Clique num exemplo para ver os detalhes de transação de ponta a ponta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfil de seu site em direto

Não sabe onde o tempo passa? O [criador de perfil do Application Insights](../../azure-monitor/app/profiler.md) rastreios HTTP chama-se ao seu site em direto e mostra-lhe as funções em seu código que demorou muito tempo.

## <a name="failed-requests"></a>Pedidos falhados

Também podem ser associados a chamadas falhadas para dependências de pedidos falhados.

Podemos Ir para o **falhas** separador no lado esquerdo e, em seguida, clique nas **dependências** separador na parte superior.

![Clique no gráfico de pedidos falhados](./media/asp-net-dependencies/4-fail.png)

Aqui, poderá ver a contagem de dependência falhadas. Para obter mais detalhes sobre uma ocorrência de falha ao tentar clicando num nome de dependência na tabela na parte inferior. Pode clicar na azul **dependências** botão à direita na parte inferior para obter os detalhes de transação de ponta a ponta.

## <a name="logs-analytics"></a>Registos (análise)

Pode controlar as dependências no [linguagem de consulta de Kusto](/azure/kusto/query/). Eis alguns exemplos.

* Encontre quaisquer chamadas de dependência falhadas:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Encontre as chamadas AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Encontre associadas a pedidos de chamadas de dependência:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Encontre as chamadas AJAX associadas a vistas de página:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como o relatório de recoletor de dependência automática faz falha chamadas para dependências?*

* Chamadas de dependência falhadas terão o campo de "êxito" definido como False. `DependencyTrackingTelemetryModule` não comunica `ExceptionTelemetry`. O modelo de dados completa de dependência é descrito [aqui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
Como cada SDK do Application Insights, o módulo de recolha de dependência também é aberto. Ler e contribuir para o código ou reportar problemas na [o repositório do GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passos Seguintes

* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados de utilizador e página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
