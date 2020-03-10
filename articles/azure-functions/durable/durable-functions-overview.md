---
title: Visão geral de funções duráveis - Azure
description: Introdução à extensão de funções duráveis para funções azure.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356698"
---
# <a name="what-are-durable-functions"></a>O que é a Durable Functions?

*Funções Duráveis* é uma extensão das [Funções Azure](../functions-overview.md) que permite escrever funções imponentes num ambiente de computação sem servidores. A extensão permite definir fluxos de trabalho estatais escrevendo [*funções orquestradoras*](durable-functions-orchestrations.md) e entidades imponentes através da escrita [*de funções*](durable-functions-entities.md) de entidade utilizando o modelo de programação de Funções Azure. Nos bastidores, a extensão gere o estado, os postos de controlo e reinicia para si, permitindo-lhe concentrar-se na sua lógica de negócio.

## <a name="language-support"></a>Línguas apoiadas

As Funções Duráveis suportam atualmente as seguintes línguas:

* **C#** : tanto [bibliotecas de classes pré-compiladas](../functions-dotnet-class-library.md) como [ C# script](../functions-reference-csharp.md).
* **JavaScript**: suportado apenas para a versão 2.x do tempo de funcionamento das Funções Azure. Requer a versão 1.7.0 da extensão das Funções Duráveis, ou uma versão posterior. 
* **F#** : bibliotecas de F# classes pré-compiladas e guião. F#script é suportado apenas para versão 1.x do tempo de execução das Funções Azure.

