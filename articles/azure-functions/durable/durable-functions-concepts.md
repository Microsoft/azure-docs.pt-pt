---
title: Padrões de Durable Functions e conceitos técnicos no Azure Functions
description: Saiba como a extensão de Durable Functions no Azure Functions oferece os benefícios da execução de código com estado na nuvem.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734575"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Padrões de Durable Functions e conceitos técnicos (Azure Functions)

Durable Functions é uma extensão de [Azure Functions](../functions-overview.md) e [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Você pode usar Durable Functions para gravar funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. 

Este artigo fornece informações detalhadas sobre os comportamentos da extensão de Durable Functions para Azure Functions e padrões de implementação comuns. As informações podem ajudá-lo a determinar como usar Durable Functions para ajudar a resolver seus desafios de desenvolvimento.

> [!NOTE]
> Durable Functions é uma extensão avançada para Azure Functions que não é apropriada para todos os aplicativos. Este artigo pressupõe que você tenha uma forte familiaridade com os conceitos em [Azure Functions](../functions-overview.md) e os desafios envolvidos no desenvolvimento de aplicativos sem servidor.

## <a name="patterns"></a>Padrões

Esta seção descreve alguns padrões comuns de aplicativo nos quais Durable Functions pode ser útil.

### <a name="chaining"></a>#1 padrão: Encadeamento de funções

No padrão de encadeamento de funções, uma sequência de funções é executada em uma ordem específica. Nesse padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama do padrão de encadeamento de funções](./media/durable-functions-concepts/function-chaining.png)

Você pode usar Durable Functions para implementar o padrão de encadeamento de funções de forma concisa, conforme mostrado no exemplo a seguir:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Há diferenças sutis entre a gravação de uma função durável pré-compilada no C# e a gravação de uma função durável pré-compilada no C# script. Em uma C# função pré-compilada, os parâmetros duráveis devem ser decorados com os respectivos atributos. Um exemplo é o `[OrchestrationTrigger]` atributo para o `DurableOrchestrationContext` parâmetro. Em uma C# função durável pré-compilada, se os parâmetros não estiverem corretamente decorados, o tempo de execução não poderá injetar as variáveis na função e ocorrerá um erro. Para obter mais exemplos, consulte os [exemplos do Azure-Functions-durável-Extension no GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Neste exemplo, os `F1`valores, `F2`, `F3`e `F4` são os nomes de outras funções no aplicativo de funções. Você pode implementar o fluxo de controle usando construções de codificação imperativas normais. O código é executado de cima para baixo. O código pode envolver a semântica de fluxo de controle de linguagem existente, como condicionais e loops. Você pode incluir a lógica de tratamento `try` de erros em / / `catch` `finally` blocos.

Você pode usar o `context` parâmetro [DurableOrchestrationContext] \(.NET\) e o `context.df` objeto (JavaScript) para invocar outras funções por nome, passar parâmetros e retornar a saída da função. Cada vez que o código `await` chamaC#() `yield` ou (JavaScript), o Durable Functions Framework verifica o progresso da instância de função atual. Se o processo ou a VM for reciclado percorrendo a execução, a instância de função será retomada `yield` da chamada ou anterior `await` . Para obter mais informações, consulte a próxima seção, padrão #2: Fan-out/ventilador no.

