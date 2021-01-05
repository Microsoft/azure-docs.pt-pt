---
title: Visão geral das funções duradouras - Azure
description: Introdução à extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 3725970c982c2d060685bf0b99d12a8fc998f20a
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763579"
---
# <a name="what-are-durable-functions"></a>O que é a Durable Functions?

*Funções Duradouras* é uma extensão de [Funções Azure](../functions-overview.md) que permite escrever funções imponentes num ambiente de computação sem servidor. A extensão permite definir fluxos de trabalho imponentes escrevendo [*funções orquestradoras*](durable-functions-orchestrations.md) e entidades imponentes, [*escrevendo funções de entidade*](durable-functions-entities.md) utilizando o modelo de programação Azure Functions. Nos bastidores, a extensão gere o estado, os postos de controlo e o recomeço para si, permitindo-lhe focar-se na sua lógica de negócio.

## <a name="supported-languages"></a><a name="language-support"></a>Linguagens suportadas

Funções Duradouras suportam atualmente os seguintes idiomas:

* **C.**: ambas [as bibliotecas de classes pré-recompensadas](../functions-dotnet-class-library.md) e [o script C#](../functions-reference-csharp.md).
* **JavaScript**: suportado apenas para a versão 2.x do tempo de execução das Funções Azure. Requer a versão 1.7.0 da extensão funções duradouras, ou uma versão posterior. 
* **Python**: requer a versão 2.3.1 da extensão de Funções Duráveis, ou uma versão posterior. O suporte para funções duradouras está atualmente em pré-visualização pública.
* **F#**: bibliotecas de classes pré-recompensadas e script F#. F# O script só é suportado para a versão 1.x do tempo de execução das Funções Azure.
* **PowerShell**: o suporte para Funções Duradouras está atualmente em visualização pública. Suportado apenas para a versão 3.x do tempo de funcionamento das Funções Azure e PowerShell 7. Requer a versão 2.2.2 da extensão funções duradouras, ou uma versão posterior. Apenas os seguintes padrões são suportados atualmente: [Acorrentamento de funções,](#chaining) [Fan-out/fan-in,](#fan-in-out) [APIs Async HTTP](#async-http).

Para aceder às funcionalidades e atualizações mais recentes, recomenda-se que utilize as versões mais recentes da extensão de Funções Duradouras e as bibliotecas de Funções Duráveis específicas da linguagem. Saiba mais sobre [as versões Funções Duradouras.](durable-functions-versions.md)

As Funções Duradouras têm como objetivo apoiar todas as [línguas Azure Functions](../supported-languages.md). Consulte a [lista de problemas de funções duradouras](https://github.com/Azure/azure-functions-durable-extension/issues) para obter o mais recente estado de trabalho para suportar idiomas adicionais.

Tal como as Funções Azure, existem modelos para o ajudar a desenvolver Funções Duráveis utilizando [o Visual Studio 2019,](durable-functions-create-first-csharp.md) [o Visual Studio Code](quickstart-js-vscode.md)e o [portal Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Padrões de aplicação

O caso de utilização primária para funções duradouras está a simplificar requisitos complexos e apátridas de coordenação em aplicações sem servidor. As seguintes secções descrevem padrões típicos de aplicação que podem beneficiar de Funções Duradouras:

* [Encadeamento de funções](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [APIs HTTP assíncronas](#async-http)
* [Monitorização](#monitoring)
* [Interação humana](#human)
* [Agregador (entidades imponentes)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Padrão #1: Acorrentamento de funções

No padrão de acorrentamento da função, uma sequência de funções executa numa ordem específica. Neste padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama do padrão de acorrentamento da função](./media/durable-functions-concepts/function-chaining.png)

Pode utilizar funções duradouras para implementar o padrão de corrente de função de forma concisa, como mostrado no exemplo seguinte.

Neste exemplo, os valores `F1` , , e são os `F2` `F3` `F4` nomes de outras funções na mesma aplicação de função. Pode implementar o fluxo de controlo utilizando construções normais de codificação imperativas. O código executa de cima para baixo. O código pode envolver a semântica de fluxo de fluxo de linguagem existente, como condicional e loops. Pode incluir lógica de manuseamento de erros em `try` / `catch` / `finally` blocos.

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

Pode utilizar o `context` parâmetro para invocar outras funções pelo nome, passar parâmetros e devolver a saída da função. De cada vez que o código `await` chama, o quadro de funções duráveis verifica o progresso da instância de função atual. Se o processo ou máquina virtual reciclar a meio da execução, a instância de função retoma a partir da `await` chamada anterior. Para mais informações, consulte a secção seguinte, Padrão #2: Ventilador para fora/ventilador dentro

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

Pode utilizar o `context.df` objeto para invocar outras funções pelo nome, passar parâmetros e devolver a saída da função. De cada vez que o código `yield` chama, o quadro de funções duráveis verifica o progresso da instância de função atual. Se o processo ou máquina virtual reciclar a meio da execução, a instância de função retoma a partir da `yield` chamada anterior. Para mais informações, consulte a secção seguinte, Padrão #2: Ventilador para fora/ventilador dentro

> [!NOTE]
> O `context` objeto em JavaScript representa todo o contexto [de função](../functions-reference-node.md#context-object). Aceda ao contexto Funções Duráveis utilizando a `df` propriedade no contexto principal.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

Pode utilizar o `context` objeto para invocar outras funções pelo nome, passar parâmetros e devolver a saída da função. De cada vez que o código `yield` chama, o quadro de funções duráveis verifica o progresso da instância de função atual. Se o processo ou máquina virtual reciclar a meio da execução, a instância de função retoma a partir da `yield` chamada anterior. Para mais informações, consulte a secção seguinte, Padrão #2: Ventilador para fora/ventilador dentro

> [!NOTE]
> O `context` objeto em Python representa o contexto de orquestração. Aceda ao principal contexto Azure Functions utilizando o `function_context` imóvel no contexto de orquestração.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

Pode utilizar o `Invoke-ActivityFunction` comando para invocar outras funções pelo nome, passar parâmetros e devolver a saída da função. Cada vez que o código chama `Invoke-ActivityFunction` sem o `NoWait` interruptor, o quadro de funções duráveis verifica o progresso da instância de função atual. Se o processo ou máquina virtual reciclar a meio da execução, a instância de função retoma a partir da `Invoke-ActivityFunction` chamada anterior. Para mais informações, consulte a secção seguinte, Padrão #2: Ventilador para fora/ventilador dentro

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Padrão #2: Ventilador para fora/ventilador em

No ventilador out/ventilador em padrão, executa várias funções em paralelo e, em seguida, aguarde que todas as funções terminem. Muitas vezes, alguns trabalhos de agregação são feitos sobre os resultados que são devolvidos das funções.

![Um diagrama do ventilador fora/padrão de ventilador](./media/durable-functions-concepts/fan-out-fan-in.png)

Com funções normais, pode dispersar-se fazendo com que a função envie várias mensagens para uma fila. Voltar a entrar é muito mais desafiante. Para abanar, numa função normal, escreve código para rastrear quando as funções desencadeadas pela fila terminam e, em seguida, armazena as saídas da função.

A extensão funções duráveis lida com este padrão com código relativamente simples:

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

O trabalho de ventilação é distribuído em várias instâncias da `F2` função. O trabalho é acompanhado através de uma lista dinâmica de tarefas. `Task.WhenAll` é chamado a esperar que todas as funções chamadas terminem. Em seguida, as `F2` saídas de função são agregadas da lista de tarefas dinâmicas e transmitidas para a `F3` função.

O controlo automático que ocorre na `await` chamada garante que uma `Task.WhenAll` possível falha ou reinicialização do meio do caminho não requer o reinício de uma tarefa já concluída.

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

O trabalho de ventilação é distribuído em várias instâncias da `F2` função. O trabalho é acompanhado através de uma lista dinâmica de tarefas. `context.df.Task.all` A API é chamada a aguardar que todas as funções chamadas terminem. Em seguida, as `F2` saídas de função são agregadas da lista de tarefas dinâmicas e transmitidas para a `F3` função.

O controlo automático que ocorre na `yield` chamada garante que uma `context.df.Task.all` possível falha ou reinicialização do meio do caminho não requer o reinício de uma tarefa já concluída.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

O trabalho de ventilação é distribuído em várias instâncias da `F2` função. O trabalho é acompanhado através de uma lista dinâmica de tarefas. `context.task_all` A API é chamada a aguardar que todas as funções chamadas terminem. Em seguida, as `F2` saídas de função são agregadas da lista de tarefas dinâmicas e transmitidas para a `F3` função.

O controlo automático que ocorre na `yield` chamada garante que uma `context.task_all` possível falha ou reinicialização do meio do caminho não requer o reinício de uma tarefa já concluída.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

O trabalho de ventilação é distribuído em várias instâncias da `F2` função. Por favor, note a utilização do `NoWait` interruptor na invocação da `F2` função: este interruptor permite que o orquestrador proceda a invocar `F2` sem a conclusão da atividade. O trabalho é acompanhado através de uma lista dinâmica de tarefas. O `Wait-ActivityFunction` comando é chamado para esperar que todas as funções chamadas terminem. Em seguida, as `F2` saídas de função são agregadas da lista de tarefas dinâmicas e transmitidas para a `F3` função.

O controlo automático que acontece na `Wait-ActivityFunction` chamada garante que uma possível falha ou reinicialização do meio do caminho não requer o reinício de uma tarefa já concluída.

---

> [!NOTE]
> Em circunstâncias raras, é possível que um acidente possa acontecer na janela depois de uma função de atividade terminar, mas antes que a sua conclusão seja guardada para a história da orquestração. Se isso acontecer, a função de atividade seria re-executada desde o início após a recuperação do processo.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Padrão #3: APIs HTTP Async

O padrão da API HTTP async aborda o problema da coordenação do estado das operações de longa duração com clientes externos. Uma forma comum de implementar este padrão é tendo um ponto final HTTP desencadeando a ação de longa duração. Em seguida, redirecione o cliente para um ponto final de estado que o cliente sonda para saber quando a operação está terminada.

![Um diagrama do padrão http API](./media/durable-functions-concepts/async-http-api.png)

As Funções Duradouras fornecem **suporte incorporado** para este padrão, simplificando ou mesmo removendo o código que precisa de escrever para interagir com execuções de função de longa duração. Por exemplo, as amostras de arranque rápido de Funções Durable[(C#](durable-functions-create-first-csharp.md) e [JavaScript](quickstart-js-vscode.md)) mostram um simples comando REST que pode utilizar para iniciar novas instâncias de função de orquestrador. Após o início de um caso, a extensão expõe o webhook HTTP APIs que consulta o estado da função do orquestrador. 

O exemplo a seguir mostra comandos REST que iniciam um orquestrador e consultam o seu estado. Para maior clareza, alguns detalhes do protocolo são omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Como o tempo de funcionamento das Funções Duradouras gere o seu estado, não precisa de implementar o seu próprio mecanismo de rastreio de estado.

A extensão Funções Duradouras expõe APIs HTTP incorporadas que gerem orquestrações de longa duração. Em alternativa, pode implementar este padrão utilizando os seus próprios gatilhos de função (como HTTP, uma fila ou Azure Event Hubs) e a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client). Por exemplo, pode utilizar uma mensagem de fila para desencadear a rescisão. Ou, pode utilizar um gatilho HTTP protegido por uma política de autenticação do Azure Ative Directory em vez das APIs HTTP incorporadas que utilizam uma chave gerada para a autenticação.

Para obter mais informações, consulte o artigo [de funcionalidades HTTP,](durable-functions-http-features.md) que explica como pode expor processos assíncronos e de longa duração sobre HTTP utilizando a extensão de Funções Duradouras.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Padrão #4: Monitor

O padrão do monitor refere-se a um processo flexível e recorrente num fluxo de trabalho. Um exemplo é a sondagem até que as condições específicas sejam satisfeitas. Pode utilizar um [gatilho de temporizador](../functions-bindings-timer.md) regular para abordar um cenário básico, como um trabalho de limpeza periódica, mas o seu intervalo é estático e gerir as vidas de instâncias torna-se complexo. Pode utilizar funções duradouras para criar intervalos de recorrência flexíveis, gerir as tarefas e criar múltiplos processos de monitorização a partir de uma única orquestração.

Um exemplo do padrão do monitor é inverter o cenário anterior da API HTTP HTTP. Em vez de expor um ponto final para um cliente externo monitorizar uma operação de longa duração, o monitor de longa duração consome um ponto final externo e, em seguida, aguarda por uma mudança de estado.

![Um diagrama do padrão do monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, pode utilizar Funções Duráveis para criar múltiplos monitores que observam pontos finais arbitrários. Os monitores podem terminar a execução quando uma condição é satisfeita, ou outra função pode usar o cliente de orquestração durável para terminar os monitores. Pode alterar o intervalo de um monitor `wait` com base numa condição específica (por exemplo, recuo exponencial.) 

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O Monitor não é suportado atualmente no PowerShell.

---

Quando um pedido é recebido, uma nova instância de orquestração é criada para esse iD de trabalho. O caso sonda um estado até que uma condição seja satisfeita e o loop seja saído. Um temporizador durável controla o intervalo de votação. Então, mais trabalho pode ser realizado, ou a orquestração pode terminar. Quando `nextCheck` `expiryTime` exceder, o monitor termina.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Padrão #5: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver humanos num processo automatizado é complicado porque as pessoas não estão tão disponíveis e tão responsivas como serviços na nuvem. Um processo automatizado pode permitir esta interação utilizando intervalos de tempo e lógica de compensação.

Um processo de aprovação é um exemplo de um processo de negócio que envolve interação humana. A aprovação de um gestor pode ser necessária para um relatório de despesas que exceda um determinado valor em dólares. Se o gestor não aprovar o relatório de despesas dentro de 72 horas (talvez o gerente tenha ido de férias), um processo de escalada começa a receber a aprovação de outra pessoa (talvez o gerente).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Pode implementar o padrão neste exemplo utilizando uma função orquestradora. O orquestrador usa um [temporizador durável](durable-functions-timers.md) para solicitar aprovação. O orquestrador aumenta se ocorrer tempo. O orquestrador aguarda um [evento externo](durable-functions-external-events.md), como uma notificação que é gerada por uma interação humana.

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

Para criar o temporizador durável, `context.CreateTimer` ligue. A notificação é recebida por `context.WaitForExternalEvent` . Em seguida, `Task.WhenAny` é chamado a decidir se se agrava (o intervalo acontece primeiro) ou processa a aprovação (a aprovação é recebida antes do intervalo).

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

Para criar o temporizador durável, `context.df.createTimer` ligue. A notificação é recebida por `context.df.waitForExternalEvent` . Em seguida, `context.df.Task.any` é chamado a decidir se se agrava (o intervalo acontece primeiro) ou processa a aprovação (a aprovação é recebida antes do intervalo).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Para criar o temporizador durável, `context.create_timer` ligue. A notificação é recebida por `context.wait_for_external_event` . Em seguida, `context.task_any` é chamado a decidir se se agrava (o intervalo acontece primeiro) ou processa a aprovação (a aprovação é recebida antes do intervalo).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A interação humana não é suportada no PowerShell.

---

Um cliente externo pode entregar a notificação do evento a uma função de orquestrador de espera utilizando as [APIs HTTP incorporadas:](durable-functions-http-api.md#raise-event)

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Um evento também pode ser levantado usando o cliente de orquestração durável a partir de outra função na mesma aplicação de função:

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A interação humana não é suportada no PowerShell.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Padrão #6: Agregador (entidades imponentes)

O sexto padrão é sobre agregar dados de eventos durante um período de tempo numa única *entidade* endereçada. Neste padrão, os dados agregados podem vir de múltiplas fontes, podem ser entregues em lotes, ou podem ser dispersos por longos períodos de tempo. O agregador poderá ter de tomar medidas sobre os dados dos eventos à medida que chega, e os clientes externos podem precisar de consultar os dados agregados.

![Diagrama agregador](./media/durable-functions-concepts/aggregator.png)

O mais complicado de tentar implementar este padrão com funções normais e apátridas é que o controlo da conuncy torna-se um grande desafio. Não só precisa de se preocupar com vários fios que modificam os mesmos dados ao mesmo tempo, como também tem de se preocupar em garantir que o agregador só funciona num único VM de cada vez.

Você pode usar [entidades duráveis](durable-functions-entities.md) para implementar facilmente este padrão como uma única função.

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

Entidades duradouras também podem ser modeladas como classes em .NET. Este modelo pode ser útil se a lista de operações for fixa e se tornar grande. O exemplo a seguir é uma implementação equivalente da `Counter` entidade utilizando classes e métodos .NET.

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

# <a name="python"></a>[Python](#tab/python)

As entidades duradouras não são atualmente apoiadas em Python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

As entidades duradouras não são atualmente apoiadas na PowerShell.

---

Os clientes podem enquadrizar *operações* para (também conhecida como "sinalização") uma função de entidade utilizando a ligação do cliente da [entidade.](durable-functions-bindings.md#entity-client)

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
> Os proxies gerados dinamicamente também estão disponíveis em .NET para entidades de sinalização de forma tipo segura. Além da sinalização, os clientes também podem consultar o estado de uma função de entidade utilizando [métodos de tipo seguro](durable-functions-bindings.md#entity-client-usage) na ligação do cliente de orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

As entidades duradouras não são atualmente apoiadas em Python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

As entidades duradouras não são atualmente apoiadas na PowerShell.

---

As funções de entidade estão disponíveis em [Funções Duráveis 2.0](durable-functions-versions.md) e superior para C# e JavaScript.

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão de Funções Duradouras é construída em cima do Quadro de [Tarefas Durable](https://github.com/Azure/durabletask), uma biblioteca de código aberto no GitHub que é usada para construir fluxos de trabalho em código. Tal como as Funções Azure é a evolução sem servidor do Azure WebJobs, as Funções Duradouras são a evolução sem servidor do Quadro de Tarefas Duráveis. A Microsoft e outras organizações utilizam o Quadro de Tarefas Durable extensivamente para automatizar processos críticos da missão. É um ajuste natural para o ambiente Azure Functions sem servidor.

## <a name="code-constraints"></a>Restrições de código

A fim de fornecer garantias de execução fiáveis e de longa duração, as funções de orquestrador têm um conjunto de regras de codificação que devem ser seguidas. Para obter mais informações, consulte o artigo de restrições de [função do Orquestrador.](durable-functions-code-constraints.md)

## <a name="billing"></a>Faturação

As funções duradouras são faturadas da mesma forma que as Funções Azure. Para obter mais informações, consulte [os preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Ao executar funções de orquestrador no [plano](../functions-scale.md#consumption-plan)de consumo de funções Azure, existem alguns comportamentos de faturação a ter em conta. Para obter mais informações sobre estes comportamentos, consulte o artigo [de faturação de Funções Duradouras.](durable-functions-billing.md)

## <a name="jump-right-in"></a>Salta para dentro

Você pode começar com Funções Duráveis em menos de 10 minutos completando um destes tutoriais de arranque rápido específicos da linguagem:

* [C# usando Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando código de estúdio visual](quickstart-js-vscode.md)
* [Python usando código de estúdio visual](quickstart-python-vscode.md)
* [PowerShell usando código de estúdio visual](quickstart-powershell-vscode.md)

Nestes arranques rápidos, você cria localmente e testa uma função durável "olá mundo". Em seguida, publique o código de função no Azure. A função que cria orquestra e acorrenta em conjunto chama a outras funções.

## <a name="learn-more"></a>Saiba mais

O seguinte vídeo destaca os benefícios das Funções Duradouras:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Para uma discussão mais aprofundada sobre as Funções Duradouras e a tecnologia subjacente, consulte o seguinte vídeo (está focado em .NET, mas os conceitos também se aplicam a outras línguas apoiadas):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Como as Funções Duradouras são uma extensão avançada para [funções Azure,](../functions-overview.md)não é apropriado para todas as aplicações. Para uma comparação com outras tecnologias de orquestração Azure, consulte [Compare Azure Functions e Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Funções duradouras tipos e funcionalidades](durable-functions-types-features-overview.md)