As Funções Duráveis têm como objetivo apoiar todas as línguas das [Funções Azure.](../supported-languages.md) Consulte a lista de problemas de [Funções Duráveis](https://github.com/Azure/azure-functions-durable-extension/issues) para o mais recente estado de trabalho para suportar línguas adicionais.

Tal como as Funções Azure, existem modelos para o ajudar a desenvolver Funções Duráveis utilizando o [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)e o portal [Azure.](durable-functions-create-portal.md)

## <a name="application-patterns"></a>Padrões de aplicação

O principal caso de utilização para Funções Duráveis é simplificar requisitos de coordenação complexos e estatais em aplicações sem servidor. As seguintes secções descrevem padrões típicos de aplicação que podem beneficiar de Funções Duráveis:

* [Corrente de funções](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [Async HTTP APIs](#async-http)
* [Monitorização](#monitoring)
* [Interação humana](#human)
* [Agregador (entidades estatais)](#aggregator)

### <a name="chaining"></a>#1 de padrão: Corrente de funções

No padrão de corrente de função, uma sequência de funções executa numa ordem específica. Neste padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama do padrão de corrente de função](./media/durable-functions-concepts/function-chaining.png)

Pode utilizar funções duráveis para implementar o padrão de corrente de funções concisamente, como mostra o seguinte exemplo.

Neste exemplo, os valores `F1`, `F2`, `F3`e `F4` são os nomes de outras funções na mesma aplicação de funções. Pode implementar o fluxo de controlo utilizando construções normais de codificação imperativas. O código executa de cima para baixo. O código pode envolver a semântica de fluxo de controlo linguístico existente, como condicionales e loops. Pode incluir lógica de manipulação de erros em `try`/  `catch`/`finally` blocos.

# <a name="c"></a>[C#](#tab/csharp)

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

Pode utilizar o parâmetro `context` para invocar outras funções pelo nome, passar parâmetros e devolução da função. Cada vez que o código chama `await`, o quadro de Funções Duráveis verifica o progresso da atual função. Se o processo ou a máquina virtual reciclar a meio da execução, a instância de função retoma a partir da chamada de `await` anterior. Para mais informações, consulte a secção seguinte, Pattern #2: Fan out/fan in.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Pode utilizar o `context.df` objeto para invocar outras funções pelo nome, passar parâmetros e devolver a saída da função. Cada vez que o código chama `yield`, o quadro de Funções Duráveis verifica o progresso da atual função. Se o processo ou a máquina virtual reciclar a meio da execução, a instância de função retoma a partir da chamada de `yield` anterior. Para mais informações, consulte a secção seguinte, Pattern #2: Fan out/fan in.

> [!NOTE]
> O objeto `context` no JavaScript representa todo o [contexto da função](../functions-reference-node.md#context-object). Aceda ao contexto das Funções Duráveis utilizando a propriedade `df` no contexto principal.

---

### <a name="fan-in-out"></a>Padrão #2: Ventilador out/ventilador em

No ventilador/ventilador em padrão, executa múltiplas funções paralelamente e, em seguida, aguarde que todas as funções terminem. Muitas vezes, alguns trabalhos de agregação são feitos sobre os resultados que são devolvidos das funções.

![Um diagrama do padrão de ventilador/ventilador](./media/durable-functions-concepts/fan-out-fan-in.png)

Com funções normais, pode sair com a função enviar várias mensagens para uma fila. Voltar a entrar é muito mais desafiante. Para se apagar, numa função normal, escreve código para rastrear quando as funções acionadas pela fila terminam e, em seguida, armazenar as saídas de função.

A extensão das Funções Duráveis trata este padrão com um código relativamente simples:

# <a name="c"></a>[C#](#tab/csharp)

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

O trabalho de fan-out é distribuído em várias instâncias da função `F2`. O trabalho é acompanhado através de uma lista dinâmica de tarefas. `Task.WhenAll` é chamado para esperar que todas as funções chamadas terminem. Em seguida, as saídas de função `F2` são agregadas a partir da lista de tarefas dinâmicas e passadas para a função `F3`.

O checkpoint automático que ocorre na `await` chamada `Task.WhenAll` garante que uma possível colisão ou reinicialização a meio do caminho não requer reiniciar uma tarefa já concluída.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

O trabalho de fan-out é distribuído em várias instâncias da função `F2`. O trabalho é acompanhado através de uma lista dinâmica de tarefas. `context.df.Task.all` API é chamada para esperar que todas as funções chamadas terminem. Em seguida, as saídas de função `F2` são agregadas a partir da lista de tarefas dinâmicas e passadas para a função `F3`.

O checkpoint automático que ocorre na `yield` chamada `context.df.Task.all` garante que uma possível colisão ou reinicialização a meio do caminho não requer reiniciar uma tarefa já concluída.

---

> [!NOTE]
> Em circunstâncias raras, é possível que um acidente possa acontecer na janela depois de uma função de atividade terminar, mas antes que a sua conclusão seja guardada na história da orquestração. Se isso acontecer, a função de atividade re-reafoga-se desde o início após a recuperação do processo.

### <a name="async-http"></a>Padrão #3: Async HTTP APIs

O padrão async HTTP API aborda o problema da coordenação do estado das operações de longo prazo com clientes externos. Uma forma comum de implementar este padrão é ter um ponto final HTTP desencadear a ação de longo prazo. Em seguida, redirecione o cliente para um ponto final de estado que o cliente vota para saber quando a operação está terminada.

![Um diagrama do padrão HTTP API](./media/durable-functions-concepts/async-http-api.png)

As Funções Duráveis fornecem **suporte incorporado** para este padrão, simplificando ou mesmo removendo o código que precisa escrever para interagir com execuções de funções de longa duração. Por exemplo, as amostras de[C#](durable-functions-create-first-csharp.md) arranque rápido das Funções Duráveis (e [JavaScript](quickstart-js-vscode.md)) mostram um simples comando REST que pode usar para iniciar novas instâncias de função orquestradora. Após o início de uma instância, a extensão expõe AApIs HTTP webhook que consultam o estado da função do orquestrador. 

O exemplo seguinte mostra comandos REST que iniciam um orquestrador e questionam o seu estado. Para maior clareza, alguns detalhes do protocolo são omitidos do exemplo.

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

Como o tempo de funcionamento das Funções Duráveis gere o estado para si, não precisa de implementar o seu próprio mecanismo de rastreio de estatutos.

A extensão Das Funções Duráveis expõe APIs HTTP incorporados que gerem orquestrações de longa duração. Você pode, em alternativa, implementar este padrão usando os seus próprios gatilhos de função (como HTTP, uma fila, ou Azure Event Hubs) e a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client). Por exemplo, pode utilizar uma mensagem de fila para desencadear a rescisão. Ou, pode utilizar um gatilho HTTP protegido por uma política de autenticação do Diretório Ativo Azure em vez das APIs HTTP incorporadas que utilizam uma chave gerada para autenticação.

Para mais informações, consulte o artigo de [funcionalidades HTTP,](durable-functions-http-features.md) que explica como pode expor processos assíncronos e de longa duração sobre http utilizando a extensão das Funções Duráveis.

### <a name="monitoring"></a>#4 de padrão: Monitor

O padrão do monitor refere-se a um processo flexível e recorrente num fluxo de trabalho. Um exemplo é a sondagem até que sejam satisfeitas condições específicas. Você pode usar um [gatilho temporizador](../functions-bindings-timer.md) regular para abordar um cenário básico, como um trabalho de limpeza periódica, mas o seu intervalo é estático e gerir as vidas de instância torna-se complexo. Pode utilizar Funções Duráveis para criar intervalos flexíveis de recorrência, gerir as vidas de tarefas e criar múltiplos processos de monitor a partir de uma única orquestração.

Um exemplo do padrão do monitor é inverter o cenário async http aPI anterior. Em vez de expor um ponto final para um cliente externo monitorizar uma operação de longa duração, o monitor de longa duração consome um ponto final externo e, em seguida, aguarda uma mudança de Estado.

![Um diagrama do padrão do monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, pode utilizar Funções Duráveis para criar múltiplos monitores que observem pontos finais arbitrários. Os monitores podem terminar a execução quando uma condição é satisfeita, ou outra função pode usar o cliente de orquestração durável para terminar os monitores. Pode alterar o intervalo de `wait` de um monitor com base numa condição específica (por exemplo, backoff exponencial.) 

O seguinte código implementa um monitor básico:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
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

---

Quando um pedido é recebido, uma nova instância de orquestração é criada para esse id de trabalho. A instância dá um estatuto até que uma condição seja cumprida e o loop seja de si. Um temporizador durável controla o intervalo das sondagens. Então, mais trabalho pode ser realizado, ou a orquestração pode terminar. Quando `nextCheck` exceder `expiryTime`, o monitor termina.

### <a name="human"></a>Padrão #5: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver humanos num processo automatizado é complicado porque as pessoas não estão tão disponíveis e tão responsivas como os serviços de nuvem. Um processo automatizado pode permitir esta interação utilizando intervalos e lógica de compensação.

Um processo de aprovação é um exemplo de um processo de negócio que envolve a interação humana. A aprovação de um gestor pode ser necessária para um relatório de despesas que exceda um determinado valor em dólares. Se o gestor não aprovar o relatório de despesas dentro de 72 horas (talvez o gerente tenha ido de férias), um processo de escalada começa a fazer efeito para obter a aprovação de outra pessoa (talvez o gerente do gestor).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Pode implementar o padrão neste exemplo utilizando uma função de orquestrador. O orquestrador usa um [temporizador durável](durable-functions-timers.md) para pedir aprovação. O orquestrador aumenta se o tempo for. O orquestrador aguarda por um [evento externo,](durable-functions-external-events.md)como uma notificação que é gerada por uma interação humana.

Estes exemplos criam um processo de aprovação para demonstrar o padrão de interação humana:

# <a name="c"></a>[C#](#tab/csharp)

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

Para criar o temporizador durável, ligue para `context.CreateTimer`. A notificação é recebida por `context.WaitForExternalEvent`. Em seguida, `Task.WhenAny` é chamado para decidir se a escala (timeout acontece primeiro) ou processar a aprovação (a aprovação é recebida antes do intervalo).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Para criar o temporizador durável, ligue para `context.df.createTimer`. A notificação é recebida por `context.df.waitForExternalEvent`. Em seguida, `context.df.Task.any` é chamado para decidir se a escala (timeout acontece primeiro) ou processar a aprovação (a aprovação é recebida antes do intervalo).

---

Um cliente externo pode entregar a notificação do evento a uma função de orquestrador de espera utilizando as [APIs HTTP incorporadas:](durable-functions-http-api.md#raise-event)

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Um evento também pode ser levantado usando o cliente de orquestração durável de outra função na mesma app de funções:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="aggregator"></a>Padrão #6: Agregador (entidades estatais)

O sexto padrão é sobre agregação de dados de eventos durante um período de tempo em uma única *entidade*endereçada . Neste padrão, os dados agregados podem vir de várias fontes, podem ser entregues em lotes, ou podem ser espalhados por longos períodos de tempo. O agregador poderá ter de tomar medidas sobre os dados do evento à medida que chega, e os clientes externos podem precisar de consultar os dados agregados.

![Diagrama agregador](./media/durable-functions-concepts/aggregator.png)

O mais complicado de tentar implementar este padrão com funções normais e apátridas é que o controlo da condivisa se torna um grande desafio. Não só precisa de se preocupar com vários fios que modifiquem os mesmos dados ao mesmo tempo, como também precisa de se preocupar em garantir que o agregador funciona apenas num único VM de cada vez.

Pode utilizar [entidades Duráveis](durable-functions-entities.md) para implementar facilmente este padrão como uma única função.

# <a name="c"></a>[C#](#tab/csharp)

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

As entidades duráveis também podem ser modeladas como classes em .NET. Este modelo pode ser útil se a lista de operações for fixada e se tornar grande. O exemplo que se segue é uma implementação equivalente da entidade `Counter` utilizando classes e métodos .NET.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Os clientes podem fazer filas de *operações* para (também conhecida como "sinalização") uma função de entidade utilizando a [entidade de encadernação do cliente](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C#](#tab/csharp)

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
> Os proxies gerados dinamicamente também estão disponíveis em .NET para sinalizar entidades de forma segura. Além da sinalização, os clientes também podem consultar o estado de uma função de entidade usando [métodos de tipo-safe](durable-functions-bindings.md#entity-client-usage) na ligação do cliente de orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

As funções da entidade estão disponíveis nas [Funções Duráveis 2.0](durable-functions-versions.md) ou superior.

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão das Funções Duráveis é construída em cima do Quadro de [Tarefas Duráveis,](https://github.com/Azure/durabletask)uma biblioteca de código aberto no GitHub que é usada para construir fluxos de trabalho em código. Tal como o Azure Functions é a evolução inservidora do Azure WebJobs, as Funções Duráveis são a evolução inútil do Quadro de Tarefas Duráveis. A Microsoft e outras organizações utilizam extensivamente o Quadro de Tarefas Duráveis para automatizar processos críticos de missão. É um ajuste natural para o ambiente de funções azure sem servidor.

## <a name="code-constraints"></a>Restrições de código

A fim de fornecer garantias de execução fiáveis e de longa duração, as funções orquestradoras têm um conjunto de regras de codificação que devem ser seguidas. Para mais informações, consulte o artigo de restrições de código de [função orchestrator.](durable-functions-code-constraints.md)

## <a name="billing"></a>Faturação

Funções Duráveis são faturadas da mesma forma que as Funções Azure. Para mais informações, consulte o preço das [Funções Azure](https://azure.microsoft.com/pricing/details/functions/). Ao executar funções de orquestrador no plano de [consumo](../functions-scale.md#consumption-plan)de funções Azure, há alguns comportamentos de faturação a ter em conta. Para obter mais informações sobre estes comportamentos, consulte o artigo de faturação de [Funções Duráveis.](durable-functions-billing.md)

## <a name="jump-right-in"></a>Salta para dentro

Você pode começar com Funções Duráveis em menos de 10 minutos completando um destes tutoriais de quickstart específicos da linguagem:

* [C#usando o Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando código de estúdio visual](quickstart-js-vscode.md)

Em ambos os arranques rápidos, cria-se localmente e testa-se uma função durável "hello world". Em seguida, publique o código de função no Azure. A função que cria orquestrações e correntes em conjunto apela a outras funções.

## <a name="learn-more"></a>Saiba mais

O seguinte vídeo destaca os benefícios das Funções Duráveis:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Para uma discussão mais aprofundada sobre as Funções Duráveis e a tecnologia subjacente, consulte o seguinte vídeo (está focado em .NET, mas os conceitos também se aplicam a outras línguas apoiadas):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Como as Funções Duráveis são uma extensão avançada para [funções Azure,](../functions-overview.md)não é apropriado para todas as aplicações. Para uma comparação com outras tecnologias de orquestração Azure, consulte [Compare Azure Functions e Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tipos e características de funções de funções duráveis](durable-functions-types-features-overview.md)
