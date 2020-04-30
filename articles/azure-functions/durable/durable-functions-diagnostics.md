---
title: Diagnósticos em Funções Duráveis - Azure
description: Aprenda a diagnosticar problemas com a extensão de Funções Duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79278197"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostics in Durable Functions in Azure (Diagnósticos no Durable Functions no Azure)

Existem várias opções para diagnosticar problemas com [Funções Duráveis.](durable-functions-overview.md) Algumas destas opções são iguais às das funções normais e outras são exclusivas do Durable Functions.

## <a name="application-insights"></a>Application Insights

[Aplicação Insights](../../azure-monitor/app/app-insights-overview.md) é a forma recomendada de fazer diagnósticos e monitorização em Funções Azure. O mesmo se aplica às Funções Duráveis. Para uma visão geral de como alavancar os Insights de Aplicação na sua aplicação de função, consulte [as Funções Monitor Azure](../functions-monitoring.md).

A Extensão Durável funções azure também emite *eventos* de rastreio que permitem rastrear a execução de ponta a ponta de uma orquestração. Estes eventos de rastreio podem ser encontrados e consultados utilizando a ferramenta [Application Insights Analytics](../../azure-monitor/app/analytics.md) no portal Azure.

### <a name="tracking-data"></a>Dados de rastreio

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de rastreio seja escrito para a coleção **de vestígios** em Application Insights. Este evento contém uma carga de carga **personalizadaDimensions** com vários campos.  Os nomes de `prop__`campo estão todos preparados com.

* **hubName**: O nome do centro de tarefas em que as suas orquestrações estão em execução.
* **nome**do aplicativo : O nome da aplicação de funções. Este campo é útil quando tem várias aplicações de função que partilham a mesma instância de Insights de Aplicação.
* **slotName**: A ranhura de [implementação](../functions-deployment-slots.md) em que a aplicação de função atual está em execução. Este campo é útil quando se aproveitam as ranhuras de implantação para versões das suas orquestrações.
* **nome de função**: O nome do orquestrador ou função de atividade.
* **funçãoTipo**: O tipo da função, tal como **Orchestrator** ou **Atividade**.
* **instânciaId**: A identificação única da instância de orquestração.
* **estado**: O estado de execução do ciclo de vida da ocorrência. Os valores válidos incluem:
  * **Agendado**: A função estava marcada para a execução, mas ainda não começou a funcionar.
  * **Início**: A função começou a funcionar mas ainda não aguardou ou completou.
  * **Aguardado**: O orquestrador tem agendado algum trabalho e está à espera que esteja concluído.
  * **Ouvir**: O orquestrador está a ouvir uma notificação de evento externo.
  * **Concluída**: A função foi concluída com sucesso.
  * **Falha:** A função falhou com um erro.
* **razão**: Dados adicionais associados ao evento de rastreio. Por exemplo, se uma instância estiver à espera de uma notificação de evento externo, este campo indica o nome do evento que aguarda. Se uma função tiver falhado, este campo conterá os detalhes do erro.
* **isReplay**: Valor booleano indicando se o evento de rastreio é para execução repetida.
* **extensãoVersão**: A versão da extensão de tarefa durável. A informação da versão é especialmente importante ao reportar possíveis bugs na extensão. Casos de longa duração podem reportar várias versões se ocorrer uma atualização durante a sua execução.
* **sequênciaNúmero:** Número de sequência de execução para um evento. Combinado com o carimbo de tempo ajuda a ordenar os eventos pelo tempo de execução. *Note que este número será reiniciado a zero se o hospedeiro reiniciar enquanto a instância estiver em execução, por isso é importante classificar sempre por carimbo de tempo primeiro, em seguida, sequenciarNúmero.*

A verbosidade dos dados de rastreio emitidos para `logger` a Aplicação Insights `logging` pode ser configurada na secção `host.json` (Funções 1.x) ou (Funções 2.0) do ficheiro.

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

Por padrão, todos os eventos de rastreio não reprodução são emitidos. O volume de dados pode `Host.Triggers.DurableTask` `"Warning"` ser `"Error"` reduzido através da definição ou, em caso em que os eventos de rastreio serão emitidos apenas para situações excecionais.

Para permitir a emissão dos eventos `LogReplayEvents` de repetição `true` da `host.json` orquestração verbosa, o pode ser definido no ficheiro sob `durableTask` o que mostra:

#### <a name="functions-10"></a>Funções 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funções 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Por predefinição, a telemetria De Insights de Aplicação é amostrada pelo tempo de funcionamento das Funções Azure para evitar emitir dados com demasiada frequência. Isto pode fazer com que a informação de rastreio se perca quando muitos eventos de ciclo de vida ocorrem num curto espaço de tempo. O artigo de Monitorização de [Funções Azure](../functions-monitoring.md#configure-sampling) explica como configurar este comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A seguinte consulta mostra dados históricos de rastreio para uma única instância da orquestração da função [Hello Sequence.](durable-functions-sequence.md) Está escrito usando a linguagem de consulta de insights de [aplicação (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Filtra a execução de repetição para que apenas o caminho *lógico* de execução seja mostrado. Os eventos podem ser `timestamp` `sequenceNumber` ordenados separando-se por e como mostrado na consulta abaixo:

```AIQL
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

O resultado é uma lista de eventos de rastreio que mostram o caminho de execução da orquestração, incluindo quaisquer funções de atividade ordenadas pelo tempo de execução em ordem ascendente.

![Consulta de Insights de Aplicação](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta sumária de instância

A seguinte consulta mostra o estado de todas as instâncias de orquestração que foram executadas num intervalo de tempo especificado.

```AIQL
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

