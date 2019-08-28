---
title: Gerenciar instâncias no Durable Functions-Azure
description: Saiba como gerenciar instâncias na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 6548b84f9599116aaa5055324bfa4625ea621ec3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087254"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias no Durable Functions no Azure

Se você estiver usando a extensão [Durable Functions](durable-functions-overview.md) para Azure functions, ou quiser começar a fazer isso, verifique se está obtendo o melhor uso dele. Você pode otimizar suas instâncias de orquestração de Durable Functions aprendendo mais sobre como gerenciá-las. Este artigo apresenta os detalhes de cada operação de gerenciamento de instância.

Você pode iniciar e encerrar instâncias, por exemplo, e pode consultar instâncias, incluindo a capacidade de consultar todas as instâncias e instâncias de consulta com filtros. Além disso, você pode enviar eventos para instâncias, aguardar a conclusão da orquestração e recuperar URLs de webhook de gerenciamento HTTP. Este artigo aborda outras operações de gerenciamento também, incluindo instâncias de rebobinamento, limpeza de histórico de instância e exclusão de um hub de tarefas.

No Durable Functions, você tem opções de como deseja implementar cada uma dessas operações de gerenciamento. Este artigo fornece exemplos que usam o [Azure Functions Core Tools](../functions-run-local.md) para .net (C#) e JavaScript.

## <a name="start-instances"></a>Iniciar instâncias

É importante poder iniciar uma instância de orquestração. Isso é feito normalmente quando você está usando uma associação de Durable Functions no gatilho de outra função.

O método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) `startNew` `DurableOrchestrationClient` ou no (JavaScript) inicia uma nova instância. Você adquire instâncias dessa classe usando a `orchestrationClient` associação. Internamente, esse método enfileira uma mensagem na fila de controle, que dispara o início de uma função com o nome especificado que usa a `orchestrationTrigger` Associação de gatilho.

Esta operação assíncrona é concluída quando o processo de orquestração é agendado com êxito. O processo de orquestração deve começar dentro de 30 segundos. Se demorar mais, você verá um `TimeoutException`.

> [!WARNING]
> Ao desenvolver localmente em JavaScript `WEBSITE_HOSTNAME` , defina a variável de ambiente como `localhost:<port>` (por exemplo `localhost:7071`,) para usar métodos `DurableOrchestrationClient`em. Para obter mais informações sobre esse requisito, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Os parâmetros para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **Entrada**: Qualquer dado serializável em JSON que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: Adicional A ID exclusiva da instância. Se você não especificar esse parâmetro, o método usará uma ID aleatória.

Veja um exemplo simples C# :

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

Os parâmetros para `startNew` são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **InstanceId**: Adicional A ID exclusiva da instância. Se você não especificar esse parâmetro, o método usará uma ID aleatória.
* **Entrada**: Adicional Qualquer dado serializável em JSON que deve ser passado como a entrada para a função de orquestrador.

Este é um exemplo simples de JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Use um identificador aleatório para a ID da instância. Isso ajuda a garantir uma distribuição de carga igual quando você estiver dimensionando funções de orquestrador em várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID deve vir de uma fonte externa, ou quando você estiver implementando o padrão de [orquestrador singleton](durable-functions-singletons.md) .

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode iniciar uma instância diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . Ele usa os seguintes parâmetros:

* (obrigatório): **`function-name`** Nome da função a ser iniciada.
* (opcional): **`input`** Entrada para a função, seja embutida ou por meio de um arquivo JSON. Para arquivos, adicione um prefixo ao caminho para o arquivo com `@`, `@path/to/file.json`como.
* (opcional): **`id`** ID da instância de orquestração. Se você não especificar esse parâmetro, o comando usará um GUID aleatório.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é AzureWebJobsStorage.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. O padrão é DurableFunctionsHub. Você também pode definir isso em [host. JSON](durable-functions-bindings.md#host-json) usando DurableTask: HubName.

> [!NOTE]
> Os comandos de ferramentas principais pressupõem que você está executando-os do diretório raiz de um aplicativo de funções. Se você fornecer explicitamente os `connection-string-setting` parâmetros `task-hub-name` e, poderá executar os comandos de qualquer diretório. Embora seja possível executar esses comandos sem um host de aplicativo de funções em execução, você pode achar que não é possível observar alguns efeitos, a menos que o host esteja em execução. Por exemplo, o `start-new` comando enfileira uma mensagem de início no Hub de tarefas de destino, mas a orquestração não é realmente executada, a menos que haja um processo de host de aplicativo de funções em execução que possa processar a mensagem.

O comando a seguir inicia a função chamada HelloWorld e passa o conteúdo do arquivo `counter-data.json` para ela:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte de seu esforço para gerenciar suas orquestrações, você provavelmente precisará reunir informações sobre o status de uma instância de orquestração (por exemplo, se ela foi concluída normalmente ou falhou).

O método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) ou o `getStatus` método na `DurableOrchestrationClient` classe (JavaScript) consulta o status de uma instância de orquestração.

