---
title: Padrões de funções duráveis e conceitos técnicos nas funções do Azure
description: Saiba como a extensão de funções duráveis nas funções do Azure dá-lhe os benefícios da execução de código com monitoração de estado na cloud.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: e5be81efcd655f1f0361d8c00d978a81c3e6caa5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443424"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Padrões de funções duráveis e conceitos técnicos (funções do Azure)

Funções duráveis é uma extensão da [as funções do Azure](../functions-overview.md) e [WebJobs do Azure](../../app-service/web-sites-create-web-jobs.md). Pode utilizar funções duráveis para escrever funções de monitorização de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. 

Isso permite o artigo ainda informações detalhadas sobre os comportamentos da extensão de funções duráveis para as funções do Azure e os padrões de implementação comuns. As informações podem ajudar a determinar como utilizar funções duráveis para ajudar a resolver seus desafios de desenvolvimento.

> [!NOTE]
> Funções duráveis é uma extensão avançada para funções do Azure que não é adequado para todos os aplicativos. Este artigo pressupõe que tenha familiaridade com os conceitos em forte [as funções do Azure](../functions-overview.md) e os desafios envolvidos no desenvolvimento de aplicações sem servidor.

## <a name="patterns"></a>Padrões

Esta secção descreve alguns padrões comuns de aplicação em que as funções durável pode ser útil.

### <a name="chaining"></a>Padrão #1: Encadeamento de funções

A função de encadeamento padrão, executa uma sequência de funções numa ordem específica. Neste padrão, a saída de uma função é aplicada a entrada de outra função.

![Um diagrama da função encadeamento padrão](./media/durable-functions-concepts/function-chaining.png)

Pode utilizar funções duráveis para implementar a função encadeamento padrão de forma concisa, conforme mostrado no exemplo a seguir:

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
> Existem diferenças sutis entre escrever uma função durável pré-compilados em C# e escrever uma função durável pré-compilados no C# script que é mostrado no exemplo. Num C# pré-compiladas função, parâmetros duráveis devem ser decorados com os respetivos atributos. Um exemplo é o `[OrchestrationTrigger]` atributo para o `DurableOrchestrationContext` parâmetro. Num C# pré-compiladas função durável, se os parâmetros não estão decorados corretamente, o tempo de execução não é possível inserir as variáveis para a função e ocorre um erro. Para obter mais exemplos, consulte a [azure-functions-durable-extension exemplos no GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Neste exemplo, os valores `F1`, `F2`, `F3`, e `F4` são os nomes de outras funções na aplicação de função. Pode implementar o fluxo de controlo através da utilização normal imperativo construções de codificação. Código é executado de cima para baixo. O código pode envolver existente semântica de fluxo de controle idioma, como condicionais e loops. Pode incluir a lógica de processamento de erros `try` / `catch` / `finally` blocos.

