---
title: Dependência de controlo no Application Insights do Azure | Documentos da Microsoft
description: Monitorize as chamadas de dependência das suas instalações ou da aplicação web do Microsoft Azure com Application Insights.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 8fb1550a3f1d4b3336384139b049b60e23e648d7
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666246"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rastreio de dependência em insights de aplicação azure 

Uma *dependência* é um componente externo que é chamado pela sua aplicação. Normalmente, é um serviço chamado através de HTTP, ou uma base de dados ou um sistema de ficheiros. [Aplicação Insights](../../azure-monitor/app/app-insights-overview.md) mede a duração das chamadas de dependência, quer falhe ou não, juntamente com informações adicionais como o nome da dependência e assim por diante. Você pode investigar chamadas específicas de dependência, e correlacionar-las com pedidos e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências rastreadas automaticamente

Aplicação Insights SDKs para .NET e .NET Core navios com `DependencyTrackingTelemetryModule` que é um Módulo de Telemetria que recolhe automaticamente dependências. Esta recolha de dependência é ativada automaticamente para [aplicações ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) quando configuradas de acordo com os documentos oficiais ligados. `DependencyTrackingTelemetryModule` é enviado como [este](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet, e é trazido automaticamente quando se utiliza qualquer um dos pacotes NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` rastreia atualmente as seguintes dependências automaticamente:

|Dependências |Detalhes|
|---------------|-------|
|Http/Https | Chamadas locais ou remotas http/https |
|Chamadas wCF| Só é rastreado automaticamente se forem utilizadas encadernações baseadas em Http.|
|SQL | Chamadas feitas com `SqlClient`. Veja [isto](#advanced-sql-tracking-to-get-full-sql-query) para capturar a consulta SQL.  |
|[Armazenamento azure (Blob, Mesa, Fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o Cliente de Armazenamento Azure. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e acima. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e acima. |
|Azure Cosmos DB | Só é rastreado automaticamente se http/HTTPS for utilizado. Modo TCP não ser capturado pelo Application Insights. |

Se estiver a perder uma dependência, ou a usar um SDK diferente, certifique-se de que está na lista de [dependências recolhidas automaticamente.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) Se a dependência não for recolhida automaticamente, ainda pode rastreá-la manualmente com uma chamada de dependência da [via](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar rastreio automático de dependência em Apps de Consola

Para rastrear automaticamente as dependências das aplicações de consola .NET/.NET Core, instale o pacote Nuget `Microsoft.ApplicationInsights.DependencyCollector`, e inicialize `DependencyTrackingTelemetryModule` da seguinte forma:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Como funciona a monitorização automática da dependência?

As dependências são recolhidas automaticamente utilizando uma das seguintes técnicas:

* Utilizar a instrumentação do código byte em torno de métodos selecionados. (InstrumentationEngine quer do StatusMonitor quer da extensão da aplicação web azure)
* Chamadas EventSource
* Chamadas DiagnosticSource (nos mais recentes SDKs de Núcleo .NET/.NET)

## <a name="manually-tracking-dependencies"></a>Rastreio manual de dependências

Seguem-se alguns exemplos de dependências, que não são recolhidas automaticamente, e que, por isso, requerem rastreio manual.

* O Azure Cosmos DB só é rastreado automaticamente se for utilizado [http/HTTPS.](../../cosmos-db/performance-tips.md#networking) Modo TCP não ser capturado pelo Application Insights.
* Redis

Para essas dependências não recolhidas automaticamente pelo SDK, pode rastreá-las manualmente utilizando a [API TrackDependency](api-custom-events-metrics.md#trackdependency) que é utilizada pelos módulos padrão de recolha automática.

Por exemplo, se criar seu código com um assembly que não escrever por conta própria, tempo todas as chamadas para o mesmo, para descobrir o que faz para os tempos de resposta de contribuição. Para que estes dados sejam apresentados nos gráficos de dependência em Insights de Aplicação, envie-os utilizando `TrackDependency`.

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

Alternativamente, `TelemetryClient` fornece métodos de extensão `StartOperation` e `StopOperation` que podem ser usados para rastrear manualmente dependências, como mostrado [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se pretender desligar o módulo padrão de rastreio da dependência, remova a referência ao DependencyTrackingTelemettryModule em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicações ASP.NET. Para ASP.NET aplicações Core, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Rastreio de chamadas do AJAX a partir de Páginas Web

Para páginas web, Os Insights de Aplicação JavaScript SDK recolhem automaticamente as chamadas DOMT como dependências.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rastreio SQL avançado para obter consulta SQL completa

Para chamadas SQL, o nome do servidor e da base de dados é sempre recolhido e armazenado como nome da `DependencyTelemetry`recolhida . Há um campo adicional chamado "data", que pode conter o texto completo da consulta SQL.

Para ASP.NET aplicações Core, não há nenhum passo adicional necessário para obter a consulta SQL completa.

Para ASP.NET aplicações, a consulta Completa sQL é recolhida com a ajuda da instrumentação do código byte, que requer motor de instrumentação. São necessárias etapas adicionais específicas da plataforma, como descrito abaixo, são necessárias.

| Plataforma | Passo(s) Necessário para obter consulta SQL completa |
| --- | --- |
| Aplicação Web do Azure |No painel de controlo de aplicações web, [abra a lâmina Deinsights de Aplicação](../../azure-monitor/app/azure-web-apps.md) e ative comandos SQL em .NET |
| IIS Server (Azure VM, on-prem, e assim por diante.) | Utilize o Módulo PowerShell do Monitor de Estado para instalar o Motor de [Instrumentação](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) e reiniciar o IIS. |
| Serviço Cloud do Azure | Adicionar [tarefa de arranque para instalar o StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> A sua aplicação deve ser a bordo do ApplicationInsights SDK no momento da construção, instalando pacotes NuGet para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ou [ASP.NET aplicações Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Não suportado

Nos casos acima referidos, a forma correta de validar que o motor de instrumentação está corretamente instalado é validando que a versão SDK de `DependencyTelemetry` recolhida é 'rddp'. 'rdddsd' ou 'rddf' indica que as dependências são recolhidas através de chamadas DiagnosticSource ou EventSource, e portanto a consulta completa do SQL não será capturada.

## <a name="where-to-find-dependency-data"></a>Onde encontrar os dados de dependência

* O Mapa de [Aplicações](app-map.md) visualiza dependências entre a sua app e os componentes vizinhos.
* [O Diagnóstico de Transações](transaction-diagnostics.md) mostra dados de servidores unificados e correlacionados.
* [O separador browsers](javascript.md) mostra chamadas do AJAX dos navegadores dos seus utilizadores.
* Clique em pedidos lentos ou falhados para verificar as suas chamadas de dependência.
* [A análise](#logs-analytics) pode ser usada para consultar dados de dependência.

## <a name="diagnosis"></a>Diagnosticar pedidos lentos

Cada evento de pedido está associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto a sua aplicação está a processar o pedido. Portanto, se alguns pedidos estão a correr mal, podes descobrir se é por causa de respostas lentas de uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos para dependências

Abra o separador **Performance** e navegue para o separador **Dependencies** no topo ao lado das operações.

Clique num **Nome de Dependência** no geral. Depois de selecionar uma dependência, um gráfico da distribuição de durações dessa dependência aparecerá à direita.

![No separador de desempenho clique no separador Dependency no topo e depois um nome de Dependência na tabela](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique no botão **amostraazul** no canto inferior direito e, em seguida, numa amostra para ver os detalhes da transação de ponta a ponta.

![Clique numa amostra para ver os detalhes da transação final](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfil de seu site em direto

Não sabe onde o tempo passa? O [perfilde Insights](../../azure-monitor/app/profiler.md) de Aplicação rastreia chamadas HTTP para o seu site ao vivo e mostra-lhe as funções no seu código que demorou mais tempo.

## <a name="failed-requests"></a>Pedidos falhados

Também podem ser associados a chamadas falhadas para dependências de pedidos falhados.

Podemos ir ao separador **Falhas** à esquerda e clicar no separador **dependências** na parte superior.

![Clique no gráfico de pedidos falhados](./media/asp-net-dependencies/4-fail.png)

Aqui poderá ver a contagem falhada de dependência. Para obter mais detalhes sobre uma ocorrência falhada tentando clicar num nome de dependência na tabela inferior. Pode clicar no botão **Dependências azuis** na parte inferior direita para obter os detalhes da transação de ponta a ponta.

## <a name="logs-analytics"></a>Registos (Analytics)

Você pode rastrear dependências na [linguagem de consulta Kusto.](/azure/kusto/query/) Eis alguns exemplos.

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como é que o relatório de colecionador de dependência automática falhou nas chamadas para as dependências?*

* As chamadas de dependência falhadas terão campo de "sucesso" definido para Falso. `DependencyTrackingTelemetryModule` não reporta `ExceptionTelemetry`. O modelo completo de dados para a dependência é descrito [aqui.](data-model-dependency-telemetry.md)

## <a name="open-source-sdk"></a>SDK de código aberto
Como todos os Insights de Aplicação SDK, o módulo de recolha de dependência também é de código aberto. Leia e contribua para o código, ou reporte problemas no [repo oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passos seguintes

* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados do utilizador e da página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