Ele usa `instanceId` (obrigatório), `showHistory` (opcional `showHistoryOutput` ), (opcional) e `showInput` (opcional, somente .net) como parâmetros.

* **`showHistory`** : Se definido como `true`, a resposta conterá o histórico de execução.
* **`showHistoryOutput`** : Se definido como `true`, o histórico de execução conterá saídas de atividade.
* **`showInput`** : Se definido como `false`, a resposta não conterá a entrada da função. O valor predefinido é `true`. (Somente .NET)

O método retorna um objeto JSON com as seguintes propriedades:

* **Nome**: O nome da função de orquestrador.
* **InstanceId**: A ID da instância da orquestração (deve ser a mesma que a `instanceId` entrada).
* **Createdtime**: A hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: A hora em que a orquestração foi verificada por último.
* **Entrada**: A entrada da função como um valor JSON. Esse campo não será preenchido `showInput` se for false.
* **CustomStatus**: Status de orquestração personalizado no formato JSON.
* **Saída**: A saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo do encerramento (se houver).
* **RuntimeStatus**: Um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não começou a ser executada.
  * **Em execução**: A instância começou a ser executada.
  * **Concluído**: A instância foi concluída normalmente.
  * **ContinuedAsNew**: A instância foi reiniciada com um novo histórico. Esse é um estado transitório.
  * **Com falha**: A instância falhou com um erro.
  * **Encerrado**: A instância foi interrompida abruptamente.
* **Histórico**: O histórico de execução da orquestração. Esse campo só será populado se `showHistory` for definido como. `true`

Esse método retornará `null` se a instância não existir ou ainda não tiver iniciado a execução.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível obter o status de uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Ele usa os seguintes parâmetros:

* (obrigatório): **`id`** ID da instância de orquestração.
* (opcional): **`show-input`** Se definido como `true`, a resposta conterá a entrada da função. O valor predefinido é `false`.
* (opcional): **`show-output`** Se definido como `true`, a resposta conterá a saída da função. O valor predefinido é `false`.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir recupera o status (incluindo entrada e saída) de uma instância com uma ID de instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Ele pressupõe que você está executando o `func` comando do diretório raiz do aplicativo de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Você pode usar o `durable get-history` comando para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* (obrigatório): **`id`** ID da instância de orquestração.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em host. JSON, usando durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consultar todas as instâncias

Em vez de consultar uma instância em sua orquestração por vez, talvez você ache mais eficiente consultar todas elas de uma só vez.

Você pode usar o `GetStatusAsync` método (.net) `getStatusAll` ou (JavaScript) para consultar os status de todas as instâncias de orquestração. No .net, você pode passar um `CancellationToken` objeto caso queira cancelá-lo. O método retorna objetos com as mesmas propriedades que o `GetStatusAsync` método com parâmetros.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível consultar instâncias diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . Ele usa os seguintes parâmetros:

* (opcional): **`top`** Este comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* (opcional): **`continuation-token`** Um token para indicar qual página ou seção de instâncias recuperar. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instâncias de consulta com filtros

E se você não precisar realmente de todas as informações que uma consulta de instância padrão pode fornecer? Por exemplo, e se você estiver apenas procurando o tempo de criação da orquestração ou o status do tempo de execução Orchestration? Você pode restringir sua consulta aplicando filtros.