Pode utilizar o `context` parâmetro [DurableOrchestrationContext] \(.NET\) e a `context.df` objeto (JavaScript) para invocar a outras funções por nome, transmita os parâmetros e retornar a função saída. Sempre que o código chama `await` (C#) ou `yield` (JavaScript), os pontos de verificação de estrutura de funções duráveis o progresso da instância de função atual. Se o processo ou VM recicla midway através da execução, a instância de função sai do modo de precedente `await` ou `yield` chamar. Para obter mais informações, consulte a secção seguinte, o padrão de n. º 2: Ventoinha out/fan-in.

> [!NOTE]
> O `context` objeto em JavaScript representa a toda [contexto de função](../functions-reference-node.md#context-object), não apenas a [DurableOrchestrationContext] parâmetro.

### <a name="fan-in-out"></a>Padrão #2: Ventoinha out/fan-in

A ventoinha out/fan-in padrão, executar várias funções em paralelo e, em seguida, aguarde que todas as funções concluir. Muitas vezes, algum trabalho de agregação é feito nos resultados devolvidos pelas funções.

![Um diagrama da ventoinha out/ventoinha padrão](./media/durable-functions-concepts/fan-out-fan-in.png)

Com as funções normais, pode fã fazendo com que a função de enviar várias mensagens para uma fila. Fanning em é muito mais desafiador. Para a ventoinha, numa função normal, escreve código para controlar quando o final de funções de acionada por fila e store, em seguida, saídas de função. 

A extensão de funções duráveis lida com esse padrão com o código relativamente simples:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

O trabalho de fan-Out é distribuído para várias instâncias do `F2` função. O trabalho é controlado com uma lista dinâmica de tarefas. O .NET `Task.WhenAll` API ou JavaScript `context.df.Task.all` API é chamada, aguardar que todas as funções de chamadas concluir. Em seguida, o `F2` saídas de função são agregados na lista de tarefas dinâmico e passado para o `F3` função.

O ponto de verificação automática que ocorre à `await` ou `yield` chamar `Task.WhenAll` ou `context.df.Task.all` garante que uma potencial falha de midway ou um reinício não requerem o reinício de uma tarefa já concluída.

### <a name="async-http"></a>Padrão #3: APIs de HTTP assíncrono

O padrão de HTTP APIs de assíncrono resolve o problema de coordenar o estado das operações de execução longa com clientes externos. Uma forma comum de implementar este padrão é ter um chamar a ação de execução longa de Acionador HTTP. Em seguida, redirecione o cliente para um ponto de extremidade de estado que o cliente consulta para saber quando a operação é concluída.

![Um diagrama do padrão de API de HTTP](./media/durable-functions-concepts/async-http-api.png)

Funções duráveis fornece APIs internas que simplificam o código de escrita para interagir com execuções de função de execução longa. Os exemplos de início rápido de funções durável ([ C# ](durable-functions-create-first-csharp.md) e [JavaScript](quickstart-js-vscode.md)) Mostrar um comando REST simples que pode utilizar para iniciar novas instâncias de função de orquestrador. Depois de uma instância for iniciada, a extensão expõe webhook APIs HTTP que consultar o estado de função do orchestrator. 

O exemplo seguinte mostra os comandos REST que iniciam um orquestrador e consultar o seu estado. Para maior clareza, alguns detalhes são omitidos do exemplo.

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

Uma vez que o tempo de execução de funções duráveis gere o estado, não precisa de implementar seu próprio mecanismo de controlo de estado.

A extensão de funções duráveis tem webhooks incorporados que gerir orquestrações de execução longa. Pode implementar este padrão com o seu próprio acionadores de função (por exemplo, HTTP, uma fila ou os Hubs de eventos do Azure) e o `orchestrationClient` enlace. Por exemplo, poderá utilizar uma mensagem de fila para acionar a terminação. Em alternativa, pode utilizar um acionador HTTP que está protegido por uma política de autenticação do Azure Active Directory em vez dos webhooks incorporadas que utilizam uma chave gerada para a autenticação.

Aqui estão alguns exemplos de como usar o padrão de API de HTTP:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

> [!WARNING]
> Quando desenvolver localmente no JavaScript, usar métodos no `DurableOrchestrationClient`, tem de definir a variável de ambiente `WEBSITE_HOSTNAME` ao `localhost:<port>` (por exemplo, `localhost:7071`). Para obter mais informações sobre este requisito, consulte [problema do GitHub 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

No .NET, o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parâmetro é um valor entre o `orchestrationClient` de saída de ligação, que faz parte da extensão de funções duráveis. No JavaScript, este objecto é devolvido ao chamar `df.getClient(context)`. Esses objetos fornecem métodos que pode utilizar para iniciar, enviar eventos para, encerrar e consultar para instâncias de função de orquestrador de novo ou existente.

Nos exemplos anteriores, uma função acionada por HTTP aceita uma `functionName` valor a partir do URL de entrada e transmite o valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). O [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) API de ligação, em seguida, devolve uma resposta que contém um `Location` cabeçalho e informações adicionais sobre a instância. Pode utilizar as informações mais tarde para pesquisar o estado da instância iniciada ou para encerrar a instância.

### <a name="monitoring"></a>Padrão #4: Monitorizar

O padrão de monitor se refere a um processo de flexível e recorrente num fluxo de trabalho. Um exemplo está a consultar até que forem cumpridas condições específicas. Pode usar um regular [acionador de temporizador](../functions-bindings-timer.md) para resolver um basic cenário, como uma tarefa de limpeza periódica, mas o respetivo intervalo é estático e durações de instância de gestão torna-se complexo. Pode utilizar funções duráveis para criar intervalos de periodicidade flexível, gerir durações de tarefas e criar o monitor de vários processos de uma única orquestração.

É um exemplo do padrão de monitor para reverter o cenário de API de HTTP de async anterior. Em vez de expor um ponto final para um cliente externo monitorizar uma operação de longa execução, o monitor de execução longa consome um ponto final externo e, em seguida, aguarda que uma alteração de estado.

![Um diagrama do padrão de monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, pode usar funções duráveis para criar vários monitores que cumprem os pontos de extremidade arbitrários. Os monitores podem terminar a execução, quando for cumprida uma condição, ou o [DurableOrchestrationClient](durable-functions-instance-management.md) pode encerrar os monitores. Pode alterar um monitor `wait` intervalo com base numa condição específica (por exemplo, término exponencial.) 

O seguinte código implementa um monitor básico:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Quando é recebido um pedido, é criada uma nova instância de orquestração para esse ID de tarefa. A instância de consulta um estado até que a condição é cumprida e o loop é encerrado. Um temporizador durável controla o intervalo de consulta. Em seguida, podem ser executada mais de trabalho ou a orquestração pode terminar. Quando o `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) excede o `expiryTime` valor, as extremidades do monitor.

### <a name="human"></a>Padrão #5: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Que envolvem seres humanos num processo automatizado é difícil, porque as pessoas não são como estando a responder como serviços em nuvem e de como sendo de elevada disponibilidade. Um processo automatizado pode permitir isso usando uma lógica de tempos limite e de compensação.

Um processo de aprovação é um exemplo de um processo comercial que envolve a interação humana. Aprovação de um Gerenciador poderá ser necessária para um relatório de despesas que exceda uma determinada quantidade de dólar. Se o gestor não aprovar o relatório de despesas dentro de 72 horas (talvez o Gestor de saiu férias), um processo de escalonamento entra em ação para obter a aprovação de outra pessoa (talvez do gestor).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Pode implementar o padrão neste exemplo, utilizando uma função de orquestrador. O orchestrator utiliza um [temporizador durável](durable-functions-timers.md) para aprovação de pedidos. O orquestrador de escala se ocorrer de tempo limite. O orchestrator aguarda uma [evento externo](durable-functions-external-events.md), por exemplo, uma notificação que é gerada por uma interação humana.

Estes exemplos, crie um processo de aprovação para demonstrar o padrão de interação humana:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Para criar o temporizador durável, chame `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). A notificação é recebida pelo `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Em seguida, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) é chamado para decidir se deve escalar (tempo limite acontece pela primeira vez) ou processar a aprovação (a aprovação é recebida antes do tempo limite).

Um cliente externo pode fornecer a notificação de evento para uma função de orquestrador de espera utilizando o [APIs internas de HTTP](durable-functions-http-api.md#raise-event) ou utilizando o [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API a partir outra função:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão de funções duráveis baseia-se do [durável de tarefa de Framework](https://github.com/Azure/durabletask), uma biblioteca de código-fonte aberto no GitHub, que é utilizada para criar orquestrações de tarefa durável. Como as funções do Azure é a evolução sem servidor do Azure WebJobs, funções duráveis é a evolução sem servidor da estrutura de tarefa durável. A Microsoft e outras organizações usam extensivamente a estrutura de tarefa durável para automatizar os processos de missão crítica. É uma opção natural para o ambiente de funções do Azure sem servidor.

### <a name="event-sourcing-checkpointing-and-replay"></a>Origem do evento, pontos de verificação e repetição

As funções do Orchestrator fiável mantêm o respetivo estado de execução através de [origem do evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) padrão de design. Em vez de armazenar diretamente o estado atual de uma orquestração, a extensão de funções duráveis utiliza um arquivo só de acréscimo para registar a série completa das ações que demora a orquestração de função. Um arquivo só de acréscimo tem muitas vantagens em comparação com o estado do tempo de execução completa de "despejo". Os benefícios incluem melhor desempenho, escalabilidade e capacidade de resposta. Também obtém a consistência eventual para dados transacionais e registos de auditoria completos e histórico. Os registos de auditoria suportam ações de compensação fiáveis.

Funções duráveis utiliza de forma transparente a origem do evento. Em segundo plano, o `await` (C#) ou `yield` operador (JavaScript) numa função de orquestrador produz o controle do orchestrator thread para o dispatcher do Framework de tarefa durável. O dispatcher, em seguida, consolida todas as novas ações que a função de orquestrador agendada (por exemplo, chamar uma ou mais funções de subordinado ou agendamento de um temporizador durável) para o armazenamento. A ação de consolidação transparente acrescenta o histórico de execução da instância de orquestração. O histórico é armazenado numa tabela de armazenamento. A ação de consolidação, em seguida, adiciona uma mensagem numa fila para agendar o trabalho real. Neste momento, a função de orquestrador pode ser baixada da memória. 

A faturação para a função de orquestrador deixa de se estiver a utilizar o plano de consumo das funções do Azure. Quando há mais de trabalho, os reinícios de função, e seu estado é reconstruído.

Quando uma função de orquestração recebe mais de trabalho (por exemplo, é recebida uma mensagem de resposta ou expira um temporizador durável), o orchestrator reativado e executa novamente a função completa desde o início para reconstruir o estado local. 

Durante a reprodução, se o código tenta chamar uma função (ou fazer qualquer outra async trabalho), a estrutura de tarefa durável atua o histórico de execução da orquestração atual. Se achar que o [função de atividade](durable-functions-types-features-overview.md#activity-functions) já executada e gerou um resultado, ele replays resultado essa função e o código do orchestrator continua a ser executado. Repetição continua até que o código de função seja concluído ou até que está agendado novo trabalho de assíncrono.

### <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de repetição de código do orchestrator cria restrições no tipo de código que pode escrever numa função de orquestrador. Por exemplo, código do orchestrator tem de ser determinístico uma vez que vai ser repetida várias vezes e deve produzir o mesmo resultado cada vez. Para obter a lista completa de restrições, consulte [restrições de código do Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

A extensão de funções duráveis emite automaticamente dados de controlo estruturados para [Application Insights](../functions-monitoring.md) se configurou a sua aplicação de função com uma chave de instrumentação do Application Insights do Azure. Pode utilizar os dados de controle para monitorizar as ações e o progresso das suas orquestrações.

Eis um exemplo de funções duráveis, acompanhar eventos como ser no portal do Application Insights ao usar [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Resultados de consulta do Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Pode encontrar dados estruturados úteis na `customDimensions` campo em cada entrada de log. Eis um exemplo de uma entrada que é totalmente expandida:

![O campo de customDimensions numa consulta do Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Devido ao comportamento de repetição do dispatcher durável Framework de tarefas, pode esperar ver as entradas de registo com redundância de ações repetidas. Entradas de registo redundantes podem ajudar a compreender o comportamento de repetição do mecanismo central. O [diagnóstico](durable-functions-diagnostics.md) artigo mostra-lhe consultas de exemplo que filtram os registos de repetição, para que possa ver apenas os registos de "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e escalabilidade

A extensão de funções duráveis utiliza as filas, tabelas e blobs no armazenamento do Azure para manter a execução histórico acionador e estado de execução de função. Pode utilizar a conta de armazenamento predefinida para a aplicação de funções, ou pode configurar uma conta de armazenamento separada. Pode desejar uma conta separada com base nos limites de débito de armazenamento. O código de orchestrator escrita não interage com as entidades nestas contas de armazenamento. A estrutura de tarefa durável gerencia as entidades diretamente como um detalhe de implementação.

As funções do Orchestrator agendar as funções de atividade e recebem suas respostas através de mensagens da fila interna. Quando uma aplicação de funções executada no plano de consumo das funções do Azure, o [controlador de dimensionamento de funções do Azure](../functions-scale.md#how-the-consumption-plan-works) monitoriza estas filas. Novas instâncias de computação são adicionadas conforme necessário. Ao aumentar horizontalmente para várias VMs, pode executar uma função de orquestrador numa VM, enquanto as funções de atividade que as chamadas de função de orquestrador possam ser executadas em várias VMs diferentes. Para obter mais informações sobre o comportamento de dimensionamento de funções durável, consulte [desempenho e dimensionamento](durable-functions-perf-and-scale.md).

O histórico de execução para contas do orchestrator é armazenado no armazenamento de tabelas. Sempre que uma instância rehydrates numa VM específica, o orchestrator obtém o histórico de execução do armazenamento de tabela para que ele pode recriar seu estado local. Um aspecto conveniente de ter o histórico disponível no armazenamento de tabelas é que pode usar ferramentas como o [Explorador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver o histórico de suas orquestrações.

Os blobs de armazenamento são principalmente usados como um mecanismo de locação para coordenar o Escalamento horizontal de instâncias de orquestração em várias VMs. Os blobs de armazenamento contêm dados de mensagens grandes que não podem ser armazenados diretamente em tabelas ou filas.

![Uma captura de ecrã do Explorador de armazenamento do Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Embora seja fácil e conveniente para ver o histórico de execuções no armazenamento de tabelas, não faça quaisquer dependências nesta tabela. A tabela poderá ser alterado à medida que a extensão de funções duráveis evolui.

## <a name="known-issues"></a>Problemas conhecidos

Devem haver um controle em todos os problemas conhecidos a [problemas do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) lista. Se se deparar com um problema e não é possível localizar o problema no GitHub, abra um novo problema. Inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as funções durável, veja [tipos e recursos de função de funções duráveis](durable-functions-types-features-overview.md). 

Para começar:

> [!div class="nextstepaction"]
> [Criar a sua primeira função durável](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