> [!NOTE]
> O `context` objeto em JavaScript representa o [contexto de função](../functions-reference-node.md#context-object)inteiro, não apenas o parâmetro [DurableOrchestrationContext] .

### <a name="fan-in-out"></a>#2 padrão: Fan-out/ventilador em

No padrão Fan-out/Fan, execute várias funções em paralelo e aguarde a conclusão de todas as funções. Geralmente, algum trabalho de agregação é feito nos resultados retornados das funções.

![Um diagrama do padrão de Fan-out/Fan](./media/durable-functions-concepts/fan-out-fan-in.png)

Com as funções normais, você pode se espalhar fazendo com que a função envie várias mensagens para uma fila. Fan de volta é muito mais desafiador. Para o Fan-in, em uma função normal, você escreve o código para controlar quando as funções disparadas por fila terminam e, em seguida, armazenam saídas de função. 

A extensão Durable Functions manipula esse padrão com código relativamente simples:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

O trabalho de Fan-out é distribuído para várias instâncias da `F2` função. O trabalho é acompanhado usando uma lista dinâmica de tarefas. A API `Task.WhenAll` do .net ou `context.df.Task.all` a API JavaScript é chamada para aguardar a conclusão de todas as funções chamadas. Em seguida, `F2` as saídas de função são agregadas da lista dinâmica de tarefas e passadas para a `F3` função.

O ponto de verificação automático que ocorre no `await` ou `yield` na chamada `Task.WhenAll` ou `context.df.Task.all` garante que uma possível falha ou reinicialização do Midway não exija a reinicialização de uma tarefa já concluída.

### <a name="async-http"></a>#3 padrão: APIs HTTP assíncronas

O padrão de APIs HTTP assíncronas resolve o problema de coordenar o estado de operações de longa execução com clientes externos. Uma maneira comum de implementar esse padrão é fazer com que uma chamada HTTP Acione a ação de execução longa. Em seguida, redirecione o cliente para um ponto de extremidade de status que o cliente sonda para saber quando a operação foi concluída.

![Um diagrama do padrão de API HTTP](./media/durable-functions-concepts/async-http-api.png)

O Durable Functions fornece APIs internas que simplificam o código que você escreve para interagir com execuções de função de longa execução. Os exemplos de início rápido[C#](durable-functions-create-first-csharp.md) do Durable Functions (e [JavaScript](quickstart-js-vscode.md)) mostram um comando REST simples que você pode usar para iniciar novas instâncias de função de orquestrador. Depois que uma instância é iniciada, a extensão expõe as APIs HTTP do webhook que consultam o status da função de orquestrador. 

O exemplo a seguir mostra os comandos REST que iniciam um orquestrador e consultam seu status. Para maior clareza, alguns detalhes são omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Como o tempo de execução do Durable Functions gerencia o estado, você não precisa implementar seu próprio mecanismo de controle de status.

A extensão de Durable Functions tem WebHooks internos que gerenciam orquestrações de longa execução. Você mesmo pode implementar esse padrão usando seus próprios gatilhos de função (como http, uma fila ou hubs de eventos do Azure) e `orchestrationClient` a associação. Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento. Ou você pode usar um gatilho HTTP que é protegido por uma política de autenticação Azure Active Directory em vez de WebHooks internos que usam uma chave gerada para autenticação.

Aqui estão alguns exemplos de como usar o padrão de API HTTP:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="c-script"></a>Script C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

No .net, o parâmetro [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` é `orchestrationClient` um valor da Associação de saída, que faz parte da extensão de durable functions. No JavaScript, esse objeto é retornado chamando `df.getClient(context)`. Esses objetos fornecem métodos que você pode usar para iniciar, enviar eventos para, encerrar e consultar instâncias de função de orquestrador novas ou existentes.

Nos exemplos anteriores, uma função disparada por http usa um `functionName` valor da URL de entrada e passa o valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). A API de associação [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) retorna uma resposta que contém um `Location` cabeçalho e informações adicionais sobre a instância. Você pode usar as informações posteriormente para pesquisar o status da instância iniciada ou para encerrar a instância.

### <a name="monitoring"></a>#4 padrão: Monitorizar

O padrão de monitor refere-se a um processo recorrente e flexível em um fluxo de trabalho. Um exemplo é sondando até que condições específicas sejam atendidas. Você pode usar um [gatilho de temporizador](../functions-bindings-timer.md) regular para abordar um cenário básico, como um trabalho de limpeza periódico, mas seu intervalo é estático e o gerenciamento de tempos de vida da instância se torna complexo. Você pode usar Durable Functions para criar intervalos de recorrência flexíveis, gerenciar tempos de vida da tarefa e criar vários processos de monitor de uma única orquestração.

Um exemplo do padrão de monitor é reverter o cenário de API HTTP assíncrono anterior. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de execução longa, o monitor de execução longa consome um ponto de extremidade externo e aguarda uma alteração de estado.

![Um diagrama do padrão de monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, você pode usar Durable Functions para criar vários monitores que observam pontos de extremidade arbitrários. Os monitores podem encerrar a execução quando uma condição é atendida ou o [DurableOrchestrationClient](durable-functions-instance-management.md) pode encerrar os monitores. Você pode alterar o intervalo de `wait` um monitor com base em uma condição específica (por exemplo, retirada exponencial). 

O código a seguir implementa um monitor básico:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID de trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável controla o intervalo de sondagem. Em seguida, mais trabalho pode ser executado ou a orquestração pode terminar. Quando o `context.CurrentUtcDateTime` (.net) ou `context.df.currentUtcDateTime` (JavaScript) excede o `expiryTime` valor, o monitor termina.

### <a name="human"></a>#5 padrão: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver seres humanos em um processo automatizado é complicado porque as pessoas não são tão altamente disponíveis e tão responsivas como serviços de nuvem. Um processo automatizado pode permitir isso usando tempos limite e a lógica de compensação.

Um processo de aprovação é um exemplo de um processo de negócios que envolve interação humana. A aprovação de um gerente pode ser necessária para um relatório de despesas que exceda um valor determinado em dólar. Se o gerente não aprovar o relatório de despesas dentro de 72 horas (talvez o gerente tenha entrado em férias), um processo de escalonamento será acionado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Você pode implementar o padrão neste exemplo usando uma função de orquestrador. O orquestrador usa um [temporizador durável](durable-functions-timers.md) para solicitar aprovação. O orquestrador escala se o tempo limite ocorrer. O orquestrador aguarda um [evento externo](durable-functions-external-events.md), como uma notificação gerada por uma interação humana.

Estes exemplos criam um processo de aprovação para demonstrar o padrão de interação humana:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Para criar o temporizador durável, `context.CreateTimer` chame (.net) `context.df.createTimer` ou (JavaScript). A notificação é recebida pelo `context.WaitForExternalEvent` (.net) ou `context.df.waitForExternalEvent` (JavaScript). Em seguida `Task.WhenAny` , (.net) `context.df.Task.any` ou (JavaScript) é chamado para decidir se deve escalonar (o tempo limite ocorre primeiro) ou processar a aprovação (a aprovação é recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos a uma função de orquestrador em espera usando as [APIs http internas](durable-functions-http-api.md#raise-event) ou usando a API [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) de outra função:

#### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>C#Prescritiva

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>Javascript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>#6 padrão: Agregador (visualização)

O sexto padrão é a agregação de dados de evento durante um período de tempo em uma única *entidade*endereçável. Nesse padrão, os dados que estão sendo agregados podem vir de várias fontes, podem ser entregues em lotes ou podem estar espalhados por longos períodos de tempo. O agregador pode precisar executar uma ação nos dados de evento conforme ele chega e os clientes externos talvez precisem consultar os dados agregados.

![Diagrama do agregador](./media/durable-functions-concepts/aggregator.png)

A coisa complicada de tentar implementar esse padrão com as funções normal e sem estado é que o controle de simultaneidade se torna um grande desafio. Você não só precisa se preocupar com vários threads modificando os mesmos dados ao mesmo tempo, você também precisa se preocupar em garantir que o agregador só seja executado em uma única VM por vez.

Usando uma [função de entidade durável](durable-functions-preview.md#entity-functions), é possível implementar esse padrão facilmente como uma única função.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

As entidades duráveis também podem ser modeladas como classes .NET. Isso pode ser útil se a lista de operações se tornar grande e se for principalmente estática. O exemplo a seguir é uma implementação equivalente da `Counter` entidade usando classes e métodos .net.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Os clientes podem enfileirar *as operações* de uma função de entidade (também conhecida como "sinalização") usando a `orchestrationClient` associação.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Os proxies gerados dinamicamente também estão disponíveis para sinalizar entidades de forma segura de tipo. Além de sinalização, os clientes também podem consultar o estado de uma função de entidade usando métodos na `orchestrationClient` associação.

> [!NOTE]
> Atualmente, as funções de entidade estão disponíveis apenas na [visualização Durable Functions 2,0](durable-functions-preview.md).

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão Durable Functions é criada sobre a estrutura de [tarefa durável](https://github.com/Azure/durabletask), uma biblioteca de software livre no GitHub que é usada para criar orquestrações de tarefas duráveis. Como Azure Functions é a evolução sem servidor do Azure WebJobs, Durable Functions é a evolução sem servidor do Framework de tarefa durável. A Microsoft e outras organizações usam o Framework de tarefa durável extensivamente para automatizar processos críticos. É uma opção natural para o ambiente de Azure Functions sem servidor.

### <a name="event-sourcing-checkpointing-and-replay"></a>Fornecimento de eventos, ponto de verificação e repetição

As funções de orquestrador mantêm seu estado de execução de forma confiável usando o padrão de design de [fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Em vez de armazenar diretamente o estado atual de uma orquestração, a extensão de Durable Functions usa um repositório somente de acréscimo para registrar a série completa de ações que a orquestração de função usa. Um armazenamento somente de acréscimo tem muitos benefícios em comparação ao "despejo" do estado de tempo de execução completo. Os benefícios incluem maior desempenho, escalabilidade e capacidade de resposta. Você também obtém consistência eventual para dados transacionais e histórico e trilhas de auditoria completas. As trilhas de auditoria dão suporte a ações de compensação confiáveis.

Durable Functions usa o fornecimento de eventos de forma transparente. Nos bastidores, o `await` operadorC#() `yield` ou (JavaScript) em uma função de orquestrador resulta no controle do thread do Orchestrator de volta para o Dispatcher do Framework de tarefa durável. Em seguida, o Dispatcher confirma as novas ações que a função de orquestrador agendou (como chamar uma ou mais funções filhas ou agendar um temporizador durável) para o armazenamento. A ação de confirmação transparente acrescenta ao histórico de execução da instância de orquestração. O histórico é armazenado em uma tabela de armazenamento. Em seguida, a ação de confirmação adiciona mensagens a uma fila para agendar o trabalho real. Neste ponto, a função de orquestrador pode ser descarregada da memória. 

A cobrança da função de orquestrador será interrompida se você estiver usando o plano de consumo Azure Functions. Quando há mais trabalho a fazer, a função é reiniciada e seu estado é reconstruído.

Quando uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador ativa e executa novamente toda a função do início para recompilar o estado local. 

Durante a repetição, se o código tentar chamar uma função (ou qualquer outro trabalho assíncrono), o Framework de tarefa durável consultará o histórico de execução da orquestração atual. Se descobrir que a [função de atividade](durable-functions-types-features-overview.md#activity-functions) já foi executada e gerou um resultado, ela repetirá o resultado dessa função e o código do orquestrador continuará a ser executado. A repetição continua até que o código da função seja concluído ou até que o novo trabalho assíncrono seja agendado.

### <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de reprodução do código do Orchestrator cria restrições sobre o tipo de código que você pode escrever em uma função de orquestrador. Por exemplo, o código do orquestrador deve ser determinístico porque será reproduzido várias vezes e deve produzir o mesmo resultado a cada vez. Para obter a lista completa de restrições, consulte [restrições de código do Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

A extensão Durable Functions emite automaticamente dados de controle estruturados para [Application insights](../functions-monitoring.md) se você configurar seu aplicativo de funções com uma chave de instrumentação do aplicativo Azure insights. Você pode usar os dados de rastreamento para monitorar as ações e o progresso de suas orquestrações.

Veja um exemplo de como os eventos de rastreamento de Durable Functions se parecem no portal de Application Insights quando você usa a [análise de Application insights](../../application-insights/app-insights-analytics.md):

![Resultados da consulta de Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Você pode encontrar dados estruturados úteis no `customDimensions` campo em cada entrada de log. Veja um exemplo de uma entrada totalmente expandida:

![O campo customDimensions em uma consulta Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Devido ao comportamento de reprodução do Dispatcher da estrutura de tarefas durável, você pode esperar ver entradas de log redundantes para ações reproduzidas. As entradas de log redundantes podem ajudá-lo a entender o comportamento de reprodução do mecanismo principal. O artigo [diagnóstico](durable-functions-diagnostics.md) mostra exemplos de consultas que filtram logs de reprodução, para que você possa ver apenas os logs "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e escalabilidade

A extensão Durable Functions usa filas, tabelas e blobs no armazenamento do Azure para manter o estado do histórico de execução e disparar a execução da função. Você pode usar a conta de armazenamento padrão para o aplicativo de funções ou pode configurar uma conta de armazenamento separada. Talvez você queira uma conta separada com base nos limites de taxa de transferência de armazenamento. O código do orquestrador que você escreve não interage com as entidades dessas contas de armazenamento. A estrutura de tarefa durável gerencia as entidades diretamente como um detalhe de implementação.

As funções de orquestrador agendam funções de atividade e recebem suas respostas por meio de mensagens de fila internas. Quando um aplicativo de funções é executado no plano de consumo de Azure Functions, o [controlador de escala de Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitora essas filas. Novas instâncias de computação são adicionadas conforme necessário. Quando dimensionado para várias VMs, uma função de orquestrador pode ser executada em uma VM, enquanto as funções de atividade que as chamadas de função de orquestrador podem ser executadas em várias VMs diferentes. Para obter mais informações sobre o comportamento de escala de Durable Functions, consulte [desempenho e escala](durable-functions-perf-and-scale.md).

O histórico de execução para contas do Orchestrator é armazenado no armazenamento de tabelas. Sempre que uma instância rehydrates em uma VM específica, o orquestrador busca seu histórico de execução do armazenamento de tabela para que possa recriar seu estado local. Um aspecto conveniente de ter o histórico disponível no armazenamento de tabelas é que você pode usar ferramentas como [Gerenciador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver o histórico de suas orquestrações.

Os blobs de armazenamento são usados principalmente como um mecanismo de leasing para coordenar a expansão das instâncias de orquestração em várias VMs. Os blobs de armazenamento armazenam dados de mensagens grandes que não podem ser armazenadas diretamente em tabelas ou filas.

![Uma captura de tela de Gerenciador de Armazenamento do Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Embora seja fácil e conveniente ver o histórico de execução no armazenamento de tabelas, não faça nenhuma dependência nessa tabela. A tabela pode mudar à medida que a extensão de Durable Functions evolui.

## <a name="known-issues"></a>Problemas conhecidos

Todos os problemas conhecidos devem ser acompanhados na lista de [problemas do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) . Se você tiver um problema e não conseguir encontrar o problema no GitHub, abra um novo problema. Inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre Durable Functions, confira [Durable Functions recursos e tipos de função](durable-functions-types-features-overview.md). 

Para começar:

> [!div class="nextstepaction"]
> [Criar sua primeira função durável](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html