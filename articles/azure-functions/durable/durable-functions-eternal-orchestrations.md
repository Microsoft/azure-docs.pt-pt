---
title: Orquestrações eternas em Funções Duráveis - Azure
description: Aprenda a implementar orquestrações eternas utilizando a extensão das Funções Duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478812"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas em Funções Duráveis (Funções Azure)

*Orquestrações eternas* são funções orquestradoras que nunca terminam. São úteis quando se pretende utilizar [Funções Duráveis](durable-functions-overview.md) para agregadores e qualquer cenário que exija um loop infinito.

## <a name="orchestration-history"></a>História da orquestração

Como explicado no tema histórico da [orquestração,](durable-functions-orchestrations.md#orchestration-history) o Quadro de Tarefas Duráveis acompanha a história de cada orquestração de funções. Esta história cresce continuamente enquanto a função de orquestrador continuar a agendar novos trabalhos. Se a função orquestradora entrar num ciclo infinito e programar continuamente o trabalho, esta história pode crescer criticamente grande e causar problemas de desempenho significativos. O conceito *de orquestração eterna* foi projetado para mitigar este tipo de problemas para aplicações que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Reposição e reinício

Em vez de utilizar loops infinitos, as funções orquestradoras redefiniram o seu estado chamando o `ContinueAsNew` método (.NET) ou `continueAsNew` (JavaScript) da ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger). Este método requer um único parâmetro json-serializável, que se torna a nova entrada para a próxima geração de funções orquestradoras.

Quando `ContinueAsNew` é chamado, a instância enverta uma mensagem para si mesma antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma instância de identificação é mantida, mas o histórico da função orquestradora é efetivamente truncado.

> [!NOTE]
> O Quadro de Tarefas Duráveis mantém a mesma instância de IDENTIFICAção, mas `ContinueAsNew`internamente cria um novo ID de *execução* para a função orquestradora que é reposto por . Este ID de execução geralmente não é exposto externamente, mas pode ser útil saber quando depura a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso para orquestrações eternas é o código que precisa fazer trabalho periódico indefinidamente.

# <a name="c"></a>[C #](#tab/csharp)

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
> O exemplo c# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

A diferença entre este exemplo e uma função acionada pelo temporizador é que os tempos de limpeza aqui não são baseados num horário. Por exemplo, um cron ocalendário que executa uma função a cada hora irá executá-lo às 1:00, 2:00, 3:00 etc. e pode potencialmente encontrar problemas de sobreposição. Neste exemplo, no entanto, se a limpeza demorar 30 minutos, então será agendada às 1:00, 2:30, 4:00, etc. e não há possibilidade de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Iniciando uma orquestração eterna

Utilize `StartNewAsync` o método (.NET) ou (JavaScript) `startNew` para iniciar uma orquestração eterna, tal como faria qualquer outra função de orquestração.  

> [!NOTE]
> Se precisa de garantir que uma orquestração eterna singleton está a `id` decorrer, é importante manter o mesmo exemplo no início da orquestração. Para mais informações, consulte [A Gestão de Instâncias.](durable-functions-instance-management.md)

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Saída de uma orquestração eterna

Se uma função de orquestrador precisar eventualmente de *not* ser `ContinueAsNew` concluída, então tudo o que precisa fazer é não ligar e deixar a função sair.

Se uma função de orquestrador estiver num ciclo `TerminateAsync` infinito e precisar `terminate` de ser interrompida, utilize o método (.NET) ou (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) para o impedir. Para mais informações, consulte [A Gestão de Instâncias.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba implementar orquestrações singleton](durable-functions-singletons.md)
