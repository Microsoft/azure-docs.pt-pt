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
ms.openlocfilehash: 5a3cfb78fe97b52abb1406dff64132fc1b3fb985
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933416"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Tratamento de erros em Durable Functions (Azure Functions)

Orquestrações de função duráveis são implementadas no código e podem usar os recursos internos de tratamento de erros da linguagem de programação. Na verdade, não há nenhum novo conceito que você precise aprender para adicionar o tratamento de erros e a compensação às suas orquestrações. No entanto, há alguns comportamentos que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção gerada em uma função de atividade é empacotada de volta para a função de orquestrador e lançada como `FunctionFailedException`um. Você pode escrever código de compensação e tratamento de erros que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador que transfere fundos de uma conta para outra:

### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Prescritiva

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

Se a primeira chamada de função **CreditAccount** falhar, a função de orquestrador compensa creditando os fundos de volta para a conta de origem.

## <a name="automatic-retry-on-failure"></a>Nova tentativa automática em caso de falha

Quando você chama funções de atividade ou funções de suborquestração, pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e aguarda 5 segundos entre cada repetição:

### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C#Prescritiva

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

Há várias opções para personalizar a política de repetição automática:

* **Número máximo de tentativas**: O número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: A quantidade de tempo de espera antes da primeira tentativa de repetição.
* **Coeficiente de retirada**: O coeficiente usado para determinar a taxa de aumento de retirada. O padrão é 1.
* **Intervalo máximo de repetição**: A quantidade máxima de tempo de espera entre as tentativas de repetição.
* **Tempo limite de repetição**: A quantidade máxima de tempo para passar por novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Identificador**: Um retorno de chamada definido pelo usuário pode ser especificado para determinar se uma função deve ser repetida.

## <a name="function-timeouts"></a>Tempos limite da função

Talvez você queira abandonar uma chamada de função dentro de uma função de orquestrador se estiver demorando muito tempo para ser concluída. A maneira correta de fazer isso hoje é criando um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.net) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.net) ou `context.df.Task.any` (JavaScript), como no exemplo a seguir:

### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Prescritiva

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
> Esse mecanismo não encerra realmente a execução da função de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e passe. Para obter mais informações, consulte a documentação de [temporizadores](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Exceções não processadas

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um `Failed` status.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre orquestrações do eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
