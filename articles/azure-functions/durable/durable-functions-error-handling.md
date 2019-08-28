---
title: Manipulando erros no Durable Functions-Azure
description: Saiba como tratar erros na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 33d1b410119e631e0ccc9941beac1062d4ec30f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087336"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Tratamento de erros em Durable Functions (Azure Functions)

Orquestrações de função duráveis são implementadas no código e podem usar os recursos de tratamento de erros da linguagem de programação. Com isso em mente, realmente não há nenhum novo conceito que você precisa saber sobre a incorporação de tratamento de erros e a compensação em suas orquestrações. No entanto, há alguns comportamentos que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção gerada em uma função de atividade é empacotada de volta para a função de orquestrador e lançada como `FunctionFailedException`um. Você pode escrever código de compensação e tratamento de erros que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador que transfere fundos de uma conta para outra:

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Se a chamada à função **CreditAccount** falhar para a conta de destino, a função de orquestrador compensará isso creditando os fundos de volta para a conta de origem.

## <a name="automatic-retry-on-failure"></a>Nova tentativa automática em caso de falha

Quando você chama funções de atividade ou funções de suborquestração, pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e aguarda 5 segundos entre cada repetição:

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

A `CallActivityWithRetryAsync` API (.net) `callActivityWithRetry` ou (JavaScript) usa um `RetryOptions` parâmetro. As chamadas de suborquestração `CallSubOrchestratorWithRetryAsync` usando a API ( `callSubOrchestratorWithRetry` .net) ou (JavaScript) podem usar essas mesmas políticas de repetição.

Há várias opções para personalizar a política de repetição automática. Entre eles, incluem-se:

* **Número máximo de tentativas**: O número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: A quantidade de tempo de espera antes da primeira tentativa de repetição.
* **Coeficiente de retirada**: O coeficiente usado para determinar a taxa de aumento de retirada. O padrão é 1.
* **Intervalo máximo de repetição**: A quantidade máxima de tempo de espera entre as tentativas de repetição.
* **Tempo limite de repetição**: A quantidade máxima de tempo para passar por novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Identificador**: Um retorno de chamada definido pelo usuário pode ser especificado, o que determina se deve ser feita uma nova tentativa ou não.

## <a name="function-timeouts"></a>Tempos limite da função

Talvez você queira abandonar uma chamada de função dentro de uma função de orquestrador se estiver demorando muito tempo para ser concluída. A maneira correta de fazer isso hoje é criando um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.net) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.net) ou `context.df.Task.any` (JavaScript), como no exemplo a seguir:

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Esse mecanismo não encerra realmente a execução da função de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e passe. Para obter mais informações, consulte [](durable-functions-timers.md#usage-for-timeout) a documentação de temporizadores.

## <a name="unhandled-exceptions"></a>Exceções não processadas

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um `Failed` status.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
