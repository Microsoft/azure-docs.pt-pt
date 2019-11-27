---
title: Visão geral de Durable Functions-Azure
description: Introdução à extensão de Durable Functions para Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 8b31a5ab716b58d167a0d16579b44aa7df95a0ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232832"
---
# <a name="what-are-durable-functions"></a>O que é a Durable Functions?

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) que permite que você escreva funções com estado em um ambiente de computação sem servidor. A extensão permite definir fluxos de trabalho com estado escrevendo [*funções de orquestrador*](durable-functions-orchestrations.md) e entidades com estado escrevendo [*funções de entidade*](durable-functions-entities.md) usando o modelo de programação Azure functions. Nos bastidores, a extensão gerencia o estado, pontos de verificação e reinicializações para você, permitindo que você se concentre em sua lógica de negócios.

## <a name="language-support"></a>Idiomas com suporte

O Durable Functions atualmente dá suporte aos seguintes idiomas:

* **C#** : as [bibliotecas de classes pré-compiladas](../functions-dotnet-class-library.md) e [ C# o script](../functions-reference-csharp.md).
* **F#** : script e bibliotecas de F# classes pré-compiladas. F#o script só tem suporte para a versão 1. x do tempo de execução de Azure Functions.
* **JavaScript**: com suporte apenas para a versão 2. x do tempo de execução de Azure functions. Requer a versão 1.7.0 da extensão de Durable Functions ou uma versão posterior. 

Durable Functions tem o objetivo de dar suporte a todas as [linguagens de Azure Functions](../supported-languages.md). Consulte a [lista de problemas de durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) para obter o status do trabalho mais recente para dar suporte a idiomas adicionais.

