---
title: Diagnósticos no Durable Functions – Azure
description: Saiba como diagnosticar problemas com a extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 333ddbc15e3ff62b1cd46383c4e3be75fb3dbb88
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614943"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnósticos em Durable Functions no Azure

Há várias opções para diagnosticar problemas com [Durable Functions](durable-functions-overview.md). Algumas destas opções são iguais às das funções normais e outras são exclusivas do Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application insights](../../azure-monitor/app/app-insights-overview.md) é a maneira recomendada de fazer diagnósticos e monitoramento no Azure functions. O mesmo se aplica a Durable Functions. Para obter uma visão geral de como aproveitar Application Insights em seu aplicativo de funções, consulte [monitorar Azure Functions](../functions-monitoring.md).

A extensão durável Azure Functions também emite *eventos de rastreamento* que permitem rastrear a execução de ponta a ponta de uma orquestração. Esses eventos de rastreamento podem ser encontrados e consultados usando a ferramenta de [análise de Application insights](../../azure-monitor/app/analytics.md) no portal do Azure.

### <a name="tracking-data"></a>Dados de rastreamento

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de rastreamento seja gravado na coleção de **rastreamentos** em Application insights. Esse evento contém uma carga **customDimensions** com vários campos.  Os nomes de campo são todos precedidos com `prop__`.

* **hubName**: o nome do hub de tarefas no qual suas orquestrações estão em execução.
* **AppName**: o nome do aplicativo de funções. Esse campo é útil quando você tem vários aplicativos de função compartilhando a mesma instância de Application Insights.
* **slotname**: o [slot de implantação](../functions-deployment-slots.md) no qual o aplicativo de funções atual está em execução. Esse campo é útil quando você aproveita os slots de implantação para a versão de suas orquestrações.
* **FunctionName**: o nome da função de orquestrador ou de atividade.
* **FunctionType**: o tipo da função, como **orquestrador** ou **atividade**.
* **InstanceId**: a ID exclusiva da instância de orquestração.
* **estado**: o estado de execução do ciclo de vida da instância. Os valores válidos incluem:
  * **Agendado**: a função foi agendada para execução, mas ainda não começou a ser executada.
  * **Iniciado**: a função começou a ser executada, mas ainda não foi aguardada ou concluída.
  * **Aguardado**: o orquestrador agendou algum trabalho e está aguardando sua conclusão.
  * **Ouvindo**: o orquestrador está ouvindo uma notificação de evento externo.
  * **Concluído**: a função foi concluída com êxito.
  * **Falha**: a função falhou com um erro.
* **motivo**: dados adicionais associados ao evento de rastreamento. Por exemplo, se uma instância estiver aguardando uma notificação de evento externo, esse campo indicará o nome do evento que está aguardando. Se uma função tiver falhado, esse campo conterá os detalhes do erro.
* **isReplay**: valor booliano que indica se o evento de rastreamento é para execução repetida.
* **extensionVersion**: a versão da extensão de tarefa durável. As informações de versão são dados especialmente importantes ao relatar possíveis bugs na extensão. Instâncias de execução longa podem relatar várias versões se uma atualização ocorrer durante a execução.
* **sequenceNumber**: número de sequência de execução para um evento. Combinado com o carimbo de data/hora ajuda a ordenar os eventos por tempo de execução. *Observe que esse número será redefinido como zero se o host for reiniciado enquanto a instância estiver em execução, portanto, é importante sempre classificar pelo carimbo de data/hora primeiro e, depois, sequenceNumber.*

O detalhamento dos dados de rastreamento emitidos para Application Insights pode ser configurado na seção `logger` (funções 1. x) ou `logging` (funções 2,0) do arquivo de `host.json`.

#### <a name="functions-10"></a>Funções 1,0

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

#### <a name="functions-20"></a>Funções 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Por padrão, todos os eventos de rastreamento sem reprodução são emitidos. O volume de dados pode ser reduzido Configurando `Host.Triggers.DurableTask` para `"Warning"` ou `"Error"` em que os eventos de controle de caso serão emitidos apenas para situações excepcionais.

Para habilitar a emissão dos eventos de reprodução de orquestração detalhada, o `LogReplayEvents` pode ser definido como `true` no arquivo de `host.json` em `durableTask`, conforme mostrado:

