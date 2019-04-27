---
title: Processar eventos externos na funções duráveis - Azure
description: Saiba como manipular eventos externos na extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: eb024e11b78d13d5ab4544c634acef2ade8141c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730791"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Manipulação de eventos externos nas funções durável (funções do Azure)

As funções do Orchestrator têm a capacidade de esperar e escutar eventos externos. Esta funcionalidade de [funções duráveis](durable-functions-overview.md) , muitas vezes, é útil para lidar com interação humana ou outros acionadores externos.

> [!NOTE]
> Eventos externos são unidirecionais operações assíncronas. Eles não são adequados para situações em que o cliente enviar o evento precisa de uma resposta síncrona da função de orquestrador.

## <a name="wait-for-events"></a>Aguardar por eventos

O [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) método permite que uma função de orquestrador aguardar e escutar um evento externo de forma assíncrona. A função de orquestrador escuta declara a *name* do evento e o *forma dos dados* ele espera receber.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

O exemplo anterior está à escuta de um único evento específico e efetua uma ação quando é recebido.

Pode ouvir por vários eventos em simultâneo, como no exemplo seguinte, que aguarda uma das três notificações de eventos possíveis.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

O exemplo anterior escuta *qualquer* de vários eventos. Também é possível aguardar *todos os* eventos.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) aguarda indefinidamente alguma entrada.  A aplicação de funções pode ser com segurança descarregada enquanto espera. Se e quando é recebido um evento para esta instância de orquestração, ele é acordado automaticamente e imediatamente processa o evento.

> [!NOTE]
> Se a sua aplicação function app utiliza o plano de consumo, sem custos de faturas são incorridos enquanto uma função de orquestrador está aguardando uma tarefa a partir `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript), não importa o período de tempo de espera.

No .NET, se a carga do evento não pode ser convertida para o tipo esperado `T`, é emitida uma exceção.

## <a name="send-events"></a>Enviar eventos

O [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe enviará os eventos `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` aguarda que (JavaScript).  O `RaiseEventAsync` leva *eventName* e *eventData* como parâmetros. Os dados do evento tem de ser serializável JSON.

Segue-se uma função de acionada por fila de exemplo que envia um evento de "Aprovação" para uma instância de função do orchestrator. O ID de instância da orquestração é proveniente do corpo da mensagem de fila.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) enfileira uma mensagem que obtém captada por função de orquestrador de espera. Se a instância não está à espera no especificado *nome do evento,* a mensagem de evento é adicionada a uma fila na memória. Se a instância da orquestração mais tarde começa a escutar para isso *nome do evento,* irá verificar a fila para mensagens de eventos.

> [!NOTE]
> Se não existir nenhuma instância de orquestração com especificado *ID da instância*, a mensagem de evento é rejeitada. Para obter mais informações sobre este comportamento, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Ao desenvolver localmente em JavaScript, terá de definir a variável de ambiente `WEBSITE_HOSTNAME` para `localhost:<port>`, por ex. `localhost:7071` Para utilizar os métodos em `DurableOrchestrationClient`. Para obter mais informações sobre este requisito, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como configurar a orquestrações externas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que tem de aguardar por eventos externos](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda a interação humana](durable-functions-phone-verification.md)