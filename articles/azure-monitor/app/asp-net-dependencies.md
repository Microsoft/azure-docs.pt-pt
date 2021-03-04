---
title: Rastreio de dependência em Azure Application Insights | Microsoft Docs
description: Monitorize as chamadas de dependência a partir das suas instalações ou da aplicação web microsoft Azure com Insights de Aplicação.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 124b8d3de59d1645379d50360e69a5fdbd5587e5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045297"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rastreio de dependência em Insights de Aplicação Azure 

Uma *dependência* é um componente que é chamado pela sua aplicação. Normalmente, é um serviço chamado através de HTTP, uma base de dados ou um sistema de ficheiros. [A Application Insights](./app-insights-overview.md) mede a duração das chamadas de dependência, quer falhe ou não, juntamente com informações adicionais como o nome da dependência e assim por diante. Pode investigar chamadas de dependência específicas e correlacioná-las com pedidos e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências rastreadas automaticamente

Insights de Aplicação SDKs para navios .NET e .NET Core `DependencyTrackingTelemetryModule` com, que é um Módulo de Telemetria que recolhe automaticamente dependências. Esta recolha de dependência é ativada automaticamente para aplicações [ASP.NET](./asp-net.md) e [ASP.NET Core,](./asp-net-core.md) quando configuradas de acordo com os documentos oficiais ligados. `DependencyTrackingTelemetryModule` é enviado como [este](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet, e é trazido automaticamente quando se utiliza qualquer uma das embalagens NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` atualmente rastreia automaticamente as seguintes dependências:

|Dependências |Detalhes|
|---------------|-------|
|Http/Https | Chamadas locais ou remotas http/https |
|Chamadas do WCF| Só é rastreado automaticamente se forem utilizadas ligações baseadas em Http.|
|SQL | Chamadas feitas `SqlClient` com. Consulte [isto](#advanced-sql-tracking-to-get-full-sql-query) para capturar consulta SQL.  |
|[Armazenamento Azure (Blob, Mesa, Fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o Cliente de Armazenamento Azure. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e superior. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e superior. |
|Azure Cosmos DB | Só é rastreado automaticamente se http/HTTPS for utilizado. O modo TCP não será capturado pela Application Insights. |

Se estiver a perder uma dependência, ou a utilizar um SDK diferente, certifique-se de que está na lista de [dependências recolhidas automaticamente.](./auto-collect-dependencies.md) Se a dependência não for recolhida automaticamente, pode rastreá-la manualmente com uma [chamada de dependência da faixa](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar rastreio de dependência automática em Apps de Consola

Para rastrear automaticamente as dependências das aplicações de consola .NET, instale o pacote NuGet `Microsoft.ApplicationInsights.DependencyCollector` e inicialize `DependencyTrackingTelemetryModule` da seguinte forma:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Para aplicações de consola .NET Core, a TelemetriaConfiguration.Ative é obsoleta. Consulte as orientações na [documentação](./worker-service.md) do serviço de trabalho e na [documentação de monitorização do núcleo ASP.NET](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>Como funciona a monitorização automática da dependência?

As dependências são automaticamente recolhidas utilizando uma das seguintes técnicas:

* Utilização de instrumentação de código byte em torno de métodos selecionados. (InstrumentationEngine quer do StatusMonitor ou da Extensão da Aplicação Web Azure)
* Chamadas EventSource
* Retornos de DiagnosticSource (nos mais recentes .NET/.NET Core SDKs)

## <a name="manually-tracking-dependencies"></a>Rastreio manual das dependências

Seguem-se alguns exemplos de dependências, que não são recolhidas automaticamente, e, portanto, requerem rastreio manual.

* O Azure Cosmos DB só é rastreado automaticamente se [o HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) for utilizado. O modo TCP não será capturado pela Application Insights.
* Redis

Para essas dependências não recolhidas automaticamente pela SDK, pode rastreá-las manualmente utilizando a [TrackDependency API](api-custom-events-metrics.md#trackdependency) que é utilizada pelos módulos de recolha de automóveis padrão.

Por exemplo, se construír o seu código com uma montagem que não escreveu a si mesmo, pode cronometrar todas as chamadas para ele, para descobrir que contribuição faz para os seus tempos de resposta. Para ter estes dados apresentados nos gráficos de dependência em Insights de Aplicação, envie-os utilizando `TrackDependency` .

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

Alternativamente, `TelemetryClient` fornece métodos de extensão `StartOperation` e que podem ser `StopOperation` usados para rastrear manualmente dependências, como mostrado [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se pretender desligar o módulo de rastreio de dependência padrão, remova a referência a DependencyTrackingTelemetryModule em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicações ASP.NET. Para ASP.NET aplicações Core, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Rastreio de chamadas AJAX a partir de Páginas Web

Para páginas web, o Application Insights JavaScript SDK recolhe automaticamente chamadas AJAX como dependências.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rastreio avançado do SQL para obter consulta SQL completa

> [!NOTE]
> As Funções Azure requer configurações separadas para permitir a recolha de textos SQL, ver [monitorização de configuração para as Funções Azure](../../azure-functions/configure-monitoring.md) para saber mais.

Para chamadas SQL, o nome do servidor e da base de dados é sempre recolhido e armazenado como nome do recolhido `DependencyTelemetry` . Há um campo adicional chamado 'dados', que pode conter todo o texto de consulta SQL.

Para ASP.NET aplicações Core, é agora obrigado a optar pela coleção de texto SQL usando
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

Para ASP.NET aplicações, o texto de consulta SQL completo é recolhido com a ajuda de instrumentação de código byte, que requer a utilização do motor de instrumentação ou utilizando o pacote [NuGet Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) em vez da biblioteca System.Data.SqlClient. As etapas específicas da plataforma para permitir a recolha completa de consultas SQL são descritas abaixo:

| Plataforma | Passo(s) Necessário para obter consulta SQL completa |
| --- | --- |
| Aplicação Web do Azure |No painel de controlo de aplicações web, [abra a lâmina De Insights de Aplicação](../../azure-monitor/app/azure-web-apps.md) e ative comandos SQL em .NET |
| Servidor IIS (Azure VM, on-prem, e assim por diante.) | Utilize o pacote [NuGet Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) ou utilize o Módulo PowerShell do Monitor de Estado para [instalar o Motor de Instrumentação](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) e reiniciar o IIS. |
| Serviço Cloud do Azure | Adicione [tarefa de arranque para instalar o StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> A sua aplicação deve ser a bordo da ApplicationInsights SDK no momento de construção, instalando pacotes NuGet para [aplicações ASP.NET](./asp-net.md) ou [ASP.NET Core](./asp-net-core.md) |
| IIS Express | Utilize o pacote [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet.
| Azure Web Jobs | Utilize o pacote [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet.

Além dos passos específicos da plataforma acima, **deve também optar explicitamente para permitir a recolha de comandos SQL** modificando o ficheiro applicationInsights.config com o seguinte:

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

Nos casos acima referidos, a forma correta de validar o motor de instrumentação corretamente instalado é através da validação de que a versão SDK recolhida `DependencyTelemetry` é 'rddp'. 'rdddsd' ou 'rddf' indica que as dependências são recolhidas através de chamadas DiagnosticSource ou EventSource e, portanto, a consulta completa do SQL não será capturada.

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência

* [O Mapa de Aplicações](app-map.md) visualiza as dependências entre a sua app e os componentes vizinhos.
* [O Diagnóstico de Transações](transaction-diagnostics.md) mostra dados de servidor unificados e correlacionados.
* [O separador browser mostra](javascript.md) chamadas AJAX dos navegadores dos seus utilizadores.
* Clique em pedidos lentos ou falhados para verificar as suas chamadas de dependência.
* [A análise](#logs-analytics) pode ser usada para consultar dados de dependência.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnosticar pedidos lentos

Cada evento de pedido está associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto a sua app está a processar o pedido. Portanto, se alguns pedidos estão a correr mal, podes descobrir se é devido a respostas lentas de uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos a dependências

Abra o **separador Desempenho** e navegue no separador **Dependências** no topo ao lado das operações.

Clique num **Nome de Dependência** em geral. Depois de selecionar uma dependência, um gráfico da distribuição dessa dependência das durações aparecerá à direita.

![No separador de desempenho clique no separador Dependência no topo e depois um nome de Dependência na tabela](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique no botão **Amostras azuis** no canto inferior direito e, em seguida, numa amostra para ver os detalhes da transação de ponta a ponta.

![Clique numa amostra para ver os detalhes da transação de ponta a ponta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfilar o seu site ao vivo

Não sabe para onde vai o tempo? O [profiler Application Insights](../../azure-monitor/app/profiler.md) rastreia chamadas HTTP para o seu site ao vivo e mostra-lhe as funções no seu código que demoraram mais tempo.

## <a name="failed-requests"></a>Pedidos com falhas

Os pedidos falhados também podem estar associados a chamadas falhadas para dependências.

Podemos ir ao separador **Falhas** à esquerda e clicar no separador **dependências** no topo.

![Clique na tabela de pedidos falhados](./media/asp-net-dependencies/4-fail.png)

Aqui poderá ver a contagem de dependência falhada. Para obter mais detalhes sobre uma ocorrência falhada tentando clicar num nome de dependência na tabela inferior. Pode clicar no botão **Dependências azuis** no canto inferior direito para obter os detalhes de transação de ponta a ponta.

## <a name="logs-analytics"></a>Registos (Analytics)

Você pode rastrear dependências na [língua de consulta kusto](/azure/kusto/query/). Eis alguns exemplos.

* Encontre chamadas de dependência falhadas:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Encontre chamadas AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Encontre chamadas de dependência associadas a pedidos:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Encontre chamadas AJAX associadas a visualizações de página:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como é que o relatório automático do colecionador de dependências falhou nas chamadas para as dependências?*

* Chamadas de dependência falhadas terão campo de "sucesso" definido para Falso. `DependencyTrackingTelemetryModule` não `ExceptionTelemetry` reporta . O modelo completo de dados para a dependência é descrito [aqui.](data-model-dependency-telemetry.md)

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Como calculo a latência de ingestão para a minha telemetria de dependência?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Como determino a hora da chamada de dependência?*

Na visão de consulta Log Analytics representa o momento em `timestamp` que a chamada TrackDependency() foi iniciada, o que ocorre imediatamente após a resposta da chamada de dependência ser recebida. Para calcular a hora em que a chamada de dependência começou, você pegaria `timestamp` e subtraia a `duration` chamada de dependência registada.

## <a name="open-source-sdk"></a>SDK de código aberto
Como todos os Application Insights SDK, o módulo de recolha de dependência também é de código aberto. Leia e contribua para o código, ou reporte problemas [no repo oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passos seguintes

* [Exceções](./asp-net-exceptions.md)
* [Dados da página & do utilizador](./javascript.md)
* [Disponibilidade](./monitor-web-app-availability.md)

