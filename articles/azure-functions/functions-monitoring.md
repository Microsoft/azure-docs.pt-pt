---
title: Monitorizar as Funções do Azure
description: Saiba como usar Aplicativo Azure insights com Azure Functions para monitorar a execução da função.
services: functions
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085617"
---
# <a name="monitor-azure-functions"></a>Monitorizar as Funções do Azure

O [Azure Functions](functions-overview.md) oferece integração interna com [informações de aplicativo Azure](../azure-monitor/app/app-insights-overview.md) para monitorar funções. Este artigo mostra como configurar Azure Functions para enviar arquivos de log gerados pelo sistema para Application Insights.

É recomendável usar Application Insights porque coleta dados de log, desempenho e erro. Ele detecta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender como suas funções são usadas. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Você pode até mesmo usar Application Insights durante o desenvolvimento do projeto de aplicativo de função local. Para obter mais informações, consulte [o que é Application insights?](../azure-monitor/app/app-insights-overview.md).

Como a instrumentação de Application Insights necessária é incorporada ao Azure Functions, tudo o que você precisa é de uma chave de instrumentação válida para conectar seu aplicativo de funções a um recurso de Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Preços e limites de Application Insights

Você pode experimentar Application Insights integração com aplicativos de funções gratuitamente. Há um limite diário para a quantidade de dados que pode ser processada gratuitamente. Você pode atingir esse limite durante o teste. O Azure fornece notificações por email e portal quando você está se aproximando do seu limite diário. Se você perder esses alertas e atingir o limite, novos logs não aparecerão em Application Insights consultas. Esteja ciente do limite para evitar o tempo de solução de problemas desnecessário. Para obter mais informações, consulte [gerenciar preços e volume de dados em Application insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Habilitar integração de Application Insights

Para que um aplicativo de funções envie dados para Application Insights, ele precisa saber a chave de instrumentação de um recurso de Application Insights. A chave deve estar em uma configuração de aplicativo chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Novo aplicativo de funções no portal

Quando você [cria seu aplicativo de funções no portal do Azure, a](functions-create-first-azure-function.md)integração Application insights é habilitada por padrão. O recurso Application Insights tem o mesmo nome que seu aplicativo de funções e é criado na mesma região ou na região mais próxima.

Para examinar a Application Insights recurso que está sendo criado, selecione-o para expandir a janela **Application insights** . Você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados.

![Habilitar Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

Quando você escolhe **criar**, um recurso de Application insights é criado com seu aplicativo de funções, que `APPINSIGHTS_INSTRUMENTATIONKEY` tem o conjunto nas configurações do aplicativo. Tudo está pronto para começar.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicionar a um aplicativo de funções existente 

Ao criar um aplicativo de funções usando o [CLI do Azure](functions-create-first-azure-function-azure-cli.md), o [Visual Studio](functions-create-your-first-function-visual-studio.md)ou o [Visual Studio Code](functions-create-first-function-vs-code.md), você deve criar o recurso de Application insights. Em seguida, você pode adicionar a chave de instrumentação a partir desse recurso como uma configuração de aplicativo em seu aplicativo de funções.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As versões anteriores das funções usavam o monitoramento interno, o que não é mais recomendado. Ao habilitar a integração de Application Insights para esse aplicativo de funções, você também deve [desabilitar o registro em log interno](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Exibir telemetria na guia monitor

Com a [integração do Application insights habilitada](#enable-application-insights-integration), você pode exibir dados de telemetria na guia **monitorar** .

1. Na página do aplicativo de funções, selecione uma função que tenha sido executada pelo menos uma vez depois que Application Insights foi configurada. Em seguida, selecione a guia **Monitor** .

   ![Selecionar guia monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **Atualizar** periodicamente, até que a lista de invocações de função seja exibida.

   Pode levar até cinco minutos para que a lista seja exibida enquanto o cliente de telemetria batche dados para transmissão para o servidor. (O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Esse serviço se conecta ao host do Functions quando você carrega a página, de modo que os logs são transmitidos diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os logs de uma invocação de função específica, selecione o link da coluna de **Data** para essa invocação.

   ![Link de detalhes de invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de log para essa invocação aparece em uma nova página.

   ![Detalhes da invocação](media/functions-monitoring/invocation-details-ai.png)

Você pode ver que ambas as páginas têm uma **execução no Application insights** link para a consulta Application insights Analytics que recupera os dados.

![Abrir no Application Insights](media/functions-monitoring/run-in-ai.png)

A consulta a seguir é exibida. Você pode ver que a lista de invocação está limitada aos últimos 30 dias. A lista mostra no máximo 20 linhas (`where timestamp > ago(30d) | take 20`). A lista de detalhes de invocação é para os últimos 30 dias sem nenhum limite.

![Lista de invocação de análise de Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Para obter mais informações, consulte [consultar dados](#query-telemetry-data) de telemetria mais adiante neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Exibir telemetria no Application Insights

Para abrir Application Insights de um aplicativo de funções no portal do Azure, vá para a página **visão geral** do aplicativo de funções. Em **recursos**configurados, selecione **Application insights**.

![Abrir Application Insights na página Visão geral do aplicativo de funções](media/functions-monitoring/ai-link.png)

Para obter informações sobre como usar Application Insights, consulte a [documentação do Application insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já estiver familiarizado com o Application Insights, poderá ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

![Guia de visão geral do Application Insights](media/functions-monitoring/metrics-explorer.png)

As áreas de Application Insights a seguir podem ser úteis ao avaliar o comportamento, o desempenho e os erros em suas funções:

| Tab | Descrição |
| ---- | ----------- |
| **[Sucedi](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções de servidor. O **nome da operação** é o nome da função. Falhas em dependências não são mostradas a menos que você implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analisar problemas de desempenho. |
| **Servidores** | Exibir a utilização de recursos e a taxa de transferência por servidor. Esses dados podem ser úteis para cenários de depuração em que as funções estão sobrecarregarndo seus recursos subjacentes. Os servidores são chamados de **instâncias de função de nuvem**. |
| **[Métricas](../azure-monitor/app/metrics-explorer.md)** | Crie gráficos e alertas baseados em métricas. As métricas incluem o número de invocações de função, tempo de execução e taxas de sucesso. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** (Fluxo de Métricas em Direto) | Exiba os dados de métricas conforme eles são criados em tempo real. |

## <a name="query-telemetry-data"></a>Consultar dados de telemetria

[Application insights Analytics](../azure-monitor/app/analytics.md) fornece acesso a todos os dados de telemetria na forma de tabelas em um banco de dados. O Analytics fornece uma linguagem de consulta para extrair, manipular e visualizar os dados.

![Selecionar análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Aqui está um exemplo de consulta que mostra a distribuição de solicitações por trabalhador nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas disponíveis são mostradas na guia **esquema** à esquerda. Você pode encontrar dados gerados pelas invocações de função nas seguintes tabelas:

| Tabela | Descrição |
| ----- | ----------- |
| **rastreamentos** | Logs criados pelo tempo de execução e por código de função. |
| **pedido** | Uma solicitação para cada invocação de função. |
| **exceção** | Todas as exceções geradas pelo tempo de execução. |
| **customMetrics** | A contagem de invocações bem-sucedidas e com falha, taxa de êxito e duração. |
| **customEvents** | Eventos rastreados pelo tempo de execução, por exemplo: Solicitações HTTP que disparam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores em que as funções estão sendo executadas. |

As outras tabelas são para testes de disponibilidade e telemetria de cliente e navegador. Você pode implementar a telemetria personalizada para adicionar dados a elas.

Em cada tabela, alguns dados específicos de funções estão em um `customDimensions` campo.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que `Error`têm o nível de log.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução `customDimensions.LogLevel` fornece `customDimensions.Category` os campos e. Você pode fornecer campos adicionais nos logs que você escreve em seu código de função. Consulte [registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar Application Insights sem nenhuma configuração personalizada. A configuração padrão pode resultar em grandes volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá atingir o limite de dados para Application Insights. Posteriormente neste artigo, você aprenderá a configurar e personalizar os dados que suas funções enviam para Application Insights. Para um aplicativo de funções, o registro em log é configurado no arquivo [host. JSON] .

### <a name="categories"></a>Categories

O agente de Azure Functions inclui uma *categoria* para cada log. A categoria indica qual parte do código de tempo de execução ou seu código de função gravou o log. 

O tempo de execução do Functions cria logs com uma categoria que começa com "host". Na versão 1. x, os `function started`logs `function executed`, e `function completed` têm a categoria `Host.Executor`. A partir da versão 2. x, esses logs têm a `Function.<YOUR_FUNCTION_NAME>`categoria.

Se você gravar logs em seu código de função, a categoria `Function` estará na versão 1. x do tempo de execução do functions. Na versão 2. x, a categoria é `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Níveis de log

O agente de Azure Functions também inclui um *nível de log* com cada log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreio       | 0 |
|depurar       | 1 |
|Information | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

O nível `None` de log é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de log em host. JSON

O arquivo [host. JSON] configura quanto log um aplicativo de funções envia para Application insights. Para cada categoria, você indica o nível de log mínimo a ser enviado. Há dois exemplos: o primeiro exemplo tem como alvo o [tempo de execução das funções versão 2. x](functions-versions.md#version-2x) (.NET Core) e o segundo exemplo é para o tempo de execução da versão 1. x.

### <a name="version-2x"></a>Versão 2. x

O tempo de execução v2. x usa a [hierarquia de filtro de log do .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

### <a name="version-1x"></a>Versão 1. x

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

* Para logs com categoria `Host.Results` ou `Function`, envie somente `Error` o nível e acima para Application insights. Os logs `Warning` de nível e abaixo são ignorados.
* Para logs com categoria `Host.Aggregator`, envie todos os logs para Application insights. O `Trace` nível de log é o mesmo que alguns agentes de chamada `Verbose`, mas usam `Trace` no arquivo [host. JSON] .
* Para todos os outros logs, envie `Information` apenas o nível e acima para Application insights.

O valor da categoria no [host. JSON] controla o registro em log para todas as categorias que começam com o mesmo valor. `Host`no [host. JSON] controla o registro `Host.General`em `Host.Executor`log `Host.Results`para,, e assim por diante.

Se o [host. JSON] incluir várias categorias que começam com a mesma cadeia de caracteres, as mais longas serão correspondidas primeiro. Suponha que você deseja tudo do tempo de `Host.Aggregator` execução, exceto `Error` para fazer logon no nível `Host.Aggregator` , mas `Information` deseja fazer logon no nível:

### <a name="version-2x"></a>Versão 2. x 

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

### <a name="version-1x"></a>Versão 1. x 

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

Para suprimir todos os logs de uma categoria, você pode usar `None`o nível de log. Nenhum log é gravado com essa categoria e não há nenhum nível de registro acima dele.

As seções a seguir descrevem as principais categorias de logs que o tempo de execução cria. 

### <a name="category-hostresults"></a>Host da categoria. resultados

Esses logs são mostrados como "solicitações" em Application Insights. Eles indicam êxito ou falha de uma função.

![Gráfico de solicitações](media/functions-monitoring/requests-chart.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, não verá nenhum desses dados.

### <a name="category-hostaggregator"></a>Categoria host. agregador

Esses logs fornecem contagens e médias de invocações de função em um período de tempo [configurável](#configure-the-aggregator) . O período padrão é de 30 segundos ou 1.000 de resultados, o que vier primeiro. 

Os logs estão disponíveis na tabela **customMetrics** em Application insights. Os exemplos são o número de execuções, a taxa de sucesso e a duração.

![consulta customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, não verá nenhum desses dados.

### <a name="other-categories"></a>Outras categorias

Todos os logs para categorias diferentes daqueles já listados estão disponíveis na tabela de **rastreamentos** em Application insights.

![consulta de rastreamentos](media/functions-monitoring/analytics-traces.png)

Todos os logs com categorias que começam `Host` com são gravados pelo tempo de execução do functions. Os logs "função iniciada" e "função concluída" têm `Host.Executor`categoria. Para execuções bem-sucedidas, esses logs `Information` são de nível. As exceções são registradas no `Error` nível. O tempo de execução `Warning` também cria logs de nível, por exemplo: mensagens de fila enviadas para a fila de suspeitas.

Os logs gravados pelo seu código de `Function` função têm categoria e podem ser qualquer nível de log.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o tempo de execução agrega dados sobre execuções de função durante um período de tempo. O período padrão é de 30 segundos ou 1.000 execuções, o que vier primeiro. Você pode definir essa configuração no arquivo [host. JSON] .  Segue-se um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar amostragem

O Application Insights tem um recurso de [amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir dados de telemetria demais em execuções concluídas em momentos de pico de carga. Quando a taxa de execuções de entrada excede um limite especificado, Application Insights começa a ignorar aleatoriamente algumas das execuções de entrada. A configuração padrão para o número máximo de execuções por segundo é 20 (cinco na versão 1. x). Você pode configurar a amostragem em [host. JSON].  Segue-se um exemplo:

### <a name="version-2x"></a>Versão 2. x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versão 1. x 

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
> A [amostragem](../azure-monitor/app/sampling.md) é habilitada por padrão. Se parecer que faltam dados, talvez seja necessário ajustar as configurações de amostragem para que se ajustem ao seu cenário de monitoramento específico.

## <a name="write-logs-in-c-functions"></a>Gravar logs em C# funções

Você pode gravar logs em seu código de função que aparecem como rastreamentos no Application Insights.

### <a name="ilogger"></a>ILogger

Use um parâmetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) em suas funções em vez de `TraceWriter` um parâmetro. Logs criados usando `TraceWriter` o go to Application insights, mas `ILogger` permite que você faça o [registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um `ILogger` objeto, você chama `Log<level>` [métodos de extensão em ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar logs. O código a seguir `Information` grava logs com a categoria "função".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Suponha que você tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer o registro em log estruturado. Application Insights armazena os pares nome-valor do parâmetro e a cadeia de caracteres da mensagem. O resultado é que os argumentos da mensagem se tornam campos que você pode consultar.

Se a chamada do método de agente for parecida com o exemplo anterior, você poderá `customDimensions.prop__rowKey`consultar o campo. O `prop__` prefixo é adicionado para garantir que não haja colisões entre os campos que o tempo de execução adiciona e os campos que o código de função adiciona.

Você também pode consultar a cadeia de caracteres da mensagem original referenciando `customDimensions.prop__{OriginalFormat}`o campo.  

Aqui está um exemplo de representação JSON `customDimensions` de dados:

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

### <a name="custom-metrics-logging"></a>Log de métricas personalizadas

Em C# funções de script, você pode usar `LogMetric` o método de `ILogger` extensão no para criar métricas personalizadas no Application insights. Aqui está uma chamada de método de exemplo:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Esse código é uma alternativa para chamar `TrackMetric` usando a API de Application insights para .net.

## <a name="write-logs-in-javascript-functions"></a>Gravar logs em funções JavaScript

Em funções do node. js, `context.log` use para gravar logs. O log estruturado não está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Log de métricas personalizadas

Quando você está executando na [versão 1. x](functions-versions.md#creating-1x-apps) do tempo de execução do functions, as funções do Node `context.log.metric` . js podem usar o método para criar métricas personalizadas no Application insights. Atualmente, não há suporte para esse método na versão 2. x. Aqui está uma chamada de método de exemplo:

```javascript
context.log.metric("TestMetric", 1234);
```

Esse código é uma alternativa para chamar `trackMetric` usando o SDK do node. js para Application insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Registrar telemetria personalizada C# em funções

Você pode usar o pacote NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar dados de telemetria personalizados para Application insights. O exemplo C# a seguir usa a API de telemetria [personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes .NET, mas o código Application Insights é o mesmo C# para script.

### <a name="version-2x"></a>Versão 2. x

O tempo de execução da versão 2. x usa recursos mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não é necessário definir manualmente os campos de operação `Id`, `ParentId`ou `Name` .

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
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
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
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

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
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versão 1. x

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

Não chame `TrackRequest` ou `StartOperation<RequestTelemetry>` porque você verá solicitações duplicadas para uma invocação de função.  O tempo de execução do Functions controla automaticamente as solicitações.

Não definir `telemetryClient.Context.Operation.Id`. Essa configuração global causa correlação incorreta quando muitas funções são executadas simultaneamente. Em vez disso, crie uma nova instância`DependencyTelemetry`de `EventTelemetry`telemetria (, `Context` ) e modifique sua propriedade. Em seguida, passe a instância de telemetria `Track` para o `TelemetryClient` método correspondente `TrackEvent()`em (`TrackDependency()`,). Esse método garante que a telemetria tenha os detalhes de correlação corretos para a invocação de função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registrar telemetria personalizada em funções JavaScript

Aqui está um trecho de código de exemplo que envia telemetria personalizada com o [Application insights SDK do node. js](https://github.com/microsoft/applicationinsights-node.js):

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

O `tagOverrides` parâmetro define o `operation_Id` para a ID de invocação da função. Essa configuração permite que você correlacione todas as telemetrias automaticamente geradas e personalizadas para uma determinada invocação de função.

## <a name="dependencies"></a>Dependências

As funções v2 coletam automaticamente as dependências para solicitações HTTP, ServiceBus e SQL.

Você pode escrever código personalizado para mostrar as dependências. Para obter exemplos, consulte o código de exemplo na [ C# seção telemetria personalizada](#log-custom-telemetry-in-c-functions). O código de exemplo resulta em um *mapa de aplicativo* em Application insights semelhante à imagem a seguir:

![Mapa da Aplicação](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Relatar problemas

Para relatar um problema com Application Insights integração em funções ou para fazer uma sugestão ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Registos de Transmissão em Fluxo

Ao desenvolver um aplicativo, muitas vezes você desejará fazer gravações nos logs em tempo quase real ao executar no Azure.

Há duas maneiras de exibir um fluxo de arquivos de log que está sendo gerado por suas execuções de função.

* **Streaming de log interno**: a plataforma do serviço de aplicativo permite que você exiba um fluxo dos arquivos de log do aplicativo. Isso é equivalente à saída vista quando você depura suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando você usa a guia **teste** no Portal. Todas as informações baseadas em log são exibidas. Para obter mais informações, consulte [como transmitir logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Esse método de streaming dá suporte a apenas uma única instância e não pode ser usado com um aplicativo em execução no Linux em um plano de consumo.

* **Live Metrics Stream**: quando seu aplicativo de funções está [conectado ao Application insights](#enable-application-insights-integration), você pode exibir dados de log e outras métricas quase em tempo real no portal do Azure usando [Live Metrics Stream](../azure-monitor/app/live-stream.md). Use esse método ao monitorar funções em execução em várias instâncias ou no Linux em um plano de consumo. Esse método usa [dados de amostra](#configure-sampling).

Os fluxos de log podem ser exibidos no portal e na maioria dos ambientes de desenvolvimento locais. 

### <a name="portal"></a>Portal

Você pode exibir os dois tipos de fluxos de log no Portal.

#### <a name="built-in-log-streaming"></a>Streaming de log interno

Para exibir os logs de streaming no portal, selecione a guia **recursos da plataforma** em seu aplicativo de funções. Em seguida, em **monitoramento**, escolha **streaming de log**.

![Habilitar logs de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isso conecta seu aplicativo ao serviço de streaming de log e os logs de aplicativo são exibidos na janela. Você pode alternar entre **logs do aplicativo** e **logs do servidor Web**.  

![Exibir logs de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Para exibir o Live Metrics Stream para seu aplicativo, selecione a guia **visão geral** do seu aplicativo de funções. Quando Application Insights habilita, você verá um link de **Application insights** em **recursos**configurados. Esse link leva você até a página de Application Insights para seu aplicativo.

Em Application Insights, selecione **Live Metrics Stream**. [As entradas de log de amostra](#configure-sampling) são exibidas em telemetria de **exemplo**.

![Exibir Live Metrics Stream no portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Ferramentas principais

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar os logs de streaming usando o [CLI do Azure](/cli/azure/install-azure-cli). Use os comandos a seguir para entrar, escolher sua assinatura e transmitir arquivos de log:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Você pode habilitar os logs de streaming usando [Azure PowerShell](/powershell/azure/overview). Para o PowerShell, use os seguintes comandos para adicionar sua conta do Azure, escolha sua assinatura e transmita os arquivos de log:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Desabilitar log interno

Ao habilitar Application Insights, desabilite o log interno que usa o armazenamento do Azure. O registro em log interno é útil para testes com cargas de trabalho leves, mas não se destina ao uso de produção de alta carga. Para o monitoramento de produção, recomendamos Application Insights. Se o registro em log interno for usado na produção, o registro de log poderá estar incompleto devido à limitação no armazenamento do Azure.

Para desabilitar o registro em log interno, exclua a configuração do `AzureWebJobsDashboard` aplicativo. Para obter informações sobre como excluir configurações de aplicativo no portal do Azure, consulte a seção **configurações de aplicativo** de [como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de excluir a configuração do aplicativo, certifique-se de que nenhuma função existente no mesmo aplicativo de funções use a configuração para gatilhos ou associações do armazenamento do Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [Log de ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host. JSON]: functions-host-json.md
