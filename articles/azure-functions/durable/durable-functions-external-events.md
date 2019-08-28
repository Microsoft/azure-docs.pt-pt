---
title: Manipulando eventos externos no Durable Functions-Azure
description: Saiba como lidar com eventos externos na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9c546064589e82cfef367978ebea98c2c202307
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087295"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Manipulando eventos externos em Durable Functions (Azure Functions)

As funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Esse recurso do [Durable Functions](durable-functions-overview.md) geralmente é útil para lidar com a interação humana ou com outros gatilhos externos.

> [!NOTE]
> Os eventos externos são operações assíncronas unidirecionais. Eles não são adequados para situações em que o cliente que envia o evento precisa de uma resposta síncrona da função de orquestrador.

## <a name="wait-for-events"></a>Aguardar eventos

O método [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permite que uma função de orquestrador Aguarde e escute de forma assíncrona um evento externo. A função de orquestrador de escuta declara o *nome* do evento e a *forma dos dados* que espera receber.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

O exemplo anterior escuta um único evento específico e executa uma ação quando ele é recebido.

Você pode escutar vários eventos simultaneamente, como no exemplo a seguir, que aguarda uma das três notificações de eventos possíveis.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

O exemplo anterior escuta *qualquer um* dos vários eventos. Também é possível aguardar *todos os* eventos.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) aguarda indefinidamente por alguma entrada.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se e quando um evento chega para essa instância de orquestração, ele é ativado automaticamente e processa imediatamente o evento.

> [!NOTE]
> Se seu aplicativo de funções usar o plano de consumo, não haverá encargos de cobrança enquanto uma função de orquestrador estiver aguardando `WaitForExternalEvent` uma tarefa de ( `waitForExternalEvent` .net) ou (JavaScript), não importa quanto tempo ele espera.

No .net, se a carga do evento não puder ser convertida no `T`tipo esperado, uma exceção será lançada.

## <a name="send-events"></a>Enviar eventos

O método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envia os eventos que `WaitForExternalEvent` (.net) ou `waitForExternalEvent` (JavaScript) aguarda.  O `RaiseEventAsync` método usa *EventName* e *EVENTDATA* como parâmetros. Os dados do evento devem ser serializáveis em JSON.

Abaixo está um exemplo de função disparada por fila que envia um evento de "aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente, `RaiseEventAsync` (.net) ou `raiseEvent` (JavaScript) enfileira uma mensagem que é selecionada pela função de orquestrador em espera. Se a instância não estiver aguardando o *nome do evento especificado,* a mensagem de evento será adicionada a uma fila na memória. Se a instância de orquestração mais tarde começar a escutar o *nome do evento,* ele verificará a fila em busca de mensagens de evento.

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a *ID de instância*especificada, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Ao desenvolver localmente em JavaScript, será necessário definir a variável `WEBSITE_HOSTNAME` de ambiente como, por `localhost:<port>`exemplo, `localhost:7071`para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como configurar orquestrações de eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda eventos externos](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)