---
title: Manipulando eventos externos no Durable Functions-Azure
description: Saiba como lidar com eventos externos na extensão de Durable Functions para Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262967"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Manipulando eventos externos em Durable Functions (Azure Functions)

As funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Esse recurso do [Durable Functions](durable-functions-overview.md) geralmente é útil para lidar com a interação humana ou com outros gatilhos externos.

> [!NOTE]
> Os eventos externos são operações assíncronas unidirecionais. Eles não são adequados para situações em que o cliente que envia o evento precisa de uma resposta síncrona da função de orquestrador.

## <a name="wait-for-events"></a>Aguardar eventos

Os métodos `WaitForExternalEvent` (.NET) e `waitForExternalEvent` (JavaScript) da [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger) permitem que uma função de orquestrador Espere e escute um evento externo de forma assíncrona. A função de orquestrador de escuta declara o *nome* do evento e a *forma dos dados* que espera receber.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

O exemplo anterior escuta um único evento específico e executa uma ação quando ele é recebido.

Você pode escutar vários eventos simultaneamente, como no exemplo a seguir, que aguarda uma das três notificações de eventos possíveis.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

O exemplo anterior escuta *qualquer um* dos vários eventos. Também é possível aguardar *todos os* eventos.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

No .NET, se a carga do evento não puder ser convertida no tipo esperado `T`, uma exceção será lançada.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

`WaitForExternalEvent` aguarda indefinidamente por alguma entrada.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se e quando um evento chega para essa instância de orquestração, ele é ativado automaticamente e processa imediatamente o evento.

> [!NOTE]
> Se seu aplicativo de funções usar o plano de consumo, não haverá encargos de cobrança enquanto uma função de orquestrador estiver aguardando uma tarefa de `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript), não importa quanto tempo ele espera.

## <a name="send-events"></a>Enviar eventos

O método `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) envia os eventos que o `WaitForExternalEvent` (.net) ou `waitForExternalEvent` (JavaScript) aguarda.  O método `RaiseEventAsync` usa *EventName* e *EVENTDATA* como parâmetros. Os dados do evento devem ser serializáveis em JSON.

Abaixo está um exemplo de função disparada por fila que envia um evento de "aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Internamente, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) enfileira uma mensagem que é selecionada pela função de orquestrador em espera. Se a instância não estiver aguardando o *nome do evento especificado,* a mensagem de evento será adicionada a uma fila na memória. Se a instância de orquestração mais tarde começar a escutar o *nome do evento,* ele verificará a fila em busca de mensagens de evento.

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a *ID de instância*especificada, a mensagem de evento será descartada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar o tratamento de erros](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)