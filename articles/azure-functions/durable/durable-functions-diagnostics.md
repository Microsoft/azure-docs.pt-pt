---
title: Diagnósticos em Funções Duráveis - Azure
description: Saiba como diagnosticar problemas com a extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584625"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostics in Durable Functions in Azure (Diagnósticos no Durable Functions no Azure)

Existem várias opções para diagnosticar problemas com [funções duradouras.](durable-functions-overview.md) Algumas destas opções são iguais às das funções normais e outras são exclusivas do Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) é a forma recomendada de fazer diagnósticos e monitorização em Funções Azure. O mesmo se aplica às Funções Duradouras. Para obter uma visão geral de como alavancar os Insights de Aplicação na sua aplicação de funções, consulte [As Funções do Monitor Azure](../functions-monitoring.md).

A Extensão Durável de Funções Azure também emite *eventos* de rastreio que lhe permitem rastrear a execução de ponta a ponta de uma orquestração. Estes eventos de rastreio podem ser encontrados e consultados utilizando a ferramenta [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) no portal Azure.

### <a name="tracking-data"></a>Dados de rastreio

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de rastreio seja escrito para a coleção **de vestígios** em Application Insights. Este evento contém uma carga **personalizadadimensional** com vários campos.  Os nomes de campo estão todos preparados `prop__` com.

* **hubName**: O nome do centro de tarefas em que as suas orquestrações estão em execução.
* **appName**: O nome da aplicação de função. Este campo é útil quando tem várias aplicações de funções que partilham a mesma instância De Insights de Aplicação.
* **slotName**: A [ranhura de implantação](../functions-deployment-slots.md) na qual a aplicação de função atual está em execução. Este campo é útil quando utiliza slots de implantação para ver versão das suas orquestrações.
* **funçãoName**: O nome do orquestrador ou função de atividade.
* **funçãoType**: O tipo de função, como **Orquestrador** ou **Atividade**.
* **instanceId**: O ID único da instância de orquestração.
* **estado**: O estado de execução do ciclo de vida do caso. Valores válidos incluem:
  * **Agendada**: A função estava marcada para a execução, mas ainda não começou a funcionar.
  * **Iniciado**: A função começou a funcionar mas ainda não aguarda ou está concluída.
  * **Aguarda-se:** O orquestrador agendou alguns trabalhos e aguarda a sua conclusão.
  * **Ouvir**: O orquestrador está a ouvir uma notificação de eventos externos.
  * **Concluído**: A função foi concluída com sucesso.
  * **Falha:** A função falhou com um erro.
* **razão**: Dados adicionais associados ao evento de rastreio. Por exemplo, se um caso estiver à espera de uma notificação de evento externo, este campo indica o nome do evento que está à espera. Se uma função falhar, este campo conterá os detalhes de erro.
* **isReplay**: Valor booleano indicando se o evento de rastreio é para execução repetida.
* **extensão :** A versão da extensão de Tarefa Duradoura. A informação da versão é especialmente importante ao reportar possíveis bugs na extensão. Casos de longa duração podem relatar várias versões se uma atualização ocorrer durante o seu funcionamento.
* **sequência Número :** Número da sequência de execução para um evento. Combinado com o tempotampia ajuda a ordenar os eventos pelo tempo de execução. *Note que este número será reiniciado para zero se o anfitrião reiniciar enquanto a instância estiver em execução, por isso é importante classificar sempre por tempos primeiro e, em seguida, sequenciarNumber.*

A verbosidade dos dados de rastreio emitidos para Insights de Aplicação pode ser configurada na `logger` secção (Funções 1.x) ou `logging` (Funções 2.0) do `host.json` ficheiro.

#### <a name="functions-10"></a>Funções 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Funções 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Por padrão, todos os eventos de rastreio não-repetição são emitidos. O volume de dados pode ser reduzido através da definição `Host.Triggers.DurableTask` `"Warning"` de eventos de rastreio `"Error"` ou, caso em que os eventos de rastreio só serão emitidos para situações excecionais.

Para permitir a emissão dos eventos de repetição da orquestração verbose, o `LogReplayEvents` pode ser definido no ficheiro sob o `true` `host.json` `durableTask` indicado:

