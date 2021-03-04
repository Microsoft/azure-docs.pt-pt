---
title: Gerir instâncias em Funções Duradouras - Azure
description: Saiba como gerir casos na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 2121ad4f908d28ab30f15be261b3f155ab6dd983
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039704"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerir instâncias em Funções Duradouras em Azure

Se estiver a utilizar a extensão [de Funções Duradouras](durable-functions-overview.md) para Funções Azure, ou quiser começar a fazê-lo, certifique-se de que está a obter o melhor uso. Pode otimizar os seus casos de orquestração de Funções Duráveis aprendendo mais sobre como geri-los. Este artigo vai para os detalhes de cada operação de gestão de instâncias.

Pode iniciar e encerrar casos, por exemplo, e pode consultar casos, incluindo a capacidade de consultar todas as instâncias e consultar casos com filtros. Além disso, pode enviar eventos para instâncias, aguardar a conclusão da orquestração e recuperar URLs de webhook de gestão HTTP. Este artigo abrange também outras operações de gestão, incluindo rebobinar instâncias, expurgar a história dos casos e eliminar um centro de tarefas.

Em Funções Duradouras, tem opções para a forma como pretende implementar cada uma destas operações de gestão. Este artigo fornece exemplos que utilizam as [Ferramentas Centrais de Funções Azure](../functions-run-local.md) para .NET (C#), JavaScript e Python.

## <a name="start-instances"></a>Iniciar instâncias

É importante ser capaz de iniciar um caso de orquestração. Isto é geralmente feito quando está a usar uma ligação de funções duradouras no gatilho de outra função.

O `StartNewAsync` método (.NET), `startNew` (JavaScript) ou `start_new` (Python) na [ligação](durable-functions-bindings.md#orchestration-client) do cliente de orquestração inicia uma nova instância. Internamente, este método encova uma mensagem na fila de controlo, que depois desencadeia o início de uma função com o nome especificado que utiliza a ligação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger).

Esta operação de assínca termina quando o processo de orquestração é programado com sucesso.

Os parâmetros para iniciar uma nova instância de orquestração são os seguintes:

* **Nome**: O nome da função do orquestrador a programar.
* **Entrada**: Quaisquer dados serializáveis do JSON que devem ser transmitidos como entrada para a função do orquestrador.
* **InstânciaId**: (Opcional) O ID único do caso. Se não especificar este parâmetro, o método utiliza um ID aleatório.

> [!TIP]
> Use um identificador aleatório para o tipo de identificação. Os IDs de instância aleatória ajudam a garantir uma distribuição de carga igual quando se está a escalonar funções de orquestradores em vários VMs. O tempo adequado para usar iDs de instância não aleatório é quando o ID deve vir de uma fonte externa, ou quando você está implementando o padrão [de orquestra de singleton.](durable-functions-singletons.md)

O seguinte código é uma função de exemplo que inicia uma nova instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Salvo especificação em contrário, os exemplos nesta página utilizam o gatilho HTTP com o seguinte function.jsligado.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo visa a versão 2.x das Funções Duradouras. Na versão 1.x, use `orchestrationClient` em vez de `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>Salvo especificação em contrário, os exemplos nesta página utilizam o gatilho HTTP com o seguinte function.jsligado.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo visa a versão 2.x das Funções Duradouras. Na versão 1.x, use `orchestrationClient` em vez de `durableClient` .

**__.py init__**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também pode iniciar uma ocorrência diretamente utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable start-new` Tools. São necessários os seguintes parâmetros:

* **`function-name` (obrigatório)**: Nome da função a iniciar.
* **`input` (opcional)**: Inserir na função, quer em linha quer através de um ficheiro JSON. Para ficheiros, adicione um prefixo ao caminho do ficheiro com `@` , tal como `@path/to/file.json` .
* **`id` (opcional)**: Identificação da instância de orquestração. Se não especificar este parâmetro, o comando utiliza um GUID aleatório.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. O padrão é DurableFunctionsHub. Também pode defini-lo em [host.js](durable-functions-bindings.md#host-json) utilizando durableTask:HubName.

> [!NOTE]
> Os comandos da Core Tools assumem que está a executá-los a partir do diretório de raiz de uma aplicação de função. Se fornecer explicitamente os `connection-string-setting` parâmetros e `task-hub-name` parâmetros, pode executar os comandos a partir de qualquer diretório. Embora possa executar estes comandos sem um anfitrião de aplicações de função em execução, pode descobrir que não pode observar alguns efeitos a menos que o anfitrião esteja em execução. Por exemplo, o `start-new` comando encoste uma mensagem de início no centro de tarefas alvo, mas a orquestração não funciona a menos que haja um processo de anfitrião de aplicações de função em execução que possa processar a mensagem.

O seguinte comando inicia a função chamada HelloWorld e transmite-lhe o conteúdo do `counter-data.json` ficheiro:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Casos de consulta

Como parte do seu esforço para gerir as suas orquestrações, provavelmente terá de recolher informações sobre o estado de uma instância de orquestração (por exemplo, se completou normalmente ou falhou).

O `GetStatusAsync` método (.NET), `getStatus` (JavaScript), ou o `get_status` método (Python) na [orquestração cliente que liga](durable-functions-bindings.md#orchestration-client) o estado de uma instância de orquestração.

É preciso um `instanceId` (obrigatório), `showHistory` (opcional), `showHistoryOutput` (opcional) e `showInput` (opcional) como parâmetros.

* **`showHistory`**: Se definido `true` para , a resposta contém o histórico de execução.
* **`showHistoryOutput`**: Se definido `true` para , o histórico de execução contém saídas de atividade.
* **`showInput`**: Se estiver `false` definido, a resposta não contém a entrada da função. O valor predefinido é `true`.

O método devolve um objeto com as seguintes propriedades:

* **Nome**: O nome da função orquestradora.
* **InstânciaId**: O ID da orquestração (deve ser o mesmo que a `instanceId` entrada).
* **CreatedTime**: O momento em que a função do orquestrador começou a funcionar.
* **LastUpdatedTime**: O momento em que a orquestração foi a última.
* **Entrada**: A entrada da função como valor JSON. Este campo não é povoado se `showInput` for falso.
* **CustomStatus**: Estado de orquestração personalizada no formato JSON.
* **Saída**: Saída da função como valor JSON (se a função tiver terminado). Se a função do orquestrador falhou, esta propriedade inclui os detalhes de falha. Se a função orquestradora foi terminada, esta propriedade inclui o motivo da rescisão (se houver).
* **RuntimeStatus**: Um dos seguintes valores:
  * **Pendente**: O caso foi agendado mas ainda não começou a funcionar.
  * **Execução**: O caso começou a funcionar.
  * **Concluído**: A instância foi concluída normalmente.
  * **ContinuaANew**: O caso recomeçou com uma nova história. Este estado é um estado transitório.
  * **Falha:** O caso falhou com um erro.
  * **Encerrado**: O caso foi interrompido abruptamente.
* **História**: A história da execução da orquestração. Este campo só é povoado se `showHistory` estiver definido para `true` .

Este método `null` retorna (.NET), `undefined` (JavaScript) ou `None` (Python) se a instância não existir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível obter o estatuto de uma instância de orquestração diretamente, usando o comando [Azure Functions Core](../functions-run-local.md) `durable get-runtime-status` Tools. São necessários os seguintes parâmetros:

* **`id` (requerido)**: Identificação da instância de orquestração.
* **`show-input` (opcional)**: Se for `true` definida, a resposta contém a entrada da função. O valor predefinido é `false`.
* **`show-output` (opcional)**: Se for `true` definida, a resposta contém a saída da função. O valor predefinido é `false`.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em [host.js,](durable-functions-bindings.md#host-json)utilizando durableTask:HubName.

O seguinte comando recupera o estatuto (incluindo entrada e saída) de um caso com uma identificação de instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Assume que está a executar o `func` comando a partir do diretório de raiz da aplicação de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Pode usar o `durable get-history` comando para recuperar a história de uma instância de orquestração. São necessários os seguintes parâmetros:

* **`id` (requerido)**: Identificação da instância de orquestração.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em host.js, utilizando durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consulta de todas as instâncias

Em vez de consultar um caso na sua orquestração de cada vez, pode achar mais eficiente questioná-los todos ao mesmo tempo.

Pode utilizar o método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusall--) (JavaScript) ou `get_status_all` (Python) para consultar os estatutos de todas as instâncias de orquestração. Em .NET, pode passar um `CancellationToken` objeto no caso de querer cancelá-lo. O método devolve uma lista de objetos que representam as instâncias de orquestração correspondentes aos parâmetros de consulta.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível consultar as instâncias diretamente, utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable get-instances` Tools. São necessários os seguintes parâmetros:

* **`top` (opcional)**: Este comando suporta a paging. Este parâmetro corresponde ao número de casos recuperados por pedido. O padrão é 10.
* **`continuation-token` (opcional)**: Um símbolo que indique qual a página ou secção de instâncias a recuperar. Cada `get-instances` execução devolve um símbolo ao próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em [host.js,](durable-functions-bindings.md#host-json)utilizando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Casos de consulta com filtros

E se não precisar de toda a informação que uma consulta de instância padrão pode fornecer? Por exemplo, e se estiveres apenas à procura do tempo de criação da orquestração, ou do estado do tempo de execução da orquestração? Pode reduzir a sua consulta aplicando filtros.

Utilize o método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) ou [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) para obter uma lista de instâncias de orquestração que correspondam a um conjunto de filtros predefinidos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Nas Ferramentas Principais de Funções Azure, também pode utilizar o `durable get-instances` comando com filtros. Além dos `top` `continuation-token` `connection-string-setting` parâmetros, e parâmetros acima `task-hub-name` referidos, pode utilizar três parâmetros de filtro `created-after` (, `created-before` e `runtime-status` .

* **`created-after` (opcional)**: Recuperar as instâncias criadas após esta data/hora (UTC). Datas formatadas ISO 8601 aceites.
* **`created-before` (opcional)**: Recuperar as instâncias criadas antes desta data/hora (UTC). Datas formatadas ISO 8601 aceites.
* **`runtime-status` (opcional)**: Recuperar as instâncias com um estatuto específico (por exemplo, correr ou completar). Pode fornecer vários status (separados pelo espaço).
* **`top` (opcional)** Número de ocorrências recuperadas por pedido. O padrão é 10.
* **`continuation-token` (opcional)**: Um símbolo que indique qual a página ou secção de instâncias a recuperar. Cada `get-instances` execução devolve um símbolo ao próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em [host.js,](durable-functions-bindings.md#host-json)utilizando durableTask:HubName.

Se não fornecer filtros `created-after` (, `created-before` ou , o comando simplesmente recupera `runtime-status` `top` casos, sem ter em conta o estado de funcionamento ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se tiver um caso de orquestração que está a demorar muito tempo a ser executado, ou se apenas precisar de o parar antes que se concretize por qualquer motivo, tem a opção de o encerrar.

Pode utilizar o `TerminateAsync` método (.NET), `terminate` (JavaScript) ou o `terminate` método (Python) da [ligação](durable-functions-bindings.md#orchestration-client) do cliente de orquestração para encerrar instâncias. Os dois parâmetros são um `instanceId` e um `reason` string, que são escritos para registos e para o estado da instância.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

Uma instância terminada acabará por transitar para o `Terminated` estado. No entanto, esta transição não acontecerá imediatamente. Em vez disso, a operação de fim de semana será interrompida no centro de tarefas, juntamente com outras operações, para esse caso. Você pode usar as APIs [de consulta de caso](#query-instances) para saber quando uma instância terminada realmente chegou ao `Terminated` estado.

> [!NOTE]
> A rescisão por exemplo não se propaga atualmente. As funções de atividade e as sub-orquestrações estão concluídas, independentemente de ter terminado a instância de orquestração que as chamou.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também pode terminar uma instância de orquestração diretamente, utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable terminate` Tools. São necessários os seguintes parâmetros:

* **`id` (obrigatório)**: Identificação da instância de orquestração para terminar.
* **`reason` (opcional)** Motivo para a rescisão.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em [host.js,](durable-functions-bindings.md#host-json)utilizando durableTask:HubName.

O seguinte comando termina uma instância de orquestração com um ID de 0ab8c55a666664d68a3a8b20b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que as suas funções de orquestrador possam esperar e ouvir eventos externos. Isto inclui [funções](durable-functions-overview.md#monitoring) de monitor e funções que aguardam a [interação humana.](durable-functions-overview.md#human)

Envie notificações de eventos para as instâncias em execução utilizando o `RaiseEventAsync` método (.NET) ou o `raiseEvent` método (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client). Os casos que podem lidar com estes eventos são aqueles que aguardam uma chamada para `WaitForExternalEvent` (.NET) ou cedem a uma `waitForExternalEvent` chamada (JavaScript).

Os parâmetros para `RaiseEventAsync` (.NET) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: A identificação única do caso.
* **Nome do Evento**: O nome do evento a enviar.
* **EventData**: Uma carga de pagamento serializável JSON para enviar para o caso.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Se não houver nenhuma instância de orquestração com o ID de instância especificado, a mensagem do evento é descartada. Se existir um caso mas ainda não estiver à espera do evento, o evento será armazenado no estado do caso até que esteja pronto para ser recebido e processado.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também pode elevar um evento a uma instância de orquestração diretamente, utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable raise-event` Tools. São necessários os seguintes parâmetros:

* **`id` (requerido)**: Identificação da instância de orquestração.
* **`event-name`**: Nome do evento a levantar.
* **`event-data` (opcional)** Dados a enviar para a instância de orquestração. Este pode ser o caminho para um ficheiro JSON, ou pode fornecer os dados diretamente na linha de comando.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurado em [host.js,](durable-functions-bindings.md#host-json)utilizando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguarde a conclusão da orquestração

Em orquestrações de longa data, talvez queira esperar e obter os resultados de uma orquestração. Nestes casos, também é útil ser capaz de definir um período de tempo na orquestração. Se o tempo limite for excedido, o estado da orquestração deve ser devolvido em vez dos resultados.

O `WaitForCompletionOrCreateCheckStatusResponseAsync` método (.NET) ou o `waitForCompletionOrCreateCheckStatusResponse` método (JavaScript) podem ser usados para obter a saída real de uma instância de orquestração sincronizadamente. Por padrão, estes métodos utilizam um valor predefinido de 10 segundos para `timeout` , e 1 segundo para `retryInterval` .  

Aqui está um exemplo da função de gatilho HTTP que demonstra como utilizar esta API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Ligue para a função com a seguinte linha. Utilize 2 segundos para o intervalo e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, existem dois casos:

* As instâncias de orquestração completam-se dentro do intervalo de tempo definido (neste caso 2 segundos), e a resposta é a produção real de instância de orquestração, entregue sincronizadamente:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* As instâncias de orquestração não podem ser completadas dentro do prazo definido, e a resposta é a padrão descrita na [descoberta do URL da API HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> O formato dos URLs webhook pode diferir, dependendo da versão do anfitrião Azure Functions que está a executar. O exemplo anterior é para o anfitrião Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs webhook de gestão HTTP

Pode utilizar um sistema externo para monitorizar ou elevar os eventos a uma orquestração. Os sistemas externos podem comunicar com funções duráveis através dos URLs webhook que fazem parte da resposta padrão descrita na [descoberta do URL da API](durable-functions-http-features.md#http-api-url-discovery)HTTP . Os URLs webhook podem, alternativamente, ser acedidos programáticamente utilizando a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client). Os `CreateHttpManagementPayload` métodos (.NET) ou `createHttpManagementPayload` (JavaScript) podem ser usados para obter um objeto serializável que contenha estes URLs webhook.

Os `CreateHttpManagementPayload` métodos (.NET) e `createHttpManagementPayload` JavaScript têm um parâmetro:

* **instanceId**: O ID único do caso.

Os métodos devolvem um objeto com as seguintes propriedades de corda:

* **Id**: A iD da orquestração (deve ser a mesma que a `InstanceId` entrada).
* **StatusQueryGetUri**: O URL de estado da instância de orquestração.
* **SendEventPostUri**: O URL "raise event" da instância de orquestração.
* **TerminatePostUri**: O URL "terminado" da instância de orquestração.
* **PurgaHistoryDeleteUri**: A URL "purgar a história" do exemplo da orquestração.

As funções podem enviar instâncias destes objetos para sistemas externos para monitorizar ou levantar eventos sobre as orquestrações correspondentes, como mostram os seguintes exemplos:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableActivityContext` em vez de , deve utilizar o atributo em vez do `IDurableActivityContext` `OrchestrationClient` `DurableClient` atributo, e deve utilizar o tipo `DurableOrchestrationClient` de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Rebobinar instâncias (pré-visualização)

Se tiver uma falha de orquestração por uma razão inesperada, pode *rebobinar* o caso para um estado anteriormente saudável, utilizando uma API construída para o efeito.

> [!NOTE]
> Esta API não se destina a substituir políticas adequadas de manipulação e retenção de erros. Pelo contrário, pretende-se que seja usada apenas em casos em que os casos de orquestração falham por razões inesperadas. Para obter mais informações sobre as políticas de manipulação de erros e de relagem, consulte o artigo [de tratamento de erros.](durable-functions-error-handling.md)

Utilize o `RewindAsync` método (.NET) ou `rewind` (JavaScript) da [ligação](durable-functions-bindings.md#orchestration-client) do cliente de orquestração para colocar a orquestração de volta no estado *de Execução.* Este método também irá refazer as falhas de execução de atividade ou sub-orquestração que causaram o fracasso da orquestração.

Por exemplo, digamos que tem um fluxo de trabalho envolvendo uma série de [aprovações humanas.](durable-functions-overview.md#human) Suponha que há uma série de funções de atividade que notificam alguém de que a sua aprovação é necessária, e aguardar a resposta em tempo real. Depois de todas as atividades de aprovação terem recebido respostas ou cronometrado, suponha que outra atividade falhe devido a uma configuração errada da aplicação, como uma cadeia de ligação de base de dados inválida. O resultado é uma falha de orquestração profunda no fluxo de trabalho. Com a `RewindAsync` API (.NET) ou `rewind` (JavaScript), um administrador de aplicação pode corrigir o erro de configuração e rebobinar a orquestração falhada de volta ao estado imediatamente antes da falha. Nenhum dos passos de interação humano precisa de ser re-aprovado, e a orquestração pode agora completar-se com sucesso.

> [!NOTE]
> A *função de retrocesso* não suporta rebobinar instâncias de orquestração que usam temporizadores duráveis.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Esta funcionalidade não é suportada em Python.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também pode rebobinar uma instância de orquestração diretamente utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable rewind` Tools. São necessários os seguintes parâmetros:

* **`id` (requerido)**: Identificação da instância de orquestração.
* **`reason` (opcional)** Motivo para rebobinar a instância de orquestração.
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. Por predefinição, é utilizado o nome do hub de tarefa no [host.jsno](durable-functions-bindings.md#host-json) ficheiro.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Purgar a história do caso

Para remover todos os dados associados a uma orquestração, pode expurgar a história do caso. Por exemplo, é melhor eliminar quaisquer linhas de tabela Azure e bolhas de mensagens grandes associadas a uma instância completa. Para tal, utilize o `PurgeInstanceHistoryAsync` método (.NET) ou `purgeInstanceHistory` (JavaScript) da [ligação](durable-functions-bindings.md#orchestration-client)do cliente de orquestração .

Este método tem duas sobrecargas. A primeira sobrecarga purga a história pela ID da instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Consulte [as instâncias iniciar](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

O exemplo seguinte mostra uma função desencadeada pelo temporizador que purga a história para todos os casos de orquestração que terminaram após o intervalo de tempo especificado. Neste caso, remove dados para todos os casos concluídos há 30 ou mais dias. Está programado para ser executado uma vez por dia, às 00:00:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O `purgeInstanceHistoryBy` método pode ser usado para purgar condicionalmente a história do exemplo em vários casos.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo visa a versão 2.x das Funções Duradouras. Na versão 1.x, use `orchestrationClient` em vez de `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Para que a operação de histórico de purga tenha êxito, o estado de funcionamento da instância-alvo deve ser **concluído,** **terminado** ou **falhado**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Pode expurgar a história de uma instância de orquestração utilizando o comando [Azure Functions Core](../functions-run-local.md) `durable purge-history` Tools. Semelhante ao segundo exemplo C# na secção anterior, purga a história para todos os casos de orquestração criados durante um intervalo de tempo especificado. Pode filtrar ainda mais as instâncias purgadas por estado de funcionação. O comando tem vários parâmetros:

* **`created-after` (opcional)**: Expurgar a história dos casos criados após esta data/hora (UTC). Datas formatadas ISO 8601 aceites.
* **`created-before` (opcional)**: Expurgar a história dos casos criados antes desta data/hora (UTC). Datas formatadas ISO 8601 aceites.
* **`runtime-status` (opcional)**: Expurgar a história dos casos com um estatuto específico (por exemplo, correr ou completar). Pode fornecer vários status (separados pelo espaço).
* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. Por predefinição, é utilizado o nome do hub de tarefa no [host.jsno](durable-functions-bindings.md#host-json) ficheiro.

O seguinte comando elimina a história de todas as instâncias falhadas criadas antes de 14 de novembro de 2018 às 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Excluir um centro de tarefas

Utilizando o comando [Azure Functions Core Tools,](../functions-run-local.md) `durable delete-task-hub` pode eliminar todos os artefactos de armazenamento associados a um determinado centro de tarefas, incluindo mesas de armazenamento Azure, filas e bolhas. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)**- Nome da definição de aplicação que contém o fio de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do centro de tarefas de funções duráveis a utilizar. Por predefinição, é utilizado o nome do hub de tarefa no [host.jsno](durable-functions-bindings.md#host-json) ficheiro.

O seguinte comando elimina todos os dados de armazenamento Azure associados ao `UserTest` centro de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referência de API HTTP incorporada para gestão de exemplo](durable-functions-http-api.md)