Assim como Azure Functions, há modelos para ajudá-lo a desenvolver Durable Functions usando o [Visual Studio 2019](durable-functions-create-first-csharp.md), o [Visual Studio Code](quickstart-js-vscode.md)e o [portal do Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Padrões de aplicação

O caso de uso primário para o Durable Functions é simplificar os requisitos complexos de coordenação com estado em aplicativos sem servidor. As seções a seguir descrevem os padrões de aplicativo típicos que podem se beneficiar do Durable Functions:

* [Encadeamento de funções](#chaining)
* [Fan-out/Fan-in](#fan-in-out)
* [APIs HTTP assíncronas](#async-http)
* [Monitorização](#monitoring)
* [Interação humana](#human)
* [Agregador](#aggregator)

### <a name="chaining"></a>#1 padrão: encadeamento de funções

No padrão de encadeamento de funções, uma sequência de funções é executada em uma ordem específica. Nesse padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama do padrão de encadeamento de funções](./media/durable-functions-concepts/function-chaining.png)

Você pode usar Durable Functions para implementar o padrão de encadeamento de funções de forma concisa, conforme mostrado no exemplo a seguir:

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
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

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

Neste exemplo, os valores `F1`, `F2`, `F3`e `F4` são os nomes de outras funções no aplicativo de funções. Você pode implementar o fluxo de controle usando construções de codificação imperativas normais. O código é executado de cima para baixo. O código pode envolver a semântica de fluxo de controle de linguagem existente, como condicionais e loops. Você pode incluir a lógica de tratamento de erros no `try`/`catch`/blocos de `finally`.

Você pode usar o parâmetro `context` [IDurableOrchestrationContext] \(.NET\) e o objeto `context.df` (JavaScript) para invocar outras funções por nome, passar parâmetros e retornar a saída da função. Cada vez que o código chama `await`C#() ou `yield` (JavaScript), o Durable Functions Framework verifica o progresso da instância de função atual. Se o processo ou a VM for reciclado percorrendo a execução, a instância de função será retomada da chamada anterior `await` ou `yield`. Para obter mais informações, consulte a próxima seção, padrão #2: Fan-out/Fan in.

> [!NOTE]
> O objeto `context` em JavaScript representa o [contexto de função](../functions-reference-node.md#context-object)inteiro, não apenas o parâmetro [IDurableOrchestrationContext].

### <a name="fan-in-out"></a>#2 padrão: Fan-out/Fan in

No padrão Fan-out/Fan, execute várias funções em paralelo e aguarde a conclusão de todas as funções. Geralmente, algum trabalho de agregação é feito nos resultados retornados das funções.

![Um diagrama do padrão de Fan-out/Fan](./media/durable-functions-concepts/fan-out-fan-in.png)

Com as funções normais, você pode se espalhar fazendo com que a função envie várias mensagens para uma fila. Fan de volta é muito mais desafiador. Para o Fan-in, em uma função normal, você escreve o código para controlar quando as funções disparadas por fila terminam e, em seguida, armazenam saídas de função.

A extensão Durable Functions manipula esse padrão com código relativamente simples:

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
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

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

O trabalho de Fan-out é distribuído para várias instâncias da função `F2`. O trabalho é acompanhado usando uma lista dinâmica de tarefas. A API .NET `Task.WhenAll` ou a API de `context.df.Task.all` JavaScript é chamada, para aguardar a conclusão de todas as funções chamadas. Em seguida, as saídas da função `F2` são agregadas da lista de tarefas dinâmicas e passadas para a função `F3`.

O ponto de verificação automático que ocorre na chamada de `await` ou `yield` em `Task.WhenAll` ou `context.df.Task.all` garante que uma falha ou reinicialização do Midway em potencial não exija a inicialização de uma tarefa já concluída.

> [!NOTE]
> Em raras circunstâncias, é possível que uma falha ocorra na janela depois que uma função de atividade for concluída, mas antes de sua conclusão ser salva no histórico de orquestração. Se isso acontecer, a função de atividade seria executada novamente desde o início depois que o processo for recuperado.

### <a name="async-http"></a>#3 padrão: APIs HTTP assíncronas

O padrão de API HTTP assíncrona resolve o problema de coordenar o estado de operações de longa execução com clientes externos. Uma maneira comum de implementar esse padrão é fazer com que um ponto de extremidade HTTP dispare a ação de execução longa. Em seguida, redirecione o cliente para um ponto de extremidade de status que o cliente sonda para saber quando a operação foi concluída.

![Um diagrama do padrão de API HTTP](./media/durable-functions-concepts/async-http-api.png)

O Durable Functions fornece **suporte interno** para esse padrão, simplificando ou até mesmo removendo o código que você precisa escrever para interagir com execuções de função de execução longa. Por exemplo, os exemplos de início rápido[C#](durable-functions-create-first-csharp.md) do Durable Functions (e [JavaScript](quickstart-js-vscode.md)) mostram um comando REST simples que você pode usar para iniciar novas instâncias de função de orquestrador. Depois que uma instância é iniciada, a extensão expõe as APIs HTTP do webhook que consultam o status da função de orquestrador. 

O exemplo a seguir mostra os comandos REST que iniciam um orquestrador e consultam seu status. Para maior clareza, alguns detalhes de protocolo são omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Como o tempo de execução do Durable Functions gerencia o estado para você, você não precisa implementar seu próprio mecanismo de controle de status.

A extensão Durable Functions expõe as APIs HTTP internas que gerenciam orquestrações de longa execução. Como alternativa, você pode implementar esse padrão usando seus próprios gatilhos de função (como HTTP, uma fila ou hubs de eventos do Azure) e a [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento. Ou você pode usar um gatilho HTTP que é protegido por uma política de autenticação Azure Active Directory em vez das APIs HTTP internas que usam uma chave gerada para autenticação.

Para obter mais informações, consulte o artigo [recursos http](durable-functions-http-features.md) , que explica como você pode expor processos assíncronos de execução longa via http usando a extensão Durable functions.

### <a name="monitoring"></a>#4 padrão: monitor

O padrão de monitor refere-se a um processo recorrente e flexível em um fluxo de trabalho. Um exemplo é sondando até que condições específicas sejam atendidas. Você pode usar um [gatilho de temporizador](../functions-bindings-timer.md) regular para abordar um cenário básico, como um trabalho de limpeza periódico, mas seu intervalo é estático e o gerenciamento de tempos de vida da instância se torna complexo. Você pode usar Durable Functions para criar intervalos de recorrência flexíveis, gerenciar tempos de vida da tarefa e criar vários processos de monitor de uma única orquestração.

Um exemplo do padrão de monitor é reverter o cenário de API HTTP assíncrono anterior. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de execução longa, o monitor de execução longa consome um ponto de extremidade externo e aguarda uma alteração de estado.

![Um diagrama do padrão de monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, você pode usar Durable Functions para criar vários monitores que observam pontos de extremidade arbitrários. Os monitores podem encerrar a execução quando uma condição é atendida ou o `IDurableOrchestrationClient` pode encerrar os monitores. Você pode alterar o intervalo de `wait` de um monitor com base em uma condição específica (por exemplo, retirada exponencial). 

O código a seguir implementa um monitor básico:

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID de trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável controla o intervalo de sondagem. Em seguida, mais trabalho pode ser executado ou a orquestração pode terminar. Quando o `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) excede o valor de `expiryTime`, o monitor termina.

### <a name="human"></a>#5 padrão: interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver seres humanos em um processo automatizado é complicado porque as pessoas não são tão altamente disponíveis e tão responsivas como serviços de nuvem. Um processo automatizado pode permitir essa interação usando tempos limite e lógica de compensação.

Um processo de aprovação é um exemplo de um processo de negócios que envolve interação humana. A aprovação de um gerente pode ser necessária para um relatório de despesas que exceda um valor determinado em dólar. Se o gerente não aprovar o relatório de despesas dentro de 72 horas (talvez o gerente tenha entrado em férias), um processo de escalonamento será acionado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Você pode implementar o padrão neste exemplo usando uma função de orquestrador. O orquestrador usa um [temporizador durável](durable-functions-timers.md) para solicitar aprovação. O orquestrador escala se o tempo limite ocorrer. O orquestrador aguarda um [evento externo](durable-functions-external-events.md), como uma notificação gerada por uma interação humana.

Estes exemplos criam um processo de aprovação para demonstrar o padrão de interação humana:

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
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
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

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

Para criar o temporizador durável, chame `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). A notificação é recebida por `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Em seguida, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) é chamado para decidir se deve ser escalonado (o tempo limite ocorre primeiro) ou processar a aprovação (a aprovação é recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos a uma função de orquestrador em espera usando as [APIs http internas](durable-functions-http-api.md#raise-event) ou usando o método `RaiseEventAsync` (.net) ou `raiseEvent` (JavaScript) de outra função:

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
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

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>#6 padrão: agregador

O sexto padrão é a agregação de dados de evento durante um período de tempo em uma única *entidade*endereçável. Nesse padrão, os dados que estão sendo agregados podem vir de várias fontes, podem ser entregues em lotes ou podem estar espalhados por longos períodos de tempo. O agregador pode precisar executar uma ação nos dados de evento conforme ele chega e os clientes externos talvez precisem consultar os dados agregados.

![Diagrama do agregador](./media/durable-functions-concepts/aggregator.png)

A coisa complicada de tentar implementar esse padrão com as funções normal e sem estado é que o controle de simultaneidade se torna um grande desafio. Você não só precisa se preocupar com vários threads modificando os mesmos dados ao mesmo tempo, você também precisa se preocupar em garantir que o agregador só seja executado em uma única VM por vez.

Você pode usar [entidades duráveis](durable-functions-entities.md) para implementar facilmente esse padrão como uma única função.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

As entidades duráveis também podem ser modeladas como classes no .NET. Esse modelo pode ser útil se a lista de operações é fixa e torna-se grande. O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando classes e métodos .NET.

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

Os clientes podem enfileirar *as operações* de uma função de entidade (também conhecida como "sinalização") usando a [Associação de cliente de entidade](durable-functions-bindings.md#entity-client).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Os proxies gerados dinamicamente também estão disponíveis no .NET para sinalizar entidades de forma segura de tipo. Além de sinalizar, os clientes também podem consultar o estado de uma função de entidade usando [métodos de tipo seguro](durable-functions-bindings.md#entity-client-usage) na associação de cliente de orquestração.


```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

As funções de entidade estão disponíveis no [Durable Functions 2,0](durable-functions-versions.md) e superior.

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão Durable Functions é criada sobre a estrutura de [tarefa durável](https://github.com/Azure/durabletask), uma biblioteca de software livre no GitHub que é usada para criar fluxos de trabalho no código. Como Azure Functions é a evolução sem servidor do Azure WebJobs, Durable Functions é a evolução sem servidor do Framework de tarefa durável. A Microsoft e outras organizações usam o Framework de tarefa durável extensivamente para automatizar processos críticos. É uma opção natural para o ambiente de Azure Functions sem servidor.

## <a name="code-constraints"></a>Restrições de código

Para fornecer garantias de execução confiáveis e de execução longa, as funções de orquestrador têm um conjunto de regras de codificação que devem ser seguidas. Para obter mais informações, consulte o artigo [restrições de código de função do Orchestrator](durable-functions-code-constraints.md) .

## <a name="billing"></a>Faturação

Durable Functions são cobradas da mesma forma que Azure Functions. Para obter mais informações, consulte [preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Ao executar funções de orquestrador no [plano de consumo](../functions-scale.md#consumption-plan)de Azure functions, há alguns comportamentos de cobrança a serem cientes. Para obter mais informações sobre esses comportamentos, consulte o artigo [Durable Functions cobrança](durable-functions-billing.md) .

## <a name="jump-right-in"></a>Ir direto para a direita

Você pode começar a usar o Durable Functions em menos de 10 minutos, concluindo um destes tutoriais de início rápido específicos do idioma:

* [C#usando o Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando Visual Studio Code](quickstart-js-vscode.md)

Em ambos os guias de início rápido, você cria e testa localmente uma função durável "Olá, mundo". Em seguida, publique o código de função no Azure. A função que você cria orquestra e encadeia chamadas para outras funções.

## <a name="learn-more"></a>Saiba mais

O vídeo a seguir destaca os benefícios de Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Para obter uma discussão mais detalhada sobre o Durable Functions e a tecnologia subjacente, consulte o vídeo a seguir (ele se concentra no .NET, mas os conceitos também se aplicam a outros idiomas com suporte):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Como Durable Functions é uma extensão avançada para [Azure Functions](../functions-overview.md), ela não é apropriada para todos os aplicativos. Para obter uma comparação com outras tecnologias de orquestração do Azure, consulte [comparar Azure Functions e aplicativos lógicos do Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Durable Functions tipos de função e recursos](durable-functions-types-features-overview.md)
