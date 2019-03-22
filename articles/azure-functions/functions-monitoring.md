---
title: Monitorizar as Funções do Azure
description: Saiba como utilizar o Azure Application Insights com as funções do Azure para monitorizar a execução de função.
services: functions
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: e9e47eff3df941b0c1437083dc7440fab4091418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317073"
---
# <a name="monitor-azure-functions"></a>Monitorizar as Funções do Azure

[As funções do Azure](functions-overview.md) oferece integração incorporada com [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorizar as funções. Este artigo mostra-lhe como configurar as funções do Azure para enviar os ficheiros de registo gerados pelo sistema para o Application Insights.

![Explorador de métricas do Application Insights](media/functions-monitoring/metrics-explorer.png)

As funções do Azure também tem [monitorização que incorporada não utiliza o Application Insights](#monitoring-without-application-insights). Recomendamos que o Application Insights porque oferece mais dados e melhores maneiras de analisar os dados.

## <a name="application-insights-pricing-and-limits"></a>Preços do Application Insights e limites

Pode experimentar a integração com aplicações de função do Application Insights gratuitamente. Existe um limite diário para a quantidade de dados pode ser processado gratuitamente. Que pode usar este limite durante o teste. O Azure disponibiliza o portal e notificações de e-mail quando estiver a atingir o seu limite diário. Se perder os alertas e atingir o limite, os novos registos não será apresentado em consultas do Application Insights. Tenha em atenção o limite para evitar o tempo de resolução de problemas desnecessário. Para obter mais informações, consulte [gerir preços e volumes de dados no Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Ativar a integração do Application Insights

Para uma aplicação de função enviar dados para o Application Insights, ele precisa saber a chave de instrumentação de um recurso do Application Insights. A chave tem de ser numa aplicação definição denominada **APPINSIGHTS_INSTRUMENTATIONKEY**.

Pode configurar esta ligação no [portal do Azure](https://portal.azure.com):

* [Ligar automaticamente uma nova aplicação de função](#new-function-app)
* [Ligar-se manualmente um recurso do Application Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nova aplicação de funções
<!-- Add a transitional sentence to introduce the procedure. -->

1. Vá para a aplicação de função **criar** página.

1. Definir o **Application Insights** mudar **no**.

1. Selecione um **localização do Application Insights**. Selecione a região mais próxima da região da sua aplicação de função e, num [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados.

   ![Ativar o Application Insights ao criar uma aplicação de funções](media/functions-monitoring/enable-ai-new-function-app.png)

1. Introduza as outras informações necessárias e selecione **criar**.

A próxima etapa é [desativar o registo incorporado](#disable-built-in-logging).


<a id="manually-connect-an-app-insights-resource"></a>
### <a name="application-insights-resource"></a>Recurso do Application Insights 
<!-- Add a transitional sentence to introduce the procedure. -->

1. Crie o recurso do Application Insights. Definir o tipo de aplicação **gerais**.

   ![Criar um recurso do Application Insights do tipo geral](media/functions-monitoring/ai-general.png)

1. Copie a chave de instrumentação a partir da **Essentials** página de recurso do Application Insights. Aponte para o fim do valor da chave apresentado para obter um **clique para copiar** botão.

   ![Copie a chave de instrumentação do Application Insights](media/functions-monitoring/copy-ai-key.png)

1. Em que a aplicação de funções **as configurações do aplicativo** página, [adicionar uma definição da aplicação](functions-how-to-use-azure-function-app-settings.md#settings) selecionando **Adicionar nova definição**. Nomeie a nova definição **APPINSIGHTS_INSTRUMENTATIONKEY** e cole a chave de instrumentação copiado.

   ![Adicionar chave de instrumentação para as definições da aplicação](media/functions-monitoring/add-ai-key.png)

1. Selecione **Guardar**.

<!-- Before the next H2 heading, add transitional sentences to summarize why the procedures were necessary. -->

## <a name="disable-built-in-logging"></a>Desativar o registo incorporado

Quando ativar o Application Insights, desativar a [logs interno que utiliza o armazenamento do Azure](#logging-to-storage). O logs interno é útil para testar com cargas de trabalho leves, mas não foi concebido para utilização em produção de alta carga. Para a monitorização de produção, recomendamos que o Application Insights. Se logs interno é utilizado em produção, o registo de registo poderá estar incompleto devido à limitação no armazenamento do Azure.

Para desativar o registo incorporado, elimine o `AzureWebJobsDashboard` definição de aplicação. Para obter informações sobre como eliminar as definições da aplicação no portal do Azure, consulte a **as configurações do aplicativo** secção [como gerir uma aplicação de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de eliminar a definição de aplicação, certifique-se que não existem funções existentes na mesma function app utilizam a definição para acionadores do armazenamento do Azure ou ligações.

## <a name="view-telemetry-in-monitor-tab"></a>Ver a telemetria no separador Monitor

Depois de configurar a integração do Application Insights conforme mostrado nas seções anteriores, pode ver os dados de telemetria no **Monitor** separador.

1. Na página da aplicação de função, selecione uma função que foi executada pelo menos uma vez após a configuração do Application Insights. Em seguida, selecione o **Monitor** separador.

   ![Selecione o separador Monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **atualizar** periodicamente, até que é apresentada a lista de invocações de função.

   Pode demorar até cinco minutos para a lista apareçam quando o cliente de telemetria dos dados para a transmissão para o servidor de lotes. (O atraso não se aplica para o [Stream de métricas em direto](../azure-monitor/app/live-stream.md). Esse serviço liga-se para o anfitrião de funções quando carrega a página, para que os registos são transmitidas em fluxo diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os registos para uma invocação de função específica, selecione o **data** ligação de coluna para essa invocação.

   ![Ligação de detalhes de invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de registo para essa invocação será exibida numa nova página.

   ![Detalhes de invocação](media/functions-monitoring/invocation-details-ai.png)

Ambas as páginas (lista de invocação e detalhes de invocação) ligar-se para a consulta do Application Insights Analytics que obtém os dados:

![Abrir no Application Insights](media/functions-monitoring/run-in-ai.png)

![Lista de invocação do Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Estas consultas, pode ver que a lista de invocação é limitada para os últimos 30 dias. A lista mostra mais do que 20 linhas (`where timestamp > ago(30d) | take 20`). A lista de detalhes de invocação é para os últimos 30 dias sem limite.

Para obter mais informações, consulte [consultar dados de telemetria](#query-telemetry-data) mais adiante neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Ver a telemetria no Application Insights

Para abrir o Application Insights a partir de uma aplicação de função no portal do Azure, vá para a aplicação de funções **descrição geral** página. Sob **configurado funcionalidades**, selecione **Application Insights**.

![Abrir o Application Insights a partir da página de descrição geral da aplicação de função](media/functions-monitoring/ai-link.png)

Para obter informações sobre como utilizar o Application Insights, consulte a [documentação do Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta secção mostra alguns exemplos de como ver dados no Application Insights. Se já estiver familiarizado com o Application Insights, pode aceder diretamente ao [secções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

Na [Explorador de métricas](../azure-monitor/app/metrics-explorer.md), pode criar gráficos e alertas que são baseadas em métricas. Métricas incluem o número de chamadas de função, tempo de execução e taxas de êxito.

![Explorador de Métricas](media/functions-monitoring/metrics-explorer.png)

Sobre o [falhas](../azure-monitor/app/asp-net-exceptions.md) separador, pode criar gráficos e alertas com base nas falhas de função e o servidor de exceções. O **nome da operação** é o nome de função. Falhas de dependências não são apresentadas, a menos que implemente [telemetria personalizada](#custom-telemetry-in-c-functions) para dependências.

![Falhas](media/functions-monitoring/failures.png)

Sobre o [desempenho](../azure-monitor/app/performance-counters.md) separador, pode analisar problemas de desempenho.

![Desempenho](media/functions-monitoring/performance.png)

O **servidores** separador mostra a utilização de recursos e o débito por servidor. Estes dados podem ser útil na depuração de cenários em que as funções são sobrecarregar os recursos subjacentes. Servidores são denominados **instâncias de função da Cloud**.

![Servidores](media/functions-monitoring/servers.png)

O [Stream de métricas em direto](../azure-monitor/app/live-stream.md) separador mostra dados das métricas, já que é criado em tempo real.

![Transmissão em direto](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Consultar dados de telemetria

[O Application Insights Analytics](../azure-monitor/app/analytics.md) permite-lhe aceder a todos os dados de telemetria na forma de tabelas numa base de dados. Analytics fornece uma linguagem de consulta para extrair, manipulação e visualizar os dados.

![Selecione a análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Eis um exemplo de consulta que mostra a distribuição de pedidos por função de trabalho nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas que estão disponíveis são mostradas na **esquema** separador no lado esquerdo. Pode encontrar os dados gerados por invocações de função nas tabelas seguintes:

* **rastreios**: Logs criados pelo tempo de execução e por código de função.
* **pedidos**: Uma solicitação para cada invocação de função.
* **Exceções**: Todas as exceções lançadas pelo tempo de execução.
* **customMetrics**: Contagem de invocações de êxito e a falhar, a taxa de êxito e a duração.
* **customEvents**: Eventos controlados pelo tempo de execução, por exemplo: Pedidos HTTP que acionam uma função.
* **performanceCounters**: Informações sobre o desempenho dos servidores que executem as funções.

As outras tabelas destinam-se a testes de disponibilidade e a telemetria de cliente e o navegador. Pode implementar a telemetria personalizada para adicionar dados a eles.

Alguns dos dados de funções específicas dentro de cada tabela, está num `customDimensions` campo.  Por exemplo, a consulta seguinte obtém todos os rastreios que tenham o nível de registo `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O runtime fornece os `customDimensions.LogLevel` e `customDimensions.Category` campos. Pode fornecer campos adicionais nos registos que escreve no código da função. Ver [estruturado registo](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar as categorias e níveis de registo

Pode utilizar o Application Insights sem qualquer configuração personalizada. A configuração predefinida pode resultar em grandes volumes de dados. Se estiver a utilizar uma subscrição do Azure do Visual Studio, que pode usar o limite de dados do Application Insights. Neste artigo, saiba como configurar e personalizar os dados que as suas funções enviam para o Application Insights.

### <a name="categories"></a>Categorias

O agente de log de funções do Azure inclui um *categoria* para cada registo. A categoria indica qual a parte do código de tempo de execução ou o código de função escreveu o registo. 

O runtime das funções cria registos com uma categoria que começam com "Host". A "função iniciada," "função" e os registos de "função concluída" tem a categoria "Host.Executor." 

Se escrever registos no código da função, a categoria é "Function".

### <a name="log-levels"></a>Níveis de registo

O agente de log de funções do Azure também inclui um *nível de registo* com cada registo. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração, e o código de número inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítica    | 5 |
|Nenhuma        | 6 |

Nível de registo `None` é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de registo no Host. JSON

O [Host. JSON](functions-host-json.md) ficheiro configura o Registro em log uma aplicação de funções envia para o Application Insights. Para cada categoria, indica o nível de registo mínimo para enviar. Existem dois exemplos: os destinos de exemplo primeiro o [funções versão 2.x do runtime](functions-versions.md#version-2x) (.NET Core) e o segundo exemplo é para o tempo de execução do versão 1.x.

### <a name="version-2x"></a>Versão 2.x

O tempo de execução v2.x utiliza a [hierarquia de filtro de registo do .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

* Para os registos com categoria `Host.Results` ou `Function`, enviar apenas `Error` nível e superior para o Application Insights. Nos registos `Warning` nível e abaixo são ignorados.
* Para os registos com categoria `Host.Aggregator`, enviar todos os registos para o Application Insights. O `Trace` nível de registo é o mesmo que o que chamam de alguns agentes `Verbose`, mas usar `Trace` no [Host. JSON](functions-host-json.md) ficheiro.
* Para todos os outros registos, envie apenas `Information` nível e superior para o Application Insights.

O valor de categoria na [Host. JSON](functions-host-json.md) controla o registo para todas as categorias que começam com o mesmo valor. `Host` na [Host. JSON](functions-host-json.md) controles para o registo `Host.General`, `Host.Executor`, `Host.Results`e assim por diante.

Se [Host. JSON](functions-host-json.md) inclui várias categorias que começam com a mesma cadeia de caracteres, mais aqueles são correspondidas pela primeira vez. Suponha que pretende que tudo, desde o tempo de execução, exceto `Host.Aggregator` para iniciar sessão na `Error` nível, mas pretender `Host.Aggregator` para iniciar sessão no `Information` nível:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para suprimir todos os registos para uma categoria, pode usar o nível de registo `None`. Não existem registos são escritos com essa categoria e não existe nenhum nível de registo acima dela.

As secções seguintes descrevem as principais categorias de registos que cria o tempo de execução. 

### <a name="category-hostresults"></a>Host.Results de categoria

Estes registos mostram como "pedidos" no Application Insights. Eles indicam o êxito ou falha de uma função.

![Gráfico de pedidos](media/functions-monitoring/requests-chart.png)

Todos estes registos são escritos em `Information` nível. Se filtrar em `Warning` ou superior, não verá qualquer um desses dados.

### <a name="category-hostaggregator"></a>Host.Aggregator de categoria

Estes registos fornecem contagens e médias de invocações de função através de um [configuráveis](#configure-the-aggregator) período de tempo. O período predefinido é 30 segundos ou 1.000 resultados, o que ocorrer primeiro. 

Os registos estão disponíveis no **customMetrics** tabela no Application Insights. Os exemplos são o número de execuções, a taxa de êxito e a duração.

![consulta de customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos estes registos são escritos em `Information` nível. Se filtrar em `Warning` ou superior, não verá qualquer um desses dados.

### <a name="other-categories"></a>Outras categorias

Todos os registos de categorias diferentes dos já encontram-se disponível na **rastreios** tabela no Application Insights.

![consulta de rastreios](media/functions-monitoring/analytics-traces.png)

Todos os registos com as categorias que começam com `Host` são escritos pelo tempo de execução de funções. Os registos de "Função concluída" e "Função started" têm a categoria `Host.Executor`. Execuções com êxito, estes registos são `Information` nível. Exceções são registadas no `Error` nível. O tempo de execução também cria `Warning` nível registos, por exemplo: mensagens enviadas para a fila não processáveis da fila.

Os registos escritos pelo seu código de função têm a categoria `Function` e pode ser qualquer nível de registo.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o tempo de execução agrega dados sobre execuções de função durante um período de tempo. O período predefinido é 30 segundos ou 1.000 é executado, o que ocorrer primeiro. Pode configurar esta definição no [Host. JSON](functions-host-json.md) ficheiro.  Segue-se um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um [amostragem](../azure-monitor/app/sampling.md) recurso que pode protegê-lo de produzir muitos dados de telemetria em momentos de pico de carga. Quando a taxa de telemetria de entrada exceder um limiar especificado, o Application Insights começa a aleatoriamente ignorar alguns dos itens recebidos. A configuração padrão para o número máximo de itens por segundo é cinco. Pode configurar amostragem em [Host. JSON](functions-host-json.md).  Segue-se um exemplo:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
      }
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Amostragem](../azure-monitor/app/sampling.md) está ativada por predefinição. Se parecem ser os dados em falta, poderá ter de ajustar as definições de amostragem para se ajustar ao seu cenário específico de monitorização.

## <a name="write-logs-in-c-functions"></a>Registos de escrita no c# das funções

Pode escrever registos no código da função que aparecem como rastreios no Application Insights.

### <a name="ilogger"></a>ILogger

Utilize um [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parâmetro nas suas funções em vez de um `TraceWriter` parâmetro. Logs criados utilizando `TraceWriter` vá para o Application Insights, mas `ILogger` permite-lhe fazer [estruturado registo](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com uma `ILogger` objeto, chama `Log<level>` [os métodos de extensão ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar registos. O seguinte código escritas `Information` registos com a categoria "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registo estruturado

A ordem dos marcadores de posição, não os respetivos nomes, determina quais parâmetros são usados na mensagem de registo. Suponha que tem o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se manter a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante teria os valores nos lugares errados.

Marcadores de posição são tratadas dessa forma, para que pode fazer o registo estruturado. O Application Insights armazena os pares de nome-valor de parâmetro e a cadeia de caracteres de mensagem. O resultado é que os argumentos de mensagem se tornar os campos que pode consultar no.

Se a chamada de método logger parecer como no exemplo anterior, pode consultar o campo `customDimensions.prop__rowKey`. O `prop__` prefixo é adicionado garantir que não há nenhum colisões entre campos o runtime adiciona e campos de seu código de função adiciona.

Também pode consultar na cadeia de caracteres de mensagem original ao referenciar o campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma representação JSON de exemplo de `customDimensions` dados:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

No c# funções de script, pode utilizar o `LogMetric` método de extensão no `ILogger` para criar métricas personalizadas no Application Insights. Eis uma chamada de método de exemplo:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Esse código é uma alternativa ao chamar `TrackMetric` , utilizando [a API do Application Insights para .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Escrever registos nas funções de JavaScript

Nas funções do node. js, utilize `context.log` para escrever os registos. Registo estruturado não está ativado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

Quando estiver a executar num [versão 1.x](functions-versions.md#creating-1x-apps) de runtime das funções, podem utilizar as funções do node. js a `context.log.metric` método para criar métricas personalizadas no Application Insights. Este método não é atualmente suportado na versão 2.x. Eis uma chamada de método de exemplo:

```javascript
context.log.metric("TestMetric", 1234);
```

Esse código é uma alternativa ao chamar `trackMetric` , utilizando [o SDK de node. js do Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="log-custom-telemetry-in-c-functions"></a>Iniciar telemetria personalizada C# funções

Pode utilizar o [applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pacote NuGet para enviar dados de telemetria personalizada para o Application Insights. O seguinte C# exemplo utiliza o [API de telemetria personalizado](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para script c#.

### <a name="version-2x"></a>Versão 2.x

A versão 2.x do runtime utiliza funcionalidades mais recentes no Application Insights automaticamente correlacionar a telemetria com a operação atual. Não é necessário definir manualmente a operação `Id`, `ParentId`, ou `Name` campos.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versão 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Não chame `TrackRequest` ou `StartOperation<RequestTelemetry>` porque poderá ver pedidos de duplicação para uma invocação de função.  O runtime das funções controla automaticamente pedidos.

Não defina `telemetryClient.Context.Operation.Id`. Esta definição global faz com que correlação incorreta quando muitas funções em execução simultânea. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modificar seu `Context` propriedade. Em seguida, passamos a instância de telemetria para o correspondente `Track` método no `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Esse método garante que a telemetria tenha os detalhes de correlação correto para a invocação de função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Telemetria personalizada do log nas funções de JavaScript

O [SDK de node. js do Application Insights](https://www.npmjs.com/package/applicationinsights) está atualmente na versão beta. Eis alguns exemplos de código que envia telemetria personalizada para o Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

O `tagOverrides` conjuntos de parâmetros a `operation_Id` para ID de invocação. de função Esta definição permite-lhe correlacionar a toda a telemetria personalizada e gerada automaticamente para uma invocação de função especificada.

## <a name="known-issues"></a>Problemas conhecidos
<!-- Add a transitional sentence to introduce the section. -->

### <a name="dependencies"></a>Dependências

Dependências de que a função tem a outros serviços não aparecem automaticamente. Pode escrever código personalizado para mostrar as dependências. Para obter exemplos, consulte o código de exemplo da [ C# secção de telemetria personalizada](#custom-telemetry-in-c-functions). O código de exemplo resulta numa *mapa da aplicação* no Application Insights que se parece com a imagem seguinte:

![Mapeamento de aplicações](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Comunicar problemas

Para comunicar um problema com a integração do Application Insights em funções ou para fazer uma sugestão ou a pedido, [criar um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitor-without-application-insights"></a>Monitor sem o Application Insights

Recomendamos que o Application Insights para funções de monitorização. Oferece mais dados e melhores maneiras de analisar os dados. Mas se preferir o sistema de logs interno que utiliza o armazenamento do Azure, pode continuar a utilizar este método.

### <a name="azure-storage-account-for-logging"></a>Conta de armazenamento do Azure para o registo

Logs interno utiliza a conta de armazenamento especificada pela cadeia de ligação no `AzureWebJobsDashboard` definição de aplicação. Numa página da aplicação de função, selecione uma função e, em seguida, selecione o **Monitor** separador e escolha mantê-los na **vista clássica**.

![Mudar para a vista clássica](media/functions-monitoring/switch-to-classic-view.png)

Obter uma lista de execuções de função. Selecione uma execução de função para rever a duração, dados de entrada, erros e os ficheiros de registo associados.

Se ativou o Application Insights, pode retornar ao uso de logs interno. Desativar o Application Insights manualmente e, em seguida, selecione o **Monitor** separador. Para desativar a integração do Application Insights, elimine o `APPINSIGHTS_INSTRUMENTATIONKEY` definição de aplicação.

Mesmo que o **Monitor** separador mostra dados do Application Insights, pode ver dados de registo no sistema de ficheiros se ainda não [desativada logs interno](#disable-built-in-logging). No recurso de armazenamento, aceda a **ficheiros**e selecione o serviço de ficheiros para a função. Em seguida, aceda a **LogFiles** > **aplicativo** > **funções** > **função**  >  **your_function** para ver o ficheiro de registo.

### <a name="real-time-monitoring"></a>Monitorização em tempo real

Pode transmitir ficheiros de registo para uma sessão de linha de comandos numa estação de trabalho local. Utilize o [Interface de linha de comandos (CLI) do Azure](/cli/azure/install-azure-cli) ou [do Azure PowerShell](/powershell/azure/overview).  

Para a CLI do Azure, utilize os seguintes comandos para iniciar sessão, escolha a sua subscrição e os ficheiros de registo do fluxo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Para o Azure PowerShell, utilize os seguintes comandos para adicionar a sua conta do Azure, escolha a sua subscrição e os ficheiros de registo do fluxo:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <function app name> -Tail
```

Para obter mais informações, consulte [como transmitir registos](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [O registo do ASP.NET Core](/aspnet/core/fundamentals/logging/)
