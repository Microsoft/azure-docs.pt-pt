---
title: Orquestrações eternas em Funções Duradouras - Azure
description: Aprenda a implementar orquestrações eternas utilizando a extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 34c70f4305ebb2c45757d982ab558aea6450003f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506371"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas em Funções Duradouras (Funções Azure)

*Orquestrações eternas* são funções orquestradoras que nunca acabam. São úteis quando se pretende utilizar [Funções Duráveis](durable-functions-overview.md) para agregadores e qualquer cenário que exija um loop infinito.

## <a name="orchestration-history"></a>História da orquestração

Como explicado no tema da história da [orquestração,](durable-functions-orchestrations.md#orchestration-history) o Quadro de Tarefas Duráveis acompanha a história de cada orquestração de funções. Esta história cresce continuamente enquanto a função orquestradora continuar a agendar novos trabalhos. Se a função orquestradora entrar num ciclo infinito e os horários continuarem a funcionar, esta história pode crescer criticamente grande e causar problemas significativos de desempenho. O conceito *de orquestração eterna* foi projetado para mitigar este tipo de problemas para aplicações que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Reposição e reinício

Em vez de utilizar laços infinitos, as funções do orquestrador reiniciam o seu estado chamando o `ContinueAsNew` `continueAsNew` método (.NET), (JavaScript) ou `continue_as_new` (Python) da [ligação](durable-functions-bindings.md#orchestration-trigger)do gatilho de orquestração . Este método requer um único parâmetro json-serializável, que se torna a nova entrada para a próxima geração de funções orquestradoras.

Quando `ContinueAsNew` é chamado, o caso encova uma mensagem para si mesmo antes de sair. A mensagem reinicia a instância com o novo valor de entrada. O mesmo caso de identificação é mantido, mas a história da função do orquestrador é efetivamente truncada.

> [!NOTE]
> O Quadro de Tarefas Durável mantém o mesmo ID de instância, mas cria internamente um novo *ID de execução* para a função orquestradora que é reiniciada por `ContinueAsNew` . Esta identificação de execução geralmente não é exposta externamente, mas pode ser útil saber sobre quando depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso para orquestrações eternas é o código que precisa fazer o trabalho periódico indefinidamente.

# <a name="c"></a>[C#](#tab/csharp)

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
> O exemplo C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

A diferença entre este exemplo e uma função desencadeada pelo temporizador é que os tempos de disparo de limpeza aqui não são baseados num horário. Por exemplo, um cron que executa uma função a cada hora irá executá-la às 1:00, 2:00, 3:00 etc. e pode potencialmente encontrar problemas de sobreposição. Neste exemplo, no entanto, se a limpeza demorar 30 minutos, então será agendado para 1:00, 2:30, 4:00, etc. e não há possibilidade de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Iniciar uma orquestração eterna

Utilize o `StartNewAsync` método (.NET), o `startNew` método (JavaScript), `start_new` (Python) para iniciar uma orquestração eterna, tal como faria com qualquer outra função de orquestração.  

> [!NOTE]
> Se precisa garantir que uma orquestração eterna singleton está a decorrer, é importante manter o mesmo caso `id` ao iniciar a orquestração. Para mais informações, consulte [Gestão de Exemplos.](durable-functions-instance-management.md)

# <a name="c"></a>[C#](#tab/csharp)

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
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

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
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>Saída de uma orquestração eterna

Se uma função de orquestrador tiver eventualmente de ser completada, então tudo o que precisa fazer é *não* ligar `ContinueAsNew` e deixar a função sair.

Se uma função orquestradora estiver num loop infinito e precisar de ser interrompida, utilize o `TerminateAsync` método (.NET), `terminate` (JavaScript) ou `terminate` (Python) da ligação do cliente da [orquestração](durable-functions-bindings.md#orchestration-client) para o impedir. Para mais informações, consulte [Gestão de Exemplos.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