O resultado é uma lista de identificações de instância e o seu estado atual de tempo de execução.

![Consulta de Insights de Aplicação](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registo

É importante ter o comportamento de repetição do orquestrador em mente ao escrever registos diretamente de uma função orquestradora. Por exemplo, considere a seguinte função orquestradora:

### <a name="precompiled-c"></a>C pré-compilado #

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

### <a name="c-script"></a>C# Script

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
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

### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

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

Os dados de registo resultantes vão parecer algo parecidos com a seguinte saída de exemplo:

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
> Lembre-se que enquanto os registos dizem chamar F1, F2 e F3, essas funções só são *realmente* chamadas na primeira vez que são encontradas. As chamadas subsequentes que acontecem durante a repetição são ignoradas e as saídas são reproduzidas para a lógica orquestradora.

Se quiser apenas iniciar sessão na execução de não repetição, só pode escrever uma expressão condicional para registar se `IsReplaying` for `false`. Considere o exemplo acima, mas desta vez com verificações de repetição.

#### <a name="precompiled-c"></a>C pré-compilado #

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

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
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

#### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

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

Começando nas Funções Duráveis 2.0, as funções de `ILogger` orquestrador .NET também têm a opção de criar uma que filtra automaticamente as declarações de registo durante a repetição. Esta filtragem automática é `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` feita utilizando a API.

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

Com as alterações anteriormente mencionadas, a saída de registo é a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Os exemplos c# anteriores são para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

## <a name="custom-status"></a>Estatuto Personalizado

O estatuto de orquestração personalizada permite definir um valor de estado personalizado para a sua função de orquestrador. Este estado é fornecido através da consulta `IDurableOrchestrationClient.GetStatusAsync` de estado HTTP API ou da API. O estado de orquestração personalizada permite uma monitorização mais rica para funções orquestradoras. Por exemplo, o código de `IDurableOrchestrationContext.SetCustomStatus` função do orquestrador pode incluir chamadas para atualizar o progresso para uma operação de longa duração. Um cliente, como uma página web ou outro sistema externo, poderia então consultar periodicamente as APIs de consulta de estado http para informações de progresso mais ricas. Uma amostra `IDurableOrchestrationContext.SetCustomStatus` que utiliza é fornecida abaixo:

### <a name="precompiled-c"></a>C pré-compilado #

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
> O exemplo c# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

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

Enquanto a orquestração está em execução, os clientes externos podem obter este estatuto personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Os clientes receberão a seguinte resposta:

```http
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
> A carga de estado personalizado está limitada a 16 KB de texto JSON UTF-16 porque precisa de ser capaz de caber numa coluna de armazenamento de mesa Azure. Pode utilizar o armazenamento externo se necessitar de uma carga útil maior.

## <a name="debugging"></a>Depurar

As Funções Azure suportam diretamente o código de função de depuração, e esse mesmo suporte transporta para funções duráveis, seja em execução em Azure ou localmente. No entanto, há alguns comportamentos a ter em conta ao depurar:

* **Repetição**: As funções de orquestrador [são repostas](durable-functions-orchestrations.md#reliability) regularmente quando são recebidas novas inputs. Este comportamento significa que uma única execução *lógica* de uma função orquestradora pode resultar em atingir o mesmo ponto de rutura várias vezes, especialmente se for definido no início do código de função.
* **Aguarde** `await` : Sempre que um é encontrado numa função de orquestrador, devolve o controlo ao despacho do Quadro de Tarefas Duráveis. Se for a primeira `await` vez que um determinado em particular é encontrado, a tarefa associada *nunca* é retomada. Como a tarefa nunca retoma, *pisar* o aguardado (F10 no Estúdio Visual) não é possível. Pisar só funciona quando uma tarefa está a ser reproduzida.
* **Tempos**de mensagens : As Funções Duráveis usam internamente mensagens de fila para impulsionar a execução de funções orquestradoras, de atividade e de entidade. Num ambiente multi-VM, invadir a depuração por longos períodos de tempo poderia fazer com que outro VM captasse a mensagem, resultando numa execução duplicada. Este comportamento também existe para funções regulares de gatilho de fila, mas é importante apontar neste contexto, uma vez que as filas são um detalhe de implementação.
* **Paragem e início**: As mensagens em funções duráveis persistem entre sessões de depuração. Se parar de depurar e terminar o processo de hospedar local enquanto uma função durável estiver a ser executada, essa função pode ser executada automaticamente numa futura sessão de depuração. Este comportamento pode ser confuso quando não se espera. Limpar todas as mensagens das filas de [armazenamento interno entre](durable-functions-perf-and-scale.md#internal-queue-triggers) sessões de depuração é uma técnica para evitar este comportamento.

> [!TIP]
> Ao definir pontos de rutura em funções orquestradoras, se quiser apenas quebrar a execução de não repetição, pode definir um ponto de rutura condicional que só se rompe se `IsReplaying` for `false`.

## <a name="storage"></a>Armazenamento

Por predefinição, as Lojas Durable Stores state in Azure Storage. Este comportamento significa que pode inspecionar o estado das suas orquestrações utilizando ferramentas como o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Screenshot do Explorador de Armazenamento Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Isto é útil para depurar porque você vê exatamente em que estado uma orquestração pode estar em. As mensagens nas filas também podem ser examinadas para saber que trabalho está pendente (ou preso em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de mesa, evite tomar qualquer dependência nesta mesa. Pode mudar à medida que a extensão das Funções Duráveis evolui.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre monitorização em Funções Azure](../functions-monitoring.md)