#### <a name="functions-10"></a>Funções 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funções 2.0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Por predefinição, a telemetria Application Insights é amostrada pelo tempo de funcionamento das Funções Azure para evitar a emissão de dados com demasiada frequência. Isto pode fazer com que a informação de rastreio se perca quando muitos eventos de ciclo de vida ocorrem em um curto período de tempo. O [artigo de Monitorização de Funções Azure](../configure-monitoring.md#configure-sampling) explica como configurar este comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A seguinte consulta mostra dados históricos de rastreamento para uma única instância da orquestração da função [Hello Sequence.](durable-functions-sequence.md) Está escrito usando a [língua de consulta de Kusto.](/azure/data-explorer/kusto/query/) Filtra a execução de repetição para que apenas seja mostrado o caminho de execução *lógica.* Os eventos podem ser encomendados por triagem por `timestamp` e como mostrado na consulta `sequenceNumber` abaixo:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

O resultado é uma lista de eventos de rastreio que mostra o caminho de execução da orquestração, incluindo quaisquer funções de atividade ordenadas pelo tempo de execução em ordem ascendente.

![Aplicação Insights única instância ordenada consulta](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta sumária de instância

A seguinte consulta mostra o estado de todas as instâncias de orquestração que foram executadas num intervalo de tempo especificado.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

O resultado é uma lista de identificações de casos e o seu estado atual de funcionação.

![Consulta de instância única do Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Registo de quadros de tarefas duráveis

Os registos de extensão durável são úteis para entender o comportamento da sua lógica de orquestração. No entanto, estes registos nem sempre contêm informações suficientes para depurar problemas de desempenho e fiabilidade ao nível do quadro. A partir de **v2.3.0** da extensão Durable, os registos emitidos pelo Quadro de Tarefas Duráveis subjacente (DTFx) também estão disponíveis para recolha.

Ao olhar para os registos emitidos pelo DTFx, é importante entender que o motor DTFx é composto por dois componentes: o motor de despacho principal ( `DurableTask.Core` ) e um dos muitos fornecedores de armazenamento suportado (Funções Duradouras utiliza por `DurableTask.AzureStorage` defeito).

* **DurableTask.Core:** contém informações sobre execução de orquestração e agendamento de baixo nível.
* **DurableTask.AzureStorage**: contém informações relacionadas com interações com artefactos de armazenamento Azure, incluindo as filas internas, bolhas e mesas de armazenamento usadas para armazenar e buscar o estado de orquestração interna.

Pode ativar estes registos atualizando a `logging/logLevel` secção dohost.jsda sua aplicação de funções **no** ficheiro. O exemplo a seguir mostra como permitir registos de alerta e erro de ambos `DurableTask.Core` `DurableTask.AzureStorage` e:

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Se tiver o Application Insights ativado, estes registos serão automaticamente adicionados à `trace` coleção. Pode revistá-los da mesma forma que procura `trace` outros registos usando consultas kusto.

> [!NOTE]
> Para aplicações de produção, recomenda-se que ative `DurableTask.Core` e `DurableTask.AzureStorage` faça logins utilizando o `"Warning"` filtro. Filtros de verbosidade mais elevados, tais como `"Information"` são muito úteis para depurar problemas de desempenho. No entanto, estes eventos de registo são de grande volume e podem aumentar significativamente os custos de armazenamento de dados do Application Insights.

A seguinte consulta kusto mostra como consultar os registos DTFx. A parte mais importante da consulta é `where customerDimensions.Category startswith "DurableTask"` uma vez que filtra os resultados para registos nas `DurableTask.Core` categorias e `DurableTask.AzureStorage` categorias.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
O resultado é um conjunto de registos escritos pelos fornecedores de registos Quadro de Tarefas Durable.

![Resultados da consulta DTFx do Application Insights DTFx](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Para obter mais informações sobre os eventos de registo disponíveis, consulte a documentação estruturada do Quadro de [Tarefas Durable no GitHub.](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging)

## <a name="app-logging"></a>Registo de aplicativos

É importante manter o comportamento do orquestrador em mente ao escrever registos diretamente de uma função orquestradora. Por exemplo, considere a seguinte função orquestradora:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Os dados de registo resultantes vão parecer algo como a seguinte saída de exemplo:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se que enquanto os registos afirmam chamar F1, F2 e F3, essas funções só são *realmente* chamadas na primeira vez que são encontradas. Chamadas subsequentes que acontecem durante a repetição são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se pretender apenas escrever registos em execuções não repetidas, só pode escrever uma expressão condicional para registar se a bandeira "reproduzir" for `false` . Considere o exemplo acima, mas desta vez com verificações de repetição.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

A partir de Funções Duráveis 2.0, as funções de orquestrador .NET também têm a opção de criar um `ILogger` que filtra automaticamente as declarações de registo durante a repetição. Esta filtragem automática é feita utilizando a [API IDurableOrchestrationContext.CreateReplaySafeLogger (ILogger).](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> Os exemplos C# anteriores são para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Com as alterações anteriormente mencionadas, a saída de registo é a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Estado personalizado

O estado de orquestração personalizada permite-lhe definir um valor de estado personalizado para a sua função de orquestrador. Este estado personalizado é então visível para clientes externos através da [consulta de estado HTTP API](durable-functions-http-api.md#get-instance-status) ou através de chamadas API específicas da linguagem. O estado de orquestração personalizada permite uma monitorização mais rica para funções de orquestradores. Por exemplo, o código de função do orquestrador pode invocar a API "set custom status" para atualizar os progressos para uma operação de longa duração. Um cliente, como uma página web ou outro sistema externo, poderia então consultar periodicamente as APIs de consulta de estado HTTP para obter informações de progresso mais ricas. O código de amostra para definir um valor de estado personalizado numa função orquestradora é fornecido abaixo:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> O exemplo C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Enquanto a orquestração está em execução, os clientes externos podem obter este estatuto personalizado:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

Os clientes terão a seguinte resposta:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> A carga útil do estado personalizado está limitada a 16 KB de texto UTF-16 JSON porque precisa de ser capaz de caber numa coluna de armazenamento de mesa Azure. Pode utilizar o armazenamento externo se precisar de uma carga útil maior.

## <a name="debugging"></a>Depurar

O Azure Functions suporta o código de função de depuração diretamente, e esse mesmo suporte transporta-se para Funções Duradouras, quer esteja a funcionar em Azure ou localmente. No entanto, existem alguns comportamentos a ter em conta quando depurar:

* **Repetição**: As funções do orquestrador [repetem-se](durable-functions-orchestrations.md#reliability) regularmente quando são recebidas novas entradas. Este comportamento significa que uma única execução *lógica* de uma função orquestradora pode resultar em atingir o mesmo ponto de rutura várias vezes, especialmente se for definida no início do código de função.
* **Aguarde:** Sempre que um `await` seja encontrado numa função orquestradora, ele devolve o controlo ao despacho quadro de tarefas durável. Se for a primeira vez que um determinado `await` é encontrado, a tarefa associada *nunca* será retomada. Como a tarefa nunca recomeça, *pisar* o aguardo (F10 em Visual Studio) não é possível. Pisar só funciona quando uma tarefa está a ser repetida.
* **Intervalos de mensagens**: Funções Duráveis usam internamente mensagens de fila para impulsionar a execução de funções de orquestrador, atividade e entidade. Num ambiente multi-VM, invadir a depuragem por longos períodos de tempo pode fazer com que outro VM apanhe a mensagem, resultando numa execução duplicada. Este comportamento também existe para funções regulares de gatilho de fila, mas é importante salientar neste contexto, uma vez que as filas são um detalhe de implementação.
* **Parar e iniciar:** As mensagens em funções duradouras persistem entre as sessões de depuração. Se parar de depurar e terminar o processo de anfitrião local enquanto uma função durável estiver a ser executada, essa função poderá voltar a executar automaticamente numa futura sessão de depuração. Este comportamento pode ser confuso quando não é esperado. Limpar todas as mensagens das [filas internas](durable-functions-perf-and-scale.md#internal-queue-triggers) de armazenamento entre sessões de depuração é uma técnica para evitar este comportamento.

> [!TIP]
> Ao definir pontos de rutura nas funções do orquestrador, se quiser apenas quebrar a execução não reprodução, pode definir um ponto de rutura condicional que só se parte se o valor "reproduzir" for `false` .

## <a name="storage"></a>Armazenamento

Por predefinição, as lojas Durable Functions armazenam o estado no Azure Storage. Este comportamento significa que pode inspecionar o estado das suas orquestrações utilizando ferramentas como o [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Screenshot do Explorador de Armazenamento Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Isto é útil para depurar porque você vê exatamente em que estado uma orquestração pode estar. As mensagens nas filas também podem ser examinadas para saber que trabalho está pendente (ou preso em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de mesas, evite ter qualquer dependência nesta mesa. Pode mudar à medida que a extensão de Funções Duradouras evolui.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre monitorização em Funções Azure](../functions-monitoring.md)
