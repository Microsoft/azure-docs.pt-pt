---
title: Monitorizar as Funções do Azure
description: Aprenda a utilizar insights de aplicação Azure com funções Azure para monitorizar a execução da função.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: devx-track-csharp, fasttrack-edit
ms.openlocfilehash: 239d1da028a06d4272ed9b22b624413394aa142f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213000"
---
# <a name="monitor-azure-functions"></a>Monitorizar as Funções do Azure

[A Azure Functions](functions-overview.md) oferece integração incorporada com [a Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorizar funções. Este artigo mostra-lhe como configurar funções Azure para enviar ficheiros de registo gerados pelo sistema para Insights de Aplicação.

Recomendamos a utilização de Insights de Aplicação porque recolhe dados de registo, desempenho e erro. Deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a compreender como as suas funções são usadas. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Pode até usar o Application Insights durante o desenvolvimento de projetos de aplicações de funções locais. Para mais informações, consulte [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md)

Como a instrumentação necessária para os Insights de Aplicação é incorporada nas Funções Azure, tudo o que precisa é de uma chave de instrumentação válida para ligar a sua aplicação de função a um recurso Application Insights. A chave de instrumentação deve ser adicionada às definições da sua aplicação quando o recurso da aplicação de função for criado em Azure. Se a sua aplicação de função ainda não tiver esta chave, pode [defini-la manualmente](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Preços e limites de Insights de Aplicação

Pode experimentar gratuitamente a integração de Application Insights com funções Azure. Há um limite diário para a quantidade de dados que podem ser processados gratuitamente. Pode atingir este limite durante os testes. O Azure fornece notificações de portal e e-mail quando se aproxima do seu limite diário. Se perder esses alertas e atingir o limite, novos registos não aparecerão nas consultas de Insights de Aplicação. Esteja atento ao limite para evitar tempos desnecessários de resolução de problemas. Para obter mais informações, consulte [Gerir os preços e o volume de dados em Insights de Aplicação](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> A Application Insights tem uma funcionalidade [de amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir demasiados dados de telemetria em execuções concluídas em momentos de carga máxima. A amostragem é ativada por defeito. Se parecer faltar dados, poderá ter de ajustar as definições de amostragem para se adaptar ao seu cenário específico de monitorização. Para saber mais, consulte [a amostragem de Configure.](#configure-sampling)

A lista completa das funcionalidades de Insights de Aplicação disponível para a sua aplicação de funções é detalhada em [Funcionalidades suportadas por Insights para Funções Azure.](../azure-monitor/app/azure-functions-supported-features.md)

## <a name="view-telemetry-in-monitor-tab"></a>Ver telemetria no separador Monitor

Com [a integração de Application Insights ativada,](#enable-application-insights-integration)pode ver os dados da telemetria no **separador Monitor.**

1. Na página da aplicação de funções, selecione uma função que tenha sido executada pelo menos uma vez após a configuração do Application Insights. Em seguida, **selecione Monitor** a partir do painel esquerdo. Selecione **Refresh** periodicamente, até aparecer a lista de invocações de funções.

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Pode levar até cinco minutos para a lista aparecer enquanto o cliente da telemetria ementa dados para transmissão para o servidor. O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Este serviço liga-se ao anfitrião Funções quando carrega a página, para que os registos sejam transmitidos diretamente para a página.

1. Para ver os registos para uma invocação de função específica, selecione a ligação da coluna **Data (UTC)** para essa invocação. A saída de registo para essa invocação aparece numa nova página.

   ![Detalhes da invocação](media/functions-monitoring/invocation-details-ai.png)

1. Escolha Executar em Insights de **Aplicação** para visualizar a origem da consulta que recupera os dados de registo do Azure Monitor no Registo Azure. Se esta for a sua primeira utilização do Azure Log Analytics na sua subscrição, é-lhe pedido que o ative.

1. Depois de ativar o Log Analytics, é apresentada a seguinte consulta. Pode ver que os resultados da consulta estão limitados aos últimos 30 dias `where timestamp > ago(30d)` ( ). Além disso, os resultados não mostram mais do que 20 linhas ( `take 20` ). Em contraste, a lista de detalhes de invocação para a sua função é para os últimos 30 dias sem limite.

   ![Lista de invocação de Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Para mais informações, consulte os dados de [telemetria da Consulta](#query-telemetry-data) mais tarde neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Ver telemetria em Insights de Aplicação

Para abrir insights de aplicação a partir de uma aplicação de função no portal Azure, selecione **Insights de Aplicação** em **Definições** na página esquerda. Se esta for a sua primeira utilização de Insights de Aplicação com a sua subscrição, será solicitado para o ativar: selecione **Ligar insights de aplicação**e, em seguida, selecione **Aplicar** na página seguinte.

![Abrir informações sobre aplicações a partir da página geral da aplicação de funções](media/functions-monitoring/ai-link.png)

Para obter informações sobre como utilizar o Application Insights, consulte a [documentação do Application Insights](/azure/application-insights/). Esta secção mostra alguns exemplos de como visualizar dados em Insights de Aplicação. Se já está familiarizado com o Application Insights, pode ir diretamente às [secções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

![Separador de visão geral de insights de aplicação](media/functions-monitoring/metrics-explorer.png)

As seguintes áreas de Insights de Aplicação podem ser úteis na avaliação do comportamento, desempenho e erros nas suas funções:

| Investigar | Descrição |
| ---- | ----------- |
| **[Falhas](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções ao servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas a menos que implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analise os problemas de desempenho visualizando a utilização de recursos e a produção por **instâncias de função cloud**. Estes dados podem ser úteis para depurar cenários onde as funções estão a atrapalhar os seus recursos subjacentes. |
| **[Métricas](../azure-monitor/platform/metrics-charts.md)** | Crie gráficos e alertas baseados em métricas. As métricas incluem o número de invocações de funções, tempo de execução e taxas de sucesso. |
| **[Métricas Vivas    ](../azure-monitor/app/live-stream.md)** | Veja os dados das métricas como são criados em quase tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria de consulta

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) dá-lhe acesso a todos os dados de telemetria sob a forma de tabelas numa base de dados. A Analytics fornece uma linguagem de consulta para extrair, manipular e visualizar os dados. 

Escolha **Registos** para explorar ou consultar eventos registados.

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Aqui está um exemplo de consulta que mostra a distribuição de pedidos por trabalhador nos últimos 30 minutos.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

As tabelas disponíveis são mostradas no **separador Schema** à esquerda. Pode encontrar dados gerados por invocações de função nas seguintes tabelas:

| Tabela | Descrição |
| ----- | ----------- |
| **vestígios** | Registos criados pelo tempo de execução e pelo código de função. |
| **pedidos** | Um pedido para cada invocação de função. |
| **exceções** | Quaisquer exceções lançadas pelo tempo de execução. |
| **costumesMetricos** | A contagem de invocações bem sucedidas e falhadas, taxa de sucesso e duração. |
| **costumesEvents** | Eventos rastreados pelo tempo de execução, por exemplo: PEDIDOS HTTP que desencadeiam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores em que as funções estão a funcionar. |

As outras tabelas são para testes de disponibilidade, e telemetria de cliente e navegador. Pode implementar telemetria personalizada para adicionar dados a eles.

Dentro de cada tabela, alguns dos dados específicos das Funções estão num `customDimensions` campo.  Por exemplo, a seguinte consulta recupera todos os vestígios que têm nível de registo `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

O tempo de execução fornece os `customDimensions.LogLevel` `customDimensions.Category` campos e campos. Pode fornecer campos adicionais em registos que escreve no seu código de função. Consulte [a sessão madeireira estruturada](#structured-logging) mais tarde neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configure categorias e níveis de registo

Pode utilizar o Application Insights sem qualquer configuração personalizada. A configuração predefinida pode resultar em volumes elevados de dados. Se estiver a utilizar uma subscrição do Visual Studio Azure, poderá atingir a sua tampa de dados para Insights de Aplicação. Mais tarde neste artigo, aprende-se a configurar e personalizar os dados que as suas funções enviam para o Application Insights. Para uma aplicação de função, o registo está configurado na [host.jsno] ficheiro.

### <a name="categories"></a>Categorias

O madeireiro Azure Functions inclui uma *categoria* para cada registo. A categoria indica qual parte do código de execução ou o seu código de função escreveu o registo. O gráfico seguinte descreve as principais categorias de registos que o tempo de execução cria. 

| Categoria | Descrição |
| ----- | ----- | 
| Host.Results | Estes registos mostram como **pedidos** em Insights de Aplicação. Indicam sucesso ou falha de uma função. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |
| Host.A agregador | Estes registos fornecem contagens e médias de invocações de funções durante um período de tempo [configurável.](#configure-the-aggregator) O período de incumprimento é de 30 segundos ou 1.000 resultados, o que vier primeiro. Os registos estão disponíveis na tabela **personalizadaMetrics** in Application Insights. Exemplos são o número de corridas, taxa de sucesso e duração. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |

Todos os registos para categorias diferentes destas estão disponíveis na tabela **de vestígios** em Application Insights.

Todos os registos com categorias que começam `Host` por serem escritos pelo tempo de execução de Funções. Os **registos da Função iniciadas** e os registos **preenchidos da função** têm categoria `Host.Executor` . Para corridas bem sucedidas, estes registos estão `Information` nivelados. As exceções são registadas ao `Error` nível. O tempo de execução também cria `Warning` registos de nível, por exemplo: mensagens de fila enviadas para a fila de venenos.

O tempo de execução das Funções cria registos com uma categoria que começa com "Host". Na versão 1.x, os `function started` `function executed` registos e `function completed` registos têm a categoria `Host.Executor` . A partir da versão 2.x, estes registos têm a categoria `Function.<YOUR_FUNCTION_NAME>` .

Se escrever registos no seu código de função, a categoria é `Function.<YOUR_FUNCTION_NAME>.User` e pode ser qualquer nível de registo. Na versão 1.x do tempo de execução das Funções, a categoria é `Function` .

### <a name="log-levels"></a>Níveis de registo

O madeireiro Azure Functions também inclui um *nível de registo* com cada registo. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração, e o código inteiro indica importância relativa:

|Nível de Registo    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

O nível de `None` registo é explicado na secção seguinte. 

### <a name="log-configuration-in-hostjson"></a>Configuração de registo em host.jsem

O [host.jsno] ficheiro configura a quantidade de registo de uma aplicação de função que envia para o Application Insights. Para cada categoria, indica o nível mínimo de registo a enviar. Existem dois exemplos: o primeiro exemplo visa a [versão 2.x e mais tarde](functions-versions.md#version-2x) do tempo de execução de Funções (com .NET Core), e o segundo exemplo é para a versão 1.x runtime.

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

Versão v2.x e versões posteriores do tempo de execução das Funções utilize a hierarquia do [filtro de registo de registo .NET Core](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

Este exemplo estabelece as seguintes regras:

* Para registos com categoria `Host.Results` ou `Function` , envie apenas `Error` nível e acima para Insights de Aplicação. Os registos para `Warning` o nível e abaixo são ignorados.
* Para registos com `Host.Aggregator` categoria, envie todos os registos para Application Insights. O `Trace` nível de registo é o mesmo que alguns madeireiros `Verbose` chamam, mas usar `Trace` nohost.js[ no] ficheiro.
* Para todos os outros registos, envie apenas `Information` nível e acima para Application Insights.

O valor da categoria em [host.jsnos] controlos de registo de todas as categorias que começam com o mesmo valor. `Host` em [host.jssobre] os controlos de registo de , e assim por `Host.General` `Host.Executor` `Host.Results` diante.

Se [host.js] inclui várias categorias que começam com a mesma corda, as mais compridas são as primeiras. Suponha que queira tudo a partir do tempo de execução, exceto `Host.Aggregator` para registar-se ao `Error` nível, mas deseja `Host.Aggregator` registar-se ao `Information` nível:

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

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

Para suprimir todos os registos de uma categoria, pode utilizar o nível de registo `None` . Não há registos escritos com esta categoria e não há nenhum nível de registo acima dela.

## <a name="configure-the-aggregator"></a>Configure o agregador

Como indicado na secção anterior, o tempo de execução agrega dados sobre execuções de funções durante um período de tempo. O período de incumprimento é de 30 segundos ou 1.000 corridas, o que vier primeiro. Pode configurar esta definição na [host.jsno] ficheiro.  Eis um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Amostragem de configure

A Application Insights tem uma funcionalidade [de amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir demasiados dados de telemetria em execuções concluídas em momentos de carga máxima. Quando a taxa de execuções recebidas excede um limiar especificado, os Insights de Aplicação começam a ignorar aleatoriamente algumas das execuções recebidas. A definição predefinição para o número máximo de execuções por segundo é de 20 (cinco na versão 1.x). Pode configurar a amostragem em [host.js.](./functions-host-json.md#applicationinsights)  Eis um exemplo:

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

Na versão 2.x, pode excluir certos tipos de telemetria da amostragem. No exemplo acima, os dados do tipo `Request` estão excluídos da amostragem. Isto garante que *todas as* execuções de funções (pedidos) são registadas enquanto outros tipos de telemetria permanecem sujeitos a amostragem.

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

## <a name="write-logs-in-c-functions"></a>Escrever registos em funções C#

Pode escrever registos no seu código de função que aparecem como vestígios em Insights de Aplicação.

### <a name="ilogger"></a>ILogger

Utilize um parâmetro [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) nas suas funções em vez de um `TraceWriter` parâmetro. Os registos criados através da utilização `TraceWriter` vão para Application Insights, mas permitem fazer `ILogger` [registos estruturados](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um `ILogger` objeto, você chama `Log<level>` [métodos de extensão no ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar registos. O código seguinte escreve `Information` registos com a categoria "Função.<YOUR_FUNCTION_NAME>. Utilizador."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Exploração madeireira estruturada

A ordem dos espaços reservados, não os seus nomes, determina quais os parâmetros utilizados na mensagem de registo. Suponha que tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se mantiver o mesmo fio de mensagem e inverter a ordem dos parâmetros, o texto de mensagem resultante terá os valores nos lugares errados.

Os espaços reservados são manuseados desta forma para que possa fazer a exploração madeireira estruturada. Application Insights armazena os pares de valor-nome do parâmetro e a cadeia de mensagem. O resultado é que os argumentos da mensagem tornam-se campos que pode consultar.

Se o seu método de madeirão se parecer com o exemplo anterior, pode consultar o campo `customDimensions.prop__rowKey` . O `prop__` prefixo é adicionado para garantir que não existem colisões entre os campos que o tempo de execução adiciona e os campos que o seu código de função adiciona.

Também pode consultar a cadeia de mensagem original fazendo referência ao campo `customDimensions.prop__{OriginalFormat}` .  

Aqui está uma amostra json representação de `customDimensions` dados:

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

Nas funções de script C#, pode utilizar o `LogMetric` método de extensão `ILogger` para criar métricas personalizadas em Insights de Aplicação. Aqui está uma chamada de método de amostra:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Este código é uma alternativa à chamada `TrackMetric` utilizando a API de Insights de Aplicação para .NET.

## <a name="write-logs-in-javascript-functions"></a>Escrever registos em funções JavaScript

Nas funções Node.js, use `context.log` para escrever registos. A exploração madeireira estruturada não está ativada.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

Quando estiver a executar a [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de execução das Funções, Node.js funções podem utilizar o `context.log.metric` método para criar métricas personalizadas em Insights de Aplicação. Este método não é suportado atualmente na versão 2.x e posterior. Aqui está uma chamada de método de amostra:

```javascript
context.log.metric("TestMetric", 1234);
```

Este código é uma alternativa à chamada `trackMetric` utilizando o Node.js SDK para Insights de Aplicação.

## <a name="log-custom-telemetry-in-c-functions"></a>Registar telemetria personalizada em funções C#

Existe uma versão específica de Funções do SDK Application Insights que pode utilizar para enviar dados de telemetria personalizados das suas funções para Insights de Aplicação: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Utilize o seguinte comando a partir da solicitação de comando para instalar este pacote:

# <a name="command"></a>[Comando](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Neste comando, `<VERSION>` substitua por uma versão deste pacote que suporte a sua versão instalada do [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Os exemplos C# a seguir utilizam a [telemetria personalizada API](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classe .NET, mas o código Application Insights é o mesmo para o script C#.

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

As versões 2.x e posteriores do tempo de execução utilizam funcionalidades mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não há necessidade de definir manualmente a `Id` `ParentId` operação, ou `Name` campos.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

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

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) é a API atualmente recomendada para a criação de uma métrica.

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

Não ligue `TrackRequest` ou `StartOperation<RequestTelemetry>` porque verá pedidos duplicados para uma invocação de função.  O tempo de execução das Funções rastreia automaticamente os pedidos.

Não se `telemetryClient.Context.Operation.Id` ateem. Esta definição global causa uma correlação incorreta quando muitas funções estão a funcionar simultaneamente. Em vez disso, crie uma nova instância de telemetria `DependencyTelemetry` (, `EventTelemetry` ) e modifique a sua `Context` propriedade. Em seguida, passe na instância da telemetria para o método correspondente `Track` em ( , , `TelemetryClient` `TrackDependency()` `TrackEvent()` `TrackMetric()` . Este método garante que a telemetria tem os detalhes corretos da correlação para a invocação da função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registar telemetria personalizada em funções JavaScript

Aqui estão os snippets de código de amostra que enviam telemetria personalizada com o [Application Insights Node.js SDK:](https://github.com/microsoft/applicationinsights-node.js)

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>Versão 1.x

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

O `tagOverrides` parâmetro define o `operation_Id` ID de invocação da função. Esta definição permite-lhe correlacionar toda a telemetria gerada e personalizada automaticamente para uma determinada invocação de função.

## <a name="dependencies"></a>Dependências

As funções v2 recolhem automaticamente dependências para pedidos HTTP, ServiceBus, EventHub e SQL.

Pode escrever código personalizado para mostrar as dependências. Por exemplo, consulte o código de amostra na [secção de telemetria personalizada C](#log-custom-telemetry-in-c-functions)# . O código de amostra resulta num *mapa de aplicações* em Application Insights que se parece com a seguinte imagem:

![Mapa da aplicação](./media/functions-monitoring/app-map.png)

> [!NOTE]
> As dependências são escritas ao nível da Informação. Se filtrar em Aviso ou acima, não verá nenhum destes dados. Além disso, a recolha automática de dependências ocorre no âmbito não-utilizador. Por isso, certifique-se de que o nível está definido para pelo menos **informações** fora do âmbito do utilizador no seu host.js(isto é, fora do <YOUR_FUNCTION_NAME>. Chave do utilizador) se quiser que essas dependências sejam capturadas.

## <a name="enable-application-insights-integration"></a>Ativar a integração do Application Insights

Para que uma aplicação de função envie dados para o Application Insights, precisa de conhecer a chave de instrumentação de um recurso Application Insights. A chave deve estar numa definição de aplicação chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

Quando cria a sua aplicação de função [no portal Azure](functions-create-first-azure-function.md), a partir da linha de comando utilizando [ferramentas principais de funções Azure](functions-create-first-azure-function-azure-cli.md), ou utilizando [o Código do Estúdio Visual,](functions-create-first-function-vs-code.md)a integração de Insights de Aplicação é ativada por padrão. O recurso Application Insights tem o mesmo nome que a sua aplicação de função, e é criado na mesma região ou na região mais próxima.

### <a name="new-function-app-in-the-portal"></a>Nova aplicação de função no portal

Para rever o recurso Application Insights que está a ser criado, selecione-o para expandir a janela **'Insights de Aplicação'.** Pode alterar o nome de **novo recurso** ou escolher uma **localização** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados.

![Ativar insights de aplicação ao criar uma aplicação de função](media/functions-monitoring/enable-ai-new-function-app.png)

Quando escolhe **Criar**, é criado um recurso Application Insights com a sua aplicação de função, que tem o `APPINSIGHTS_INSTRUMENTATIONKEY` conjunto nas definições de aplicação. Tudo está pronto para ir.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicione a uma aplicação de função existente 

Quando criar uma aplicação de função utilizando [o Visual Studio,](functions-create-your-first-function-visual-studio.md)tem de criar o recurso Application Insights. Em seguida, pode adicionar a chave de instrumentação desse recurso como uma definição de [aplicação](functions-how-to-use-azure-function-app-settings.md#settings) na sua aplicação de função.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As primeiras versões de Funções utilizadas na monitorização incorporada, que já não é recomendada. Ao ativar a integração do Application Insights para uma aplicação de tal função, também deve [desativar a sessão embutimento.](#disable-built-in-logging)  

## <a name="report-issues"></a>Comunicar problemas

Para reportar um problema com a integração de Application Insights em Funções, ou para fazer uma sugestão ou pedido, [criar um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Registos de streaming

Ao desenvolver uma aplicação, muitas vezes quer ver o que está a ser escrito nos registos em tempo real quando corre em Azure.

Existem duas formas de ver um fluxo de ficheiros de registo a ser gerado pelas execuções de funções.

* **Streaming de registo incorporado**: a plataforma Serviço de Aplicações permite-lhe visualizar um fluxo de ficheiros de registo de aplicações. Isto é equivalente à saída observada quando desativa as suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando utiliza o separador **Teste** no portal. Todas as informações baseadas em registos são apresentadas. Para obter mais informações, consulte [os registos de stream](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming suporta apenas uma única instância, e não pode ser usado com uma aplicação em execução no Linux num plano de Consumo.

* **Live Metrics Stream**: quando a sua aplicação de função está [ligada ao Application Insights](#enable-application-insights-integration), pode visualizar dados de registo e outras métricas em quase tempo real no portal Azure utilizando live [metrics stream](../azure-monitor/app/live-stream.md). Utilize este método ao monitorizar as funções em execução em múltiplas instâncias ou em Linux num plano de consumo. Este método utiliza [dados amostrados.](#configure-sampling)

Os fluxos de log podem ser vistos tanto no portal como na maioria dos ambientes de desenvolvimento local. 

### <a name="portal"></a>Portal

Pode ver ambos os tipos de correntes de registo no portal.

#### <a name="built-in-log-streaming"></a>Transmissão em fluxo de registos incorporada

Para visualizar registos de streaming no portal, selecione o separador **funcionalidades da Plataforma** na sua aplicação de função. Em seguida, em **Monitoring,** escolha **o streaming de Registo**.

![Ativar registos de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isto liga a sua aplicação ao serviço de streaming de registos e os registos de aplicações são apresentados na janela. Pode alternar entre **registos de aplicações** e **registos de servidores Web**.  

![Ver registos de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Para ver o Live Metrics Stream para a sua aplicação, selecione o **separador Visão Geral** da sua aplicação de função. Quando tiver o Application Insights ativa, vê um link **'Insights de Aplicação'** nas **funcionalidades configuradas.** Este link leva-o à página 'Insights' de aplicação para a sua aplicação.

Em Application Insights, selecione **Live Metrics Stream**. [As entradas de registo amostradas](#configure-sampling) são apresentadas em **Telemetria de Amostra**.

![Ver Live Metrics Stream no portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Ferramentas Centrais

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>CLI do Azure

Pode ativar os registos de streaming utilizando o [Azure CLI](/cli/azure/install-azure-cli). Utilize os seguintes comandos para iniciar sessão, escolher a sua subscrição e transmitir ficheiros de registo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Pode ativar registos de streaming utilizando [a Azure PowerShell](/powershell/azure/). Para powerShell, utilize o comando [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) para permitir o início de sessão na aplicação de função, como mostrado no seguinte corte: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Para mais informações, consulte o exemplo completo do [código.](../app-service/scripts/powershell-monitor.md#sample-script) 

## <a name="scale-controller-logs-preview"></a>Registos de controlador de escala (pré-visualização)

Esta funcionalidade está em pré-visualização. 

O [controlador de escala Azure Functions](./functions-scale.md#runtime-scaling) monitoriza as instâncias do anfitrião Azure Functions em que a sua aplicação é executada. Este controlador toma decisões sobre quando adicionar ou remover casos com base no desempenho atual. Pode ter o controlador de escala a emitir registos para o Application Insights ou para o armazenamento blob para entender melhor as decisões que o controlador de escala está a tomar para a sua aplicação de função.

Para ativar esta funcionalidade, adicione uma nova definição de aplicação chamada `SCALE_CONTROLLER_LOGGING_ENABLED` . O valor desta definição deve ser do `<DESTINATION>:<VERBOSITY>` formato, com base no seguinte:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Por exemplo, o seguinte comando Azure CLI liga a registar verbose do controlador de escala para Insights de Aplicação:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Neste exemplo, substitua `<FUNCTION_APP_NAME>` e pelo nome da sua app de `<RESOURCE_GROUP_NAME>` função e pelo nome do grupo de recursos, respectivamente. 

O seguinte comando Azure CLI desativa o registo, definindo a verbosidade `None` para:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Também pode desativar a sessão, removendo a `SCALE_CONTROLLER_LOGGING_ENABLED` definição utilizando o seguinte comando Azure CLI:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Desativar o registo integrado

Quando ativar o Application Insights, desative a exploração madeireira incorporada que utiliza o Azure Storage. O registo embutida é útil para testes com cargas de trabalho leves, mas não se destina a uma utilização de produção de alta carga. Para monitorização da produção, recomendamos a Application Insights. Se a exploração madeireira incorporada for utilizada na produção, o registo de registos pode estar incompleto devido ao estrangulamento no Azure Storage.

Para desativar a sessão de registos incorporados, elimine a definição da `AzureWebJobsDashboard` aplicação. Para obter informações sobre como eliminar as definições de aplicações no portal Azure, consulte a secção de **definições** de Aplicação de [Como gerir uma aplicação de função](functions-how-to-use-azure-function-app-settings.md#settings). Antes de eliminar a definição da aplicação, certifique-se de que nenhuma função existente na mesma aplicação de função utiliza a definição para gatilhos ou encadernações de armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [Registo do núcleo de ASP.NET](/aspnet/core/fundamentals/logging/)

[host.js]: functions-host-json.md
