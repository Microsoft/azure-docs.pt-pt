---
title: Dependência de controlo no Application Insights do Azure | Documentos da Microsoft
description: Monitore chamadas de dependência de seu aplicativo Web local ou Microsoft Azure com Application Insights.
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
ms.openlocfilehash: 5e07243720872ff4555d4c000dcb7b0b7236e66f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126746"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rastreamento de dependência no insights Aplicativo Azure 

R *dependência* é um componente externo que é chamado pela sua aplicação. Normalmente, é um serviço chamado através de HTTP, ou uma base de dados ou um sistema de ficheiros. [Application insights](../../azure-monitor/app/app-insights-overview.md) mede a duração de chamadas de dependência, se sua falha ou não, juntamente com informações adicionais, como nome de dependência e assim por diante. Você pode investigar chamadas de dependência específicas e correlacioná-las a solicitações e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências rastreadas automaticamente

Os SDKs do Application insights para .net e .NET Core `DependencyTrackingTelemetryModule` são fornecidos com o, que é um módulo de telemetria que coleta automaticamente as dependências. Essa coleção de dependências é habilitada automaticamente para aplicativos [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) , quando configurada de acordo com os documentos oficiais vinculados. é enviado como [este](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet e é colocado automaticamente ao usar qualquer um dos pacotes `Microsoft.ApplicationInsights.Web` NuGet ou. `Microsoft.ApplicationInsights.AspNetCore` `DependencyTrackingTelemetryModule`

 `DependencyTrackingTelemetryModule`atualmente rastreia as seguintes dependências automaticamente:

|Dependências |Detalhes|
|---------------|-------|
|Http/Https | Chamadas http/https locais ou remotas |
|Chamadas do WCF| Rastreado automaticamente se as associações baseadas em http forem usadas.|
|SQL | Chamadas feitas com `SqlClient`. Consulte [isto](#advanced-sql-tracking-to-get-full-sql-query) para capturar a consulta SQL.  |
|[Armazenamento do Azure (BLOB, tabela, fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK do cliente do EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e posterior. |
|[SDK do cliente do ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Rastreado automaticamente se HTTP/HTTPS for usado. Modo TCP não ser capturado pelo Application Insights. |

Se você não tiver uma dependência ou se estiver usando um SDK diferente, verifique se ele está na lista de [dependências coletadas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se a dependência não for coletada automaticamente, você ainda poderá rastreá-la manualmente com uma [chamada de dependência de faixa](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar o rastreamento automático de dependência em aplicativos de console

Para controlar automaticamente as dependências de aplicativos de console .net/.NET Core, instale `Microsoft.ApplicationInsights.DependencyCollector`o pacote NuGet `DependencyTrackingTelemetryModule` e inicialize da seguinte maneira:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Como funciona o monitoramento de dependência automática?

As dependências são coletadas automaticamente usando uma das seguintes técnicas:

* Usando a instrumentação de código de byte em volta dos métodos Select. (InstrumentationEngine de StatusMonitor ou extensão de aplicativo Web do Azure)
* Retornos de chamada EventSource
* Retornos de chamada de diagnosticm (nos SDKs .NET/.NET Core mais recentes)

## <a name="manually-tracking-dependencies"></a>Controlando dependências manualmente

Veja a seguir alguns exemplos de dependências, que não são coletadas automaticamente e, portanto, exigem acompanhamento manual.

* O Azure Cosmos DB é controlado automaticamente apenas se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é utilizado. Modo TCP não ser capturado pelo Application Insights.
* Redis

Para essas dependências não coletadas automaticamente pelo SDK, você pode rastreá-las manualmente usando a [API do TrackDependency](api-custom-events-metrics.md#trackdependency) que é usada pelos módulos de coleção automática padrão.

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

Como alternativa, `TelemetryClient` fornece métodos `StartOperation` de extensão `StopOperation` e que podem ser usados para controlar manualmente as dependências, como mostrado [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se você quiser desativar o módulo de rastreamento de dependência padrão, remova a referência a DependencyTrackingTelemetryModule em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicativos ASP.net. Para ASP.NET Core aplicativos, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Controlando chamadas AJAX de páginas da Web

Para páginas da Web, Application Insights SDK do JavaScript coleta automaticamente chamadas AJAX como dependências.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Acompanhamento de SQL avançado para obter a consulta SQL completa

Para chamadas SQL, o nome do servidor e do banco de dados é sempre coletado e armazenado como o `DependencyTelemetry`nome do coletado. Há um campo adicional chamado ' data ', que pode conter o texto completo da consulta SQL.

Para aplicativos ASP.NET Core, não há nenhuma etapa adicional necessária para obter a consulta SQL completa.

Para aplicativos ASP.NET, a consulta SQL completa é coletada com a ajuda da instrumentação de código de byte, que requer o mecanismo de instrumentação. As etapas adicionais específicas da plataforma, conforme descrito abaixo, são necessárias.

| Plataforma | Etapas necessárias para obter a consulta SQL completa |
| --- | --- |
| Aplicação Web do Azure |No painel de controle do aplicativo Web, [abra a folha Application insights](../../azure-monitor/app/azure-web-apps.md) e habilite os comandos SQL no .net |
| Servidor IIS (VM do Azure, local e assim por diante). | Use o módulo Status Monitor PowerShell para [instalar o mecanismo de instrumentação](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) e reiniciar o IIS. |
| Serviço Cloud do Azure | Adicionar [tarefa de inicialização para instalar o StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Seu aplicativo deve ser integrado ao SDK do ApplicationInsights no momento da compilação Instalando pacotes NuGet para aplicativos [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ou [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Não suportado

Nos casos acima, a maneira correta de validar esse mecanismo de instrumentação está instalada corretamente é Validando que a versão do SDK coletada `DependencyTelemetry` é ' rddp '. ' rdddsd ' ou ' rddf ' indica que as dependências são coletadas por meio de retornos de chamada de Diagnostic ou EventSource e, portanto, a consulta SQL completa não será capturada.

## <a name="where-to-find-dependency-data"></a>Onde encontrar os dados de dependência

* [Mapa da aplicação](app-map.md) visualiza as dependências entre as suas aplicações e componentes vizinhos.
* O [diagnóstico de transação](transaction-diagnostics.md) mostra dados unificados e correlacionados do servidor.
* [Guia navegadores](javascript.md) mostra chamadas AJAX dos navegadores dos seus usuários.
* Clique em solicitações lentas ou com falha para verificar suas chamadas de dependência.
* [Análise](#logs-analytics) pode ser usado para consultar dados de dependência.

## <a name="diagnosis"></a> Diagnosticar pedidos lentos

Cada evento de solicitação é associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto seu aplicativo está processando a solicitação. Portanto, se algumas solicitações estiverem fazendo mal, você poderá descobrir se é devido a respostas lentas de uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos para dependências

Abra a guia **desempenho** e navegue até a guia **dependências** na parte superior ao lado de operações.

Clique em um **nome de dependência** em geral. Depois de selecionar uma dependência, um grafo da distribuição de durações da dependência será exibido à direita.

![Na guia desempenho, clique na guia dependência na parte superior e, em seguida, um nome de dependência no gráfico](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique no botão **amostras** azuis na parte inferior direita e, em seguida, em um exemplo para ver os detalhes da transação de ponta a ponta.

![Clique em uma amostra para ver os detalhes da transação de ponta a ponta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfil de seu site em direto

Não sabe onde o tempo passa? O [criador de perfil Application insights](../../azure-monitor/app/profiler.md) RASTREIA chamadas http para seu site ativo e mostra as funções em seu código que levaram a hora mais longa.

## <a name="failed-requests"></a>Pedidos falhados

Também podem ser associados a chamadas falhadas para dependências de pedidos falhados.

Podemos ir até a guia **falhas** à esquerda e, em seguida, clicar na guia dependências na parte superior.

![Clique no gráfico de pedidos falhados](./media/asp-net-dependencies/4-fail.png)

Aqui você poderá ver a contagem de dependências com falha. Para obter mais detalhes sobre uma ocorrência com falha ao tentar clicar em um nome de dependência na tabela inferior. Você pode clicar no botão de **dependências** azuis na parte inferior direita para obter os detalhes da transação de ponta a ponta.

## <a name="logs-analytics"></a>Logs (análise)

Você pode controlar as dependências na [linguagem de consulta Kusto](/azure/kusto/query/). Eis alguns exemplos.

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como o coletor de dependências automáticas relatam chamadas com falha para dependências?*

* As chamadas de dependência com falha terão o campo ' êxito ' definido como falso. `DependencyTrackingTelemetryModule`não relata `ExceptionTelemetry`. O modelo de dados completo para dependência é descrito [aqui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
Assim como todos os Application Insights SDK, o módulo de coleta de dependência também é de código-fonte aberto. Leia e contribua com o código ou relate problemas no [repositório do GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passos seguintes

* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados de utilizador e página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