Use o `GetStatusAsync` método (.net) `getStatusBy` ou (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

No Azure Functions Core Tools, você também pode usar o `durable get-instances` comando com filtros. Além `top`dos `connection-string-setting``created-after` `runtime-status`parâmetros, `continuation-token`, `created-before`e mencionados anteriormente, você pode usar três parâmetros de filtro (,, e). `task-hub-name`

* (opcional): **`created-after`** Recupere as instâncias criadas após esta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* (opcional): **`created-before`** Recupere as instâncias criadas antes desta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* (opcional): **`runtime-status`** Recupere as instâncias com um status específico (por exemplo, executando ou concluído). Pode fornecer vários status (separados por espaço).
* (opcional): **`top`** Número de instâncias recuperadas por solicitação. O padrão é 10.
* (opcional): **`continuation-token`** Um token para indicar qual página ou seção de instâncias recuperar. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Se você não fornecer filtros`created-after`(, `created-before`ou `runtime-status`), o comando simplesmente recupera `top` instâncias, sem considerar o status do tempo de execução ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se você tiver uma instância de orquestração demorando muito para ser executada ou apenas precisar interrompê-la antes de ser concluída por qualquer motivo, você terá a opção de finalizá-la.

Você pode usar o método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) ou o `terminate` método da `DurableOrchestrationClient` classe (JavaScript). Os dois parâmetros são um `instanceId` e uma `reason` cadeia de caracteres, que são gravados nos logs e no status da instância. Uma instância finalizada interrompe a execução assim que atinge o próximo `await` ponto (.net) `yield` ou (JavaScript) ou termina imediatamente se já estiver em um `await` ou `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> O encerramento da instância não se propaga no momento. As funções de atividade e as suborquestrações são executadas até a conclusão, independentemente de você ter encerrado a instância de orquestração que as chamou.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode encerrar uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . Ele usa os seguintes parâmetros:

* (obrigatório): **`id`** ID da instância de orquestração a ser encerrada.
* (opcional): **`reason`** Motivo do encerramento.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir encerra uma instância de orquestração com uma ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que suas funções de orquestrador possam esperar e ouvir eventos externos. Isso inclui [funções](durable-functions-concepts.md#monitoring) e funções de monitor que estão aguardando a [interação humana](durable-functions-concepts.md#human).

Envie notificações de eventos para executar instâncias usando o método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) ou o `raiseEvent` método da `DurableOrchestrationClient` classe (JavaScript). As instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET `waitForExternalEvent` ) ou (JavaScript).

Os parâmetros para [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: A ID exclusiva da instância.
* **Eventoname**: O nome do evento a ser enviado.
* **EVENTDATA**: Uma carga serializável em JSON a ser enviada para a instância.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Se não houver nenhuma instância de orquestração com a ID de instância especificada ou se a instância não estiver aguardando o nome do evento especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode gerar um evento para uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . Ele usa os seguintes parâmetros:

* (obrigatório): **`id`** ID da instância de orquestração.
* (opcional): **`event-name`** Nome do evento a ser gerado. A predefinição é `$"Event_{RandomGUID}"`.
* (opcional): **`event-data`** Dados a serem enviados para a instância de orquestração. Esse pode ser o caminho para um arquivo JSON ou você pode fornecer os dados diretamente na linha de comando.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

Em orquestrações de longa execução, talvez você queira esperar e obter os resultados de uma orquestração. Nesses casos, também é útil ser capaz de definir um período de tempo limite na orquestração. Se o tempo limite for excedido, o estado da orquestração deverá ser retornado em vez dos resultados.

A classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expõe uma API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) no .net. Você pode usar essa API para obter a saída real de uma instância de orquestração de forma síncrona. No JavaScript, a `DurableOrchestrationClient` classe expõe uma `waitForCompletionOrCreateCheckStatusResponse` API para a mesma finalidade. Quando não estiverem definidas, os métodos usarão um valor padrão de 10 segundos para `timeout`e 1 segundo para. `retryInterval`  

Aqui está um exemplo de função de gatilho HTTP que demonstra como usar essa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Chame a função com a linha a seguir. Use 2 segundos para o tempo limite e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração são concluídas dentro do tempo limite definido (neste caso, 2 segundos) e a resposta é a saída real da instância de orquestração, entregue de forma síncrona:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* As instâncias de orquestração não podem ser concluídas dentro do tempo limite definido e a resposta é a padrão descrita na [descoberta de URL da API http](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> O formato das URLs de webhook pode ser diferente, dependendo da versão do host de Azure Functions que você está executando. O exemplo anterior é para o host Azure Functions 2. x.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs de webhook de gerenciamento HTTP

Você pode usar um sistema externo para monitorar ou gerar eventos para uma orquestração. Os sistemas externos podem se comunicar com Durable Functions por meio de URLs de webhook que fazem parte da resposta padrão descrita na [descoberta de URL da API http](durable-functions-http-api.md). No entanto, as URLs de webhook também podem ser acessadas programaticamente no cliente de orquestração ou em uma função de atividade. Faça isso usando o método [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) ou o `createHttpManagementPayload` método da `DurableOrchestrationClient` classe (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` ter um parâmetro:

* **instanceId**: A ID exclusiva da instância.

Os métodos retornam uma instância de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.net) ou um objeto (JavaScript), com as seguintes propriedades de cadeia de caracteres:

* **ID**: A ID da instância da orquestração (deve ser a mesma que a `InstanceId` entrada).
* **StatusQueryGetUri**: A URL de status da instância de orquestração.
* **SendEventPostUri**: A URL "gerar evento" da instância de orquestração.
* **TerminatePostUri**: A URL "Terminate" da instância de orquestração.
* **RewindPostUri**: A URL de "retrocesso" da instância de orquestração.

