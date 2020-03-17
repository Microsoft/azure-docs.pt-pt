---
title: Monitorizar as Funções do Azure
description: Aprenda a utilizar insights de aplicação Azure com funções Azure para monitorizar a execução da função.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: dda62e3041d04d5becc9179fff1c56d0c587ba1e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276871"
---
# <a name="monitor-azure-functions"></a>Monitorizar as Funções do Azure

[A Azure Functions](functions-overview.md) oferece integração integrada com insights de [aplicação Azure](../azure-monitor/app/app-insights-overview.md) para monitorizar funções. Este artigo mostra-lhe como configurar as Funções Azure para enviar ficheiros de registo gerados pelo sistema para Insights de Aplicação.

Recomendamos a utilização de Insights de Aplicação porque recolhe dados de registo, desempenho e erro. Deteta automaticamente anomalias de desempenho e inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender como as suas funções são usadas. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Pode até utilizar os Insights de Aplicação durante o desenvolvimento do projeto de aplicação de funções locais. Para mais informações, consulte [o que é informação sobre aplicação?](../azure-monitor/app/app-insights-overview.md)

Como a instrumentação de Insights de Aplicação necessária é incorporada nas Funções Azure, tudo o que precisa é de uma chave de instrumentação válida para ligar a sua aplicação de função a um recurso Desinformação de Aplicações.

## <a name="application-insights-pricing-and-limits"></a>Preços e limites de insights de aplicação

Pode experimentar a integração de Insights de Aplicação com Apps de Função gratuitamente. Há um limite diário para quantos dados podem ser processados gratuitamente. Pode atingir este limite durante os testes. O Azure fornece notificações de portal e e-mail quando se aproxima do seu limite diário. Se perder esses alertas e atingir o limite, novos registos não aparecerão nas consultas de Application Insights. Esteja ciente do limite para evitar tempos desnecessários de resolução de problemas. Para mais informações, consulte [Gerir preços e volume de dados em Insights de Aplicação](../azure-monitor/app/pricing.md).

