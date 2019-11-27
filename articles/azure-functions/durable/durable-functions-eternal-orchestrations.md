---
title: Orquestrações de eternas no Durable Functions-Azure
description: Saiba como implementar orquestrações eternas usando a extensão Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 8d28ae18c44c434dba053b23a60eb78728f8d8e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232916"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternass no Durable Functions (Azure Functions)

As *orquestrações de eternas* são funções de orquestrador que nunca terminam. Eles são úteis quando você deseja usar [Durable Functions](durable-functions-overview.md) para agregadores e qualquer cenário que exija um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Conforme explicado no tópico do [histórico de orquestração](durable-functions-orchestrations.md#orchestration-history) , o Framework de tarefa durável controla o histórico de cada orquestração de função. Esse histórico cresce continuamente, desde que a função de orquestrador continue a agendar um novo trabalho. Se a função de orquestrador entrar em um loop infinito e agendar o trabalho continuamente, esse histórico poderá aumentar de forma muito grande e causar problemas de desempenho significativos. O conceito de *orquestração eternas* foi projetado para atenuar esses tipos de problemas em aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Redefinindo e reiniciando

Em vez de usar loops infinitos, as funções de orquestrador redefinem seu estado chamando o método `ContinueAsNew` (.NET) ou `continueAsNew` (JavaScript) da [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). Esse método usa um único parâmetro serializável em JSON, que se torna a nova entrada para a próxima geração de função de orquestrador.

Quando `ContinueAsNew` é chamado, a instância coloca uma mensagem em fila antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma ID de instância é mantida, mas o histórico da função de orquestrador é efetivamente truncado.

> [!NOTE]
> O Framework de tarefa durável mantém a mesma ID de instância, mas cria internamente uma nova *ID de execução* para a função de orquestrador que é redefinida por `ContinueAsNew`. Essa ID de execução geralmente não é exposta externamente, mas pode ser útil saber ao depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso para orquestrações de eternas é o código que precisa fazer trabalho periódico indefinidamente.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> O exemplo C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

A diferença entre este exemplo e uma função disparada por temporizador é que os tempos de gatilho de limpeza aqui não se baseiam em uma agenda. Por exemplo, uma agenda CRON que executa uma função a cada hora a executará em 1:00, 2:00, 3:00 etc. e pode potencialmente causar problemas de sobreposição. Neste exemplo, no entanto, se a limpeza levar 30 minutos, ela será agendada às 1:00, 2:30, 4:00, etc. e não há nenhuma chance de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Iniciando uma orquestração de eternas

Use o método `StartNewAsync` (.NET) ou `startNew` (JavaScript) para iniciar uma orquestração eternas, assim como faria com qualquer outra função de orquestração.  

> [!NOTE]
> Se você precisar garantir que uma orquestração singleton eternas esteja em execução, é importante manter a mesma instância `id` ao iniciar a orquestração. Para obter mais informações, consulte [Gerenciamento de instâncias](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eternas

Se uma função de orquestrador precisar ser concluída eventualmente, tudo o que você precisa fazer *não* será chamar `ContinueAsNew` e deixar a função sair.

Se uma função de orquestrador estiver em um loop infinito e precisar ser interrompida, use o método `TerminateAsync` (.NET) ou `terminate` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para interrompê-la. Para obter mais informações, consulte [Gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