As funções de atividade podem enviar uma instância desses objetos a sistemas externos para monitorar ou gerar eventos para uma orquestração:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

## <a name="rewind-instances-preview"></a>Rebobinar instâncias (visualização)

Se você tiver uma falha de orquestração por um motivo inesperado , poderá retroceder a instância para um estado de integridade anterior usando uma API criada para essa finalidade.

> [!NOTE]
> Essa API não se destina a ser uma substituição para o tratamento de erros e as políticas de repetição adequados. Em vez disso, ele se destina a ser usado apenas nos casos em que as instâncias de orquestração falham por motivos inesperados. Para obter mais detalhes sobre o tratamento de erros e as políticas de repetição, consulte o tópico [tratamento de erros](durable-functions-error-handling.md) .

Use a API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.net) `rewindAsync` ou (JavaScript) para colocar a orquestração de volta no estado de *execução* . Execute novamente a atividade ou as falhas de execução de suborquestração que causaram a falha de orquestração.

Por exemplo, digamos que você tenha um fluxo de trabalho que envolva uma série de [aprovações humanas](durable-functions-concepts.md#human). Suponha que haja uma série de funções de atividade que notifique alguém de que sua aprovação é necessária e aguarde a resposta em tempo real. Depois que todas as atividades de aprovação receberam respostas ou atingiram o tempo limite, suponha que outra atividade falhe devido a uma configuração incorreta do aplicativo, como uma cadeia de conexão de banco de dados inválida. O resultado é uma falha de orquestração profunda no fluxo de trabalho. Com a `RewindAsync` API (.net) `rewindAsync` ou (JavaScript), um administrador de aplicativos pode corrigir o erro de configuração e rebobinar a orquestração com falha de volta para o estado imediatamente antes da falha. Nenhuma das etapas de interação humana precisa ser reaprovada e a orquestração agora pode ser concluída com êxito.

> [!NOTE]
> O recurso de *retrocesso* não dá suporte à rebobinagem de instâncias de orquestração que usam temporizadores duráveis.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode rebobinar uma instância de orquestração diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . Ele usa os seguintes parâmetros:

* (obrigatório): **`id`** ID da instância de orquestração.
* (opcional): **`reason`** Motivo para retroceder a instância de orquestração.
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar histórico de instância

Para remover todos os dados associados a uma orquestração, você pode limpar o histórico de instâncias. Por exemplo, talvez você queira se livrar de linhas de tabela do Azure e blobs de mensagens grandes, se existirem. Para fazer isso, use a API [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) .

> [!NOTE]
> A `PurgeInstanceHistoryAsync` API está atualmente disponível apenas para C#o.

 O método tem duas sobrecargas. A primeira limpa o histórico pela ID da instância de orquestração:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

O segundo exemplo mostra uma função disparada por temporizador que limpa o histórico de todas as instâncias de orquestração concluídas após o intervalo de tempo especificado. Nesse caso, ele remove dados de todas as instâncias concluídas 30 ou mais dias atrás. Ele está agendado para ser executado uma vez por dia, às 12:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> Para que o processo de função acionada por tempo tenha êxito, o status do tempo de execução deve ser **concluído**, **terminado**ou **com falha**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você pode limpar o histórico de uma instância de orquestração usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Semelhante ao segundo C# exemplo na seção anterior, ele limpa o histórico de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. Você pode filtrar ainda mais as instâncias limpas por status de tempo de execução. O comando tem vários parâmetros:

* (opcional): **`created-after`** Limpar o histórico de instâncias criadas após esta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* (opcional): **`created-before`** Limpar o histórico de instâncias criadas antes desta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* (opcional): **`runtime-status`** Limpe o histórico de instâncias com um status específico (por exemplo, em execução ou concluído). Pode fornecer vários status (separados por espaço).
* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir exclui o histórico de todas as instâncias com falha criadas antes de 14 de novembro de 2018 às 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Excluir um hub de tarefas

Usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` , você pode excluir todos os artefatos de armazenamento associados a um determinado Hub de tarefas. Isso inclui tabelas, filas e BLOBs do armazenamento do Azure. O comando tem dois parâmetros:

* (opcional): **`connection-string-setting`** Nome da configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* (opcional): **`task-hub-name`** Nome do hub de tarefas Durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir exclui todos os dados do armazenamento do `UserTest` Azure associados ao Hub de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como usar as APIs HTTP para o gerenciamento de instâncias](durable-functions-http-api.md)