A lista completa das funcionalidades de Insights de Aplicação disponíveis para a sua aplicação de funções é detalhada em [Funcionalidades suportadas por Funcionalidades De Aplicação para Funções Azure](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="enable-application-insights-integration"></a>Ativar a integração de Insights de Aplicação

Para que uma aplicação de função envie dados para A aplicação Insights, precisa de saber a chave de instrumentação de um recurso de Insights de Aplicação. A chave deve estar numa definição de aplicação chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nova aplicação de funções no portal

Quando [cria a sua aplicação de funções no portal Azure, a](functions-create-first-azure-function.md)integração de Insights de Aplicação é ativada por padrão. O recurso Application Insights tem o mesmo nome que a sua app de funções, e é criado na mesma região ou na região mais próxima.

Para rever o recurso Application Insights criado, selecione-o para expandir a janela **Application Insights.** Pode alterar o **nome de novo recurso** ou escolher um **Local** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados.

![Ativar insights de aplicação ao criar uma aplicação de função](media/functions-monitoring/enable-ai-new-function-app.png)

Quando escolhe o **Create**, é criado um recurso De Insights de Aplicação com a sua aplicação de funções, que tem o conjunto de `APPINSIGHTS_INSTRUMENTATIONKEY` nas definições de aplicação. Está tudo pronto para ir.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicione a uma aplicação de função existente 

Quando criar uma aplicação de função utilizando o [Azure CLI,](functions-create-first-azure-function-azure-cli.md) [Visual Studio](functions-create-your-first-function-visual-studio.md), ou Visual [Studio Code,](functions-create-first-function-vs-code.md)tem de criar o recurso Application Insights. Em seguida, pode adicionar a chave de instrumentação a partir desse recurso como uma definição de aplicação na sua aplicação de função.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As primeiras versões de Funções utilizaram monitorização incorporada, que já não é recomendada. Ao permitir a integração de Insights de Aplicação para uma aplicação de função, também deve [desativar](#disable-built-in-logging)o registo incorporado.  

## <a name="view-telemetry-in-monitor-tab"></a>Ver telemetria no separador Monitor

Com a integração de Insights de [Aplicação ativada,](#enable-application-insights-integration)pode visualizar os dados de telemetria no separador **Monitor.**

1. Na página da aplicação de funções, selecione uma função que tenha sido executada pelo menos uma vez após a configuração do Application Insights. Em seguida, selecione o separador **Monitor.**

   ![Selecione o separador Monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **Refresh** periodicamente, até aparecer a lista de invocações de função.

   Pode levar até cinco minutos para a lista aparecer enquanto o cliente de telemetria emite dados para transmissão para o servidor. (O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Este serviço liga-se ao anfitrião das funções quando carrega a página, para que os registos sejam transmitidos diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os registos para uma determinada invocação de função, selecione o link da coluna **Data** para essa invocação.

   ![Ligação de detalhes de invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de exploração madeireira para essa invocação aparece numa nova página.

   ![Detalhes da invocação](media/functions-monitoring/invocation-details-ai.png)

Pode ver que ambas as páginas têm uma ligação **Run in Application Insights** à consulta de Análise de Insights de Aplicação que recupera os dados.

![Executar em Insights de Aplicação](media/functions-monitoring/run-in-ai.png)

A seguinte consulta é exibida. Pode ver que os resultados da consulta estão limitados aos últimos 30 dias (`where timestamp > ago(30d)`). Além disso, os resultados não mostram mais de 20 linhas (`take 20`). Em contraste, a lista de detalhes da invocação para a sua função é para os últimos 30 dias sem limite.

![Lista de invocações de Insights de Aplicação Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Para mais informações, consulte os dados da [telemetria da Consulta](#query-telemetry-data) mais tarde neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Ver telemetria em Insights de Aplicação

Para abrir os Insights de Aplicação a partir de uma aplicação de função no portal Azure, vá à página de **visão geral** da aplicação de funções. Em **funcionalidades configuradas,** selecione **Insights de Aplicação**.

![Visão de aplicação aberta da página de visão geral da aplicação de funções](media/functions-monitoring/ai-link.png)

Para obter informações sobre como utilizar os Insights da Aplicação, consulte a documentação de Insights de [Aplicação](https://docs.microsoft.com/azure/application-insights/). Esta secção mostra alguns exemplos de como visualizar dados em Insights de Aplicação. Se já está familiarizado com os Insights de Aplicação, pode ir diretamente às [secções sobre como configurar e personalizar os dados de telemetria.](#configure-categories-and-log-levels)

![Aplicativo Insights Overview tab](media/functions-monitoring/metrics-explorer.png)

As seguintes áreas de Insights de Aplicação podem ser úteis na avaliação do comportamento, desempenho e erros nas suas funções:

| Guia | Descrição |
| ---- | ----------- |
| **[Falhas](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções ao servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas a menos que implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analise os problemas de desempenho. |
| **Servidores** | Ver utilização de recursos e entrada por servidor. Estes dados podem ser úteis para depurar cenários onde as funções estão a afundar os seus recursos subjacentes. Os servidores são referidos como **exemplos**de papel cloud . |
| **[Métricas](../azure-monitor/app/metrics-explorer.md)** | Crie gráficos e alertas que se baseiem em métricas. As métricas incluem o número de invocações de funções, tempo de execução e taxas de sucesso. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** (Fluxo de Métricas em Direto) | Veja os dados das métricas como é criado em quase tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria de consulta

[Aplicação Insights Analytics](../azure-monitor/app/analytics.md) dá-lhe acesso a todos os dados de telemetria sob a forma de tabelas numa base de dados. A Analytics fornece uma linguagem de consulta para extrair, manipular e visualizar os dados.

![Selecione Analytics](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Aqui está um exemplo de consulta que mostra a distribuição de pedidos por trabalhador nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas disponíveis são mostradas no separador **Schema** à esquerda. Pode encontrar dados gerados por invocações de funções nas seguintes tabelas:

| Tabela | Descrição |
| ----- | ----------- |
| **vestígios** | Registos criados pelo tempo de funcionamento e pelo código de função. |
| **pedidos** | Um pedido para cada invocação de função. |
| **exceções** | Quaisquer exceções lançadas pelo tempo de execução. |
| **métricas personalizadas** | A contagem de invocações bem sucedidas e fracassadas, taxa de sucesso e duração. |
| **eventos personalizados** | Eventos rastreados pelo tempo de execução, por exemplo: PEDIDOS HTTP que desencadeiam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores em que as funções estão a funcionar. |

As outras tabelas são para testes de disponibilidade, e telemetria de cliente e navegador. Pode implementar telemetria personalizada para lhes adicionar dados.

Dentro de cada tabela, alguns dos dados específicos das Funções estão num campo `customDimensions`.  Por exemplo, a seguinte consulta recupera todos os vestígios que têm nível de registo `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução proporciona os campos de `customDimensions.LogLevel` e `customDimensions.Category`. Pode fornecer campos adicionais nos registos que escreve no seu código de função. Consulte [a exploração madeireira estruturada](#structured-logging) mais tarde neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configure categorias e níveis de registo

Pode utilizar os Insights de Aplicação sem qualquer configuração personalizada. A configuração predefinida pode resultar em elevados volumes de dados. Se estiver a utilizar uma subscrição do Visual Studio Azure, poderá atingir o seu limite de dados para Insights de Aplicação. Mais tarde neste artigo, aprende-se a configurar e personalizar os dados que as suas funções enviam para Insights de Aplicação. Para uma aplicação de função, o registo está configurado no ficheiro [host.json]

### <a name="categories"></a>Categorias

O logger Funções Azure inclui uma *categoria* para cada registo. A categoria indica qual a parte do código de tempo de execução ou o seu código de função escreveu o registo. 

O tempo de funcionamento das Funções cria registos com uma categoria que começa com "Anfitrião". Na versão 1.x, os registos `function started`, `function executed`e `function completed` têm a categoria `Host.Executor`. A partir da versão 2.x, estes registos têm a categoria `Function.<YOUR_FUNCTION_NAME>`.

Se escrever registos no seu código de funcionamento, a categoria é `Function` na versão 1.x do tempo de execução das Funções. Na versão 2.x, a categoria é `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Níveis de registo

O logger funções Azure também inclui um *nível de registo* com cada registo. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração, e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítica    | 5 |
|Nenhum        | 6 |

O nível de registo `None` é explicado na secção seguinte. 

### <a name="log-configuration-in-hostjson"></a>Configuração de log em host.json

O ficheiro [host.json] concalcula a quantidade de registo que uma aplicação de função envia para Application Insights. Para cada categoria, indica o nível mínimo de registo a enviar. Existem dois exemplos: o primeiro exemplo visa a [versão 2.x e mais tarde](functions-versions.md#version-2x) do tempo de execução das Funções (com .NET Core), e o segundo exemplo é para o tempo de execução da versão 1.x.

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

Versão v2.x e versões posteriores do tempo de funcionamento das funções utilizam a hierarquia do filtro de [registo .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

* Para registos com categoria `Host.Results` ou `Function`, envie apenas `Error` nível e acima para Informações de Aplicação. Os registos para `Warning` nível e abaixo são ignorados.
* Para registos com categoria `Host.Aggregator`, envie todos os registos para Informações de Aplicação. O nível de registo `Trace` é o mesmo que alguns madeireiros chamam `Verbose`, mas usam `Trace` no ficheiro [host.json]
* Para todos os outros registos, envie apenas `Information` nível e acima para Informações de Aplicação.

O valor da categoria em [host.json] controla o registo de exploração para todas as categorias que começam com o mesmo valor. `Host` no [host.json] controla a exploração madeireira para `Host.General`, `Host.Executor`, `Host.Results`, e assim por diante.

Se [host.json] inclui várias categorias que começam com a mesma corda, as mais compridas são correspondidas primeiro. Suponha que quer tudo desde o tempo de execução, exceto `Host.Aggregator` para iniciar sessão ao nível `Error`, mas quer `Host.Aggregator` registar ao nível `Information`:

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

Para suprimir todos os registos para uma categoria, pode utilizar o nível de registo `None`. Não há registos escritos com esta categoria e não há nenhum nível de registo acima.

As seguintes secções descrevem as principais categorias de registos que o tempo de execução cria. 

### <a name="category-hostresults"></a>Anfitrião da categoria.Resultados

Estes registos mostram como "pedidos" em Insights de Aplicação. Indicam sucesso ou falha de uma função.

![Gráfico de pedidos](media/functions-monitoring/requests-chart.png)

Todos estes troncos estão escritos ao nível `Information`. Se filtrar em `Warning` ou acima, não verá nenhum destes dados.

### <a name="category-hostaggregator"></a>Categoria Anfitrião.Agregador

Estes registos fornecem contagens e médias de invocações de funções durante um período de tempo [configurável.](#configure-the-aggregator) O período de incumprimento é de 30 segundos ou 1.000 resultados, o que vier primeiro. 

Os registos estão disponíveis na tabela **customMetrics** em Application Insights. Exemplos são o número de corridas, taxa de sucesso e duração.

![consulta personalizadaMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos estes troncos estão escritos ao nível `Information`. Se filtrar em `Warning` ou acima, não verá nenhum destes dados.

### <a name="other-categories"></a>Outras categorias

Todos os registos para categorias diferentes das já listadas estão disponíveis na tabela **de rastreios** em Insights de Aplicação.

![traces consulta](media/functions-monitoring/analytics-traces.png)

Todos os registos com categorias que começam com `Host` são escritos pelo tempo de funcionamento das Funções. Os registos "Função iniciada" e "Função concluída" têm categoria `Host.Executor`. Para corridas bem sucedidas, estes registos são `Information` nível. As exceções são registadas a `Error` nível. O tempo de execução também cria `Warning` registos de nível, por exemplo: mensagens de fila enviadas para a fila de veneno.

Os registos escritos pelo seu código de função têm categoria `Function` e podem ser qualquer nível de registo.

## <a name="configure-the-aggregator"></a>Configure o agregador

Como notado na secção anterior, o tempo de execução agrega dados sobre execuções de funções durante um período de tempo. O período de incumprimento é de 30 segundos ou 1.000 corridas, o que vier primeiro. Pode configurar esta definição no ficheiro [host.json]  Segue-se um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar amostragem

Application Insights tem uma função [de amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir demasiados dados de telemetria em execuções concluídas em momentos de pico de carga. Quando a taxa de execuções de entrada excede um limiar especificado, os Insights de Aplicação começam a ignorar aleatoriamente algumas das execuções que chegam. A definição padrão para o número máximo de execuções por segundo é de 20 (cinco na versão 1.x). Pode configurar a amostragem em [host.json].  Segue-se um exemplo:

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

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
> [A amostragem](../azure-monitor/app/sampling.md) é ativada por defeito. Se parecer que faltam dados, poderá ter de ajustar as definições de amostragem para se adaptar ao seu cenário de monitorização específico.

## <a name="write-logs-in-c-functions"></a>Escrever registos C# em funções

Pode escrever registos no seu código de função que aparecem como vestígios em Insights de Aplicação.

### <a name="ilogger"></a>ILogger

Utilize um parâmetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) nas suas funções em vez de um parâmetro `TraceWriter`. Os registos criados através da utilização `TraceWriter` vão para a Application Insights, mas `ILogger` permite-lhe fazer [a exploração madeireira estruturada](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, você chama `Log<level>` métodos de [extensão no ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar registos. O código seguinte escreve `Information` registos com a categoria "Função.<YOUR_FUNCTION_NAME>. Utilizador."

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

Se mantiver a mesma cadeia de mensagens e inverter a ordem dos parâmetros, o texto de mensagem resultante teria os valores nos lugares errados.

Os espaços reservados são manuseados desta forma para que possa fazer a exploração madeireira estruturada. Application Insights armazena os pares de nomes do parâmetro e a cadeia de mensagens. O resultado é que os argumentos da mensagem tornam-se campos sobre os que se pode consultar.

Se a chamada do seu método de logger se parecer com o exemplo anterior, pode consultar o campo `customDimensions.prop__rowKey`. O prefixo `prop__` é adicionado para garantir que não há colisões entre os campos que o tempo de funcionamento adiciona e campos que o seu código de função adiciona.

Também pode consultar a cadeia de mensagens original, referindo-se ao campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma amostra de representação da JSON de dados `customDimensions`:

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

Nas C# funções do script, pode utilizar o método de extensão `LogMetric` em `ILogger` para criar métricas personalizadas em Insights de Aplicação. Aqui está uma chamada de método de amostra:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Este código é uma alternativa para chamar `TrackMetric` utilizando a API de Insights de Aplicação para .NET.

## <a name="write-logs-in-javascript-functions"></a>Escreva registos nas funções JavaScript

Nas funções Node.js, use `context.log` para escrever registos. A exploração madeireira estruturada não está ativada.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

Quando estiver a funcionar na [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de funcionamento das funções, as funções nonó.js podem usar o método `context.log.metric` para criar métricas personalizadas em Insights de Aplicação. Este método não é suportado atualmente na versão 2.x e posteriormente. Aqui está uma chamada de método de amostra:

```javascript
context.log.metric("TestMetric", 1234);
```

Este código é uma alternativa para chamar `trackMetric` utilizando o Node.js SDK para Insights de Aplicação.

## <a name="log-custom-telemetry-in-c-functions"></a>Registar telemetria C# personalizada em funções

Pode utilizar o pacote [NuGet Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar dados personalizados de telemetria para Insights de Aplicação. O C# exemplo que se segue utiliza a API de [telemetria personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes .NET, C# mas o código Deinsights de Aplicação é o mesmo para script.

### <a name="version-2x-and-later"></a>Versão 2.x e mais tarde

A versão 2.x e as versões posteriores do tempo de execução utilizam funcionalidades mais recentes em Insights de Aplicação para correlacionar automaticamente a telemetria com a operação atual. Não há necessidade de definir manualmente a operação `Id`, `ParentId`ou `Name` campos.

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

Não ligue para `TrackRequest` ou `StartOperation<RequestTelemetry>` porque verá pedidos duplicados para uma invocação de função.  O tempo de funcionamento das Funções rastreia automaticamente os pedidos.

Não `telemetryClient.Context.Operation.Id`coloque. Esta definição global causa uma correlação incorreta quando muitas funções estão a funcionar simultaneamente. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modifique a sua propriedade `Context`. Em seguida, passe a instância de telemetria para o método de `Track` correspondente no `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`). Este método garante que a telemetria tem os detalhes de correlação corretos para a invocação da função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registar telemetria personalizada nas funções JavaScript

Aqui está um snippet de código de amostra que envia telemetria personalizada com o [Insípido de Aplicação Node.js SDK:](https://github.com/microsoft/applicationinsights-node.js)

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

O parâmetro `tagOverrides` define o `operation_Id` ao ID de invocação da função. Esta definição permite-lhe correlacionar toda a telemetria gerada automaticamente e personalizada para uma determinada invocação de função.

## <a name="dependencies"></a>Dependências

Funções v2 recolhe automaticamente dependências para pedidos HTTP, ServiceBus, EventHub e SQL.

Pode escrever código personalizado para mostrar as dependências. Por exemplo, consulte o código da amostra na secção de [ C# telemetria personalizada](#log-custom-telemetry-in-c-functions). O código da amostra resulta num mapa de aplicação em Insights de *Aplicação* que se parece com a seguinte imagem:

![Mapeamento de aplicações](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Questões de relatório

Para reportar um problema com a integração de Insights de Aplicação em Funções, ou para fazer uma sugestão ou pedido, [criar um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Registos de streaming

Ao desenvolver uma aplicação, muitas vezes quer ver o que está sendo escrito nos registos em quase tempo real quando corre em Azure.

Existem duas formas de ver um fluxo de ficheiros de registo a ser gerado pelas execuções da sua função.

* **Streaming de log incorporado**: a plataforma Do Serviço de Aplicações permite-lhe visualizar um fluxo dos ficheiros de registo da sua aplicação. Isto equivale à saída observada quando depura as suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando utiliza o separador **Test** no portal. Todas as informações baseadas em registo são apresentadas. Para mais informações, consulte os [registos do Stream](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming suporta apenas uma instância única, e não pode ser usado com uma aplicação em execução no Linux num plano de consumo.

* **Live Metrics Stream**: quando a sua aplicação de funções estiver ligada aos Insights de [Aplicação,](#enable-application-insights-integration)pode ver dados de registo e outras métricas em tempo real no portal Azure utilizando live [Metrics Stream](../azure-monitor/app/live-stream.md). Utilize este método ao monitorizar as funções em funcionamento em várias instâncias ou no Linux num plano de consumo. Este método utiliza [dados amostrados.](#configure-sampling)

Os fluxos de log podem ser vistos tanto no portal como na maioria dos ambientes de desenvolvimento local. 

### <a name="portal"></a>Portal

Pode ver ambos os tipos de fluxos de registo no portal.

#### <a name="built-in-log-streaming"></a>Streaming de log incorporado

Para visualizar os registos de streaming no portal, selecione o separador **de funcionalidades da Plataforma** na sua aplicação de função. Em seguida, sob **monitorização,** escolha **streaming de registo**.

![Ativar registos de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isto liga a sua aplicação ao serviço de streaming de registos e os registos de aplicações são apresentados na janela. Pode alternar entre **os registos** da Aplicação e **os registos do servidor Web**.  

![Ver registos de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Fluxo de Métricas Ao Vivo

Para ver o Live Metrics Stream para a sua aplicação, selecione o separador **Overview** da sua aplicação de função. Quando tem insights de aplicação ativa, vê um link De insights de **aplicação** em **funcionalidades configuradas**. Este link leva-o à página Insights da Aplicação para a sua aplicação.

Em Insights de Aplicação, selecione **Live Metrics Stream**. As entradas de [registo amostradas](#configure-sampling) são apresentadas em **Telemetria de Amostra**.

![Ver Live Metrics Stream no portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Ferramentas Core

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>CLI do Azure

Pode ativar os registos de streaming utilizando o [Azure CLI](/cli/azure/install-azure-cli). Utilize os seguintes comandos para iniciar sessão, escolha a sua subscrição e transmita ficheiros de registo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Pode ativar os registos de streaming utilizando o [Azure PowerShell](/powershell/azure/overview). Para o PowerShell, utilize os seguintes comandos para adicionar a sua conta Azure, escolha a sua subscrição e transmita ficheiros de registo:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Desativar a exploração madeireira incorporada

Quando ativa o Application Insights, desative a exploração madeireira incorporada que utiliza o Armazenamento Azure. A exploração madeireira incorporada é útil para testes com cargas de trabalho leves, mas não se destina a uma utilização de produção de alta carga. Para monitorização da produção, recomendamos a Aplicação Insights. Se a exploração madeireira incorporada for utilizada na produção, o registo de exploração madeireira pode estar incompleto devido à aceleração do Armazenamento Azure.

Para desativar a exploração madeireira incorporada, elimine a definição de aplicações `AzureWebJobsDashboard`. Para obter informações sobre como eliminar as definições de aplicações no portal Azure, consulte a secção de **definições** de Aplicação de [Como gerir uma aplicação de função](functions-how-to-use-azure-function-app-settings.md#settings). Antes de eliminar a definição da aplicação, certifique-se de que nenhuma função existente na mesma aplicação de função utiliza a definição para gatilhos ou encadernações de Armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [ASP.NET loglogging Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