#### <a name="functions-10"></a>Funções 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funções 2,0

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
> Por padrão, a telemetria Application Insights é amostrada pelo tempo de execução do Azure Functions para evitar emitir dados com muita frequência. Isso pode fazer com que as informações de rastreamento sejam perdidas quando muitos eventos de ciclo de vida ocorrem em um curto período de tempo. O [artigo Azure Functions monitoramento](../functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta a seguir mostra dados de controle de histórico para uma única instância da orquestração de função de [sequência de saudação](durable-functions-sequence.md) . Ele é escrito usando a [linguagem de consulta de Application insights (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Ele filtra a execução de reprodução para que apenas o caminho de execução *lógica* seja mostrado. Os eventos podem ser ordenados por meio da classificação por `timestamp` e `sequenceNumber` conforme mostrado na consulta abaixo:

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

O resultado é uma lista de eventos de rastreamento que mostra o caminho de execução da orquestração, incluindo qualquer função de atividade ordenada pelo tempo de execução em ordem crescente.

![Consulta de Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta de resumo da instância

A consulta a seguir exibe o status de todas as instâncias de orquestração que foram executadas em um intervalo de tempo especificado.

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

O resultado é uma lista de IDs de instância e seu status de tempo de execução atual.

![Consulta de Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registo

É importante manter o comportamento de reprodução do orquestrador em mente ao gravar logs diretamente de uma função de orquestrador. Por exemplo, considere a seguinte função de orquestrador:

### <a name="precompiled-c"></a>Pré-compiladoC#

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

### <a name="c-script"></a>C#Prescritiva

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

### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

Os dados de log resultantes serão parecidos com o seguinte exemplo de saída:

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
> Lembre-se de que, enquanto os logs alegam chamar F1, F2 e F3, essas funções são *, na verdade* , chamadas apenas na primeira vez em que são encontradas. As chamadas subsequentes que acontecem durante a repetição são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se você quiser fazer logon somente na execução sem repetição, poderá gravar uma expressão condicional para registrar somente se `IsReplaying` for `false`. Considere o exemplo acima, mas desta vez com as verificações de repetição.

#### <a name="precompiled-c"></a>Pré-compiladoC#

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

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

A partir do Durable Functions 2,0, as funções do .NET Orchestrator também têm a opção de criar um `ILogger` que filtra automaticamente as instruções de log durante a reprodução. Essa filtragem automática é feita usando a API de `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)`.

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

Com as alterações mencionadas anteriormente, a saída do log é a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Os exemplos C# anteriores são para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

## <a name="custom-status"></a>Status personalizado

O status de orquestração personalizado permite definir um valor de status personalizado para a função de orquestrador. Esse status é fornecido por meio da API de consulta de status HTTP ou da API `IDurableOrchestrationClient.GetStatusAsync`. O status personalizado de orquestração permite um monitoramento mais rico para funções de orquestrador. Por exemplo, o código da função de orquestrador pode incluir chamadas de `IDurableOrchestrationContext.SetCustomStatus` para atualizar o progresso de uma operação de execução longa. Um cliente, como uma página da Web ou outro sistema externo, pode consultar periodicamente as APIs de consulta de status HTTP para obter informações mais detalhadas sobre o progresso. Um exemplo de uso de `IDurableOrchestrationContext.SetCustomStatus` é fornecido abaixo:

### <a name="precompiled-c"></a>Pré-compiladoC#

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
> O exemplo C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

Enquanto a orquestração está em execução, os clientes externos podem buscar esse status personalizado:

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
> A carga de status personalizada é limitada a 16 KB de texto JSON UTF-16 porque ele precisa ser capaz de se ajustar em uma coluna de armazenamento de tabelas do Azure. Você pode usar o armazenamento externo se precisar de uma carga maior.

## <a name="debugging"></a>Depurar

O Azure Functions dá suporte diretamente ao código de função de depuração e esse mesmo suporte é enviado para Durable Functions, seja em execução no Azure ou localmente. No entanto, há alguns comportamentos que devem ser considerados durante a depuração:

* **Reprodução**: as funções de orquestrador periodicamente são [reproduzidas](durable-functions-orchestrations.md#reliability) quando novas entradas são recebidas. Esse comportamento significa que uma única execução *lógica* de uma função de orquestrador pode resultar em atingir o mesmo ponto de interrupção várias vezes, especialmente se ele estiver definido no início do código de função.
* **Await**: sempre que um `await` for encontrado em uma função de orquestrador, ele resultará no controle de volta para o Dispatcher do Framework de tarefa durável. Se for a primeira vez que um determinado `await` tiver sido encontrado, a tarefa associada *nunca* será retomada. Como a tarefa nunca é retomada, não é possível *percorrer o Await* (F10 no Visual Studio). A depuração só funciona quando uma tarefa está sendo repetida.
* **Tempos limite de mensagens**: Durable Functions usa internamente mensagens de fila para acionar a execução de funções de orquestrador, atividade e entidade. Em um ambiente de várias VMS, dividir a depuração por longos períodos de tempo pode fazer com que outra VM pegue a mensagem, resultando em execução duplicada. Esse comportamento existe para funções regulares de gatilho de fila também, mas é importante destacar nesse contexto, uma vez que as filas são um detalhe de implementação.
* **Parando e iniciando**: as mensagens nas funções duráveis persistem entre as sessões de depuração. Se você parar a depuração e encerrar o processo de host local enquanto uma função durável estiver em execução, essa função poderá ser executada automaticamente em uma sessão de depuração futura. Esse comportamento pode ser confuso quando não esperado. Limpar todas as mensagens das [filas de armazenamento internas](durable-functions-perf-and-scale.md#internal-queue-triggers) entre sessões de depuração é uma técnica para evitar esse comportamento.

> [!TIP]
> Ao definir pontos de interrupção em funções de orquestrador, se você quiser interromper apenas a execução sem repetição, poderá definir um ponto de interrupção condicional que será interrompido somente se `IsReplaying` for `false`.

## <a name="storage"></a>Storage

Por padrão, o Durable Functions armazena o estado no armazenamento do Azure. Esse comportamento significa que você pode inspecionar o estado de suas orquestrações usando ferramentas como [Gerenciador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de tela Gerenciador de Armazenamento do Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Isso é útil para depuração porque você vê exatamente em que estado uma orquestração pode estar. As mensagens nas filas também podem ser examinadas para saber qual trabalho está pendente (ou preso em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de tabelas, evite usar qualquer dependência nessa tabela. Ele pode mudar à medida que a extensão de Durable Functions evolui.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre monitoramento no Azure Functions](../functions-monitoring.md)
