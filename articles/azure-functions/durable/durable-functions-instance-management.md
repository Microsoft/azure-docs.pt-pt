---
title: Gerenciar instâncias no Durable Functions-Azure
description: Saiba como gerenciar instâncias na extensão de Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ab9cc9b093008730d175fa3fde4391f9de236a84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231386"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias no Durable Functions no Azure

Se você estiver usando a extensão [Durable Functions](durable-functions-overview.md) para Azure functions, ou quiser começar a fazer isso, verifique se está obtendo o melhor uso dele. Você pode otimizar suas instâncias de orquestração de Durable Functions aprendendo mais sobre como gerenciá-las. Este artigo apresenta os detalhes de cada operação de gerenciamento de instância.

Você pode iniciar e encerrar instâncias, por exemplo, e pode consultar instâncias, incluindo a capacidade de consultar todas as instâncias e instâncias de consulta com filtros. Além disso, você pode enviar eventos para instâncias, aguardar a conclusão da orquestração e recuperar URLs de webhook de gerenciamento HTTP. Este artigo aborda outras operações de gerenciamento também, incluindo instâncias de rebobinamento, limpeza de histórico de instância e exclusão de um hub de tarefas.

No Durable Functions, você tem opções de como deseja implementar cada uma dessas operações de gerenciamento. Este artigo fornece exemplos que usam o [Azure Functions Core Tools](../functions-run-local.md) para .net (C#) e JavaScript.

## <a name="start-instances"></a>Iniciar instâncias

É importante poder iniciar uma instância de orquestração. Isso é feito normalmente quando você está usando uma associação de Durable Functions no gatilho de outra função.

O método `StartNewAsync` (.NET) ou `startNew` (JavaScript) na [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) inicia uma nova instância. Internamente, esse método enfileira uma mensagem na fila de controle, que dispara o início de uma função com o nome especificado que usa a [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

Esta operação assíncrona é concluída quando o processo de orquestração é agendado com êxito.

Os parâmetros para iniciar uma nova instância de orquestração são os seguintes:

* **Nome**: o nome da função de orquestrador a ser agendada.
* **Entrada**: qualquer dado serializável em JSON que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se você não especificar esse parâmetro, o método usará uma ID aleatória.

> [!TIP]
> Use um identificador aleatório para a ID da instância. As IDs de instância aleatórias ajudam a garantir uma distribuição de carga igual quando você estiver dimensionando funções de orquestrador em várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID deve vir de uma fonte externa, ou quando você estiver implementando o padrão de [orquestrador singleton](durable-functions-singletons.md) .

O código a seguir é uma função de exemplo que inicia uma nova instância de orquestração:

### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript"></a>Javascript

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode iniciar uma instância diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Ele usa os seguintes parâmetros:

* **`function-name` (obrigatório)** : o nome da função a ser iniciada.
* **`input` (opcional)** : entrada para a função, seja embutida ou por meio de um arquivo JSON. Para arquivos, adicione um prefixo ao caminho para o arquivo com `@`, como `@path/to/file.json`.
* **`id` (opcional)** : ID da instância de orquestração. Se você não especificar esse parâmetro, o comando usará um GUID aleatório.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. O padrão é DurableFunctionsHub. Você também pode definir isso em [host. JSON](durable-functions-bindings.md#host-json) usando DurableTask: HubName.

> [!NOTE]
> Os comandos de ferramentas principais pressupõem que você está executando-os do diretório raiz de um aplicativo de funções. Se você fornecer explicitamente os parâmetros `connection-string-setting` e `task-hub-name`, poderá executar os comandos de qualquer diretório. Embora seja possível executar esses comandos sem um host de aplicativo de funções em execução, você pode achar que não é possível observar alguns efeitos, a menos que o host esteja em execução. Por exemplo, o comando `start-new` enfileira uma mensagem de início no Hub de tarefas de destino, mas a orquestração não é realmente executada, a menos que haja um processo de host de aplicativo de funções em execução que possa processar a mensagem.

O comando a seguir inicia a função chamada HelloWorld e passa o conteúdo do arquivo `counter-data.json` a ela:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte de seu esforço para gerenciar suas orquestrações, você provavelmente precisará reunir informações sobre o status de uma instância de orquestração (por exemplo, se ela foi concluída normalmente ou falhou).

O `GetStatusAsync` (.NET) ou o método `getStatus` (JavaScript) na [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) consulta o status de uma instância de orquestração.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional), `showHistoryOutput` (opcional) e `showInput` (opcional) como parâmetros.

* **`showHistory`** : se for definido como `true`, a resposta conterá o histórico de execução.
* **`showHistoryOutput`** : se for definido como `true`, o histórico de execução conterá saídas de atividade.
* **`showInput`** : se definido como `false`, a resposta não conterá a entrada da função. O valor padrão é `true`.

O método retorna um objeto com as seguintes propriedades:

* **Nome**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância da orquestração (deve ser a mesma que a entrada de `instanceId`).
* **Createdtime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração foi verificada por último.
* **Input**: a entrada da função como um valor JSON. Esse campo não será preenchido se `showInput` for false.
* **CustomStatus**: status de orquestração personalizado no formato JSON.
* **Saída**: a saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo do encerramento (se houver).
* **RuntimeStatus**: um dos seguintes valores:
  * **Pendente**: a instância foi agendada, mas ainda não começou a ser executada.
  * **Em execução**: a instância começou a ser executada.
  * **Concluído**: a instância foi concluída normalmente.
  * **ContinuedAsNew**: a instância reiniciou-se com um novo histórico. Esse estado é um estado transitório.
  * **Falha**: a instância falhou com um erro.
  * **Encerrado**: a instância foi interrompida abruptamente.
* **Histórico**: o histórico de execução da orquestração. Esse campo só será populado se `showHistory` for definido como `true`.

Esse método retornará `null` (.NET) ou `undefined` (JavaScript) se a instância não existir.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

Também é possível obter o status de uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`show-input` (opcional)** : se for definido como `true`, a resposta conterá a entrada da função. O valor padrão é `false`.
* **`show-output` (opcional)** : se definido como `true`, a resposta conterá a saída da função. O valor padrão é `false`.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir recupera o status (incluindo entrada e saída) de uma instância com uma ID de instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Ele pressupõe que você esteja executando o comando `func` do diretório raiz do aplicativo de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Você pode usar o comando `durable get-history` para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em host. JSON, usando durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consultar todas as instâncias

Em vez de consultar uma instância em sua orquestração por vez, talvez você ache mais eficiente consultar todas elas de uma só vez.

Você pode usar o método `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) para consultar os status de todas as instâncias de orquestração. No .NET, você pode passar um objeto `CancellationToken` caso queira cancelá-lo. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

Também é possível consultar instâncias diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Ele usa os seguintes parâmetros:

* **`top` (opcional)** : esse comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)** : um token para indicar qual página ou seção de instâncias recuperar. Cada execução de `get-instances` retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instâncias de consulta com filtros

E se você não precisar realmente de todas as informações que uma consulta de instância padrão pode fornecer? Por exemplo, e se você estiver apenas procurando o tempo de criação da orquestração ou o status do tempo de execução Orchestration? Você pode restringir sua consulta aplicando filtros.

Use o método `GetStatusAsync` (.NET) ou `getStatusBy` (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

No Azure Functions Core Tools, você também pode usar o comando `durable get-instances` com filtros. Além dos parâmetros `top`, `continuation-token`, `connection-string-setting`e `task-hub-name` mencionados anteriormente, você pode usar três parâmetros de filtro (`created-after`, `created-before`e `runtime-status`).

* **`created-after` (opcional)** : recuperar as instâncias criadas após esta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* **`created-before` (opcional)** : recuperar as instâncias criadas antes desta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* **`runtime-status` (opcional)** : recuperar as instâncias com um status específico (por exemplo, executando ou concluído). Pode fornecer vários status (separados por espaço).
* **`top` (opcional)** : número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)** : um token para indicar qual página ou seção de instâncias recuperar. Cada execução de `get-instances` retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Se você não fornecer filtros (`created-after`, `created-before`ou `runtime-status`), o comando simplesmente recuperará `top` instâncias, sem considerar o status do tempo de execução ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se você tiver uma instância de orquestração demorando muito para ser executada ou apenas precisar interrompê-la antes de ser concluída por qualquer motivo, você terá a opção de finalizá-la.

Você pode usar o `TerminateAsync` (.NET) ou o método `terminate` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para encerrar instâncias. Os dois parâmetros são um `instanceId` e uma `reason` cadeia de caracteres, que são gravados nos logs e no status da instância. Uma instância encerrada interrompe a execução assim que alcança o próximo ponto de `await` (.NET) ou `yield` (JavaScript) ou termina imediatamente se já estiver em um `await` ou `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

Você também pode encerrar uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable terminate`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração a ser encerrada.
* **`reason` (opcional)** : motivo da rescisão.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir encerra uma instância de orquestração com uma ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que suas funções de orquestrador possam esperar e ouvir eventos externos. Isso inclui [funções](durable-functions-overview.md#monitoring) e funções de monitor que estão aguardando a [interação humana](durable-functions-overview.md#human).

Envie notificações de eventos para executar instâncias usando o método `RaiseEventAsync` (.NET) ou o método `raiseEvent` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). As instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para `WaitForExternalEvent` (.NET) ou resultando em uma chamada de `waitForExternalEvent` (JavaScript).

Os parâmetros para `RaiseEventAsync` (.NET) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EVENTDATA**: uma carga serializável em JSON a ser enviada para a instância.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a ID de instância especificada, a mensagem de evento será descartada. Se uma instância existir, mas ainda não estiver aguardando o evento, o evento será armazenado no estado da instância até que esteja pronto para ser recebido e processado.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode gerar um evento para uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`event-name`** : o nome do evento a ser gerado.
* **`event-data` (opcional)** : dados a serem enviados para a instância de orquestração. Esse pode ser o caminho para um arquivo JSON ou você pode fornecer os dados diretamente na linha de comando.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. A predefinição é `DurableFunctionsHub`. Ele também pode ser definido em [host. JSON](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

Em orquestrações de longa execução, talvez você queira esperar e obter os resultados de uma orquestração. Nesses casos, também é útil ser capaz de definir um período de tempo limite na orquestração. Se o tempo limite for excedido, o estado da orquestração deverá ser retornado em vez dos resultados.

O método `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) ou `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) pode ser usado para obter a saída real de uma instância de orquestração de forma síncrona. Por padrão, esses métodos usam um valor padrão de 10 segundos para `timeout`e 1 segundo para `retryInterval`.  

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
        Date: Thu, 14 Dec 2018 06:14:29 GMT
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
> O formato das URLs de webhook pode ser diferente, dependendo da versão do host de Azure Functions que você está executando. O exemplo anterior é para o host Azure Functions 2,0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs de webhook de gerenciamento HTTP

Você pode usar um sistema externo para monitorar ou gerar eventos para uma orquestração. Os sistemas externos podem se comunicar com Durable Functions por meio de URLs de webhook que fazem parte da resposta padrão descrita na [descoberta de URL da API http](durable-functions-http-features.md#http-api-url-discovery). Como alternativa, as URLs de webhook podem ser acessadas programaticamente usando a [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). Os métodos `CreateHttpManagementPayload` (.NET) ou `createHttpManagementPayload` (JavaScript) podem ser usados para obter um objeto serializável que contenha essas URLs de webhook.

Os métodos `CreateHttpManagementPayload` (.NET) e `createHttpManagementPayload` (JavaScript) têm um parâmetro:

* **InstanceId**: a ID exclusiva da instância.

Os métodos retornam um objeto com as seguintes propriedades de cadeia de caracteres:

* **ID**: a ID da instância da orquestração (deve ser a mesma que a entrada de `InstanceId`).
* **StatusQueryGetUri**: a URL de status da instância de orquestração.
* **SendEventPostUri**: a URL "gerar evento" da instância de orquestração.
* **TerminatePostUri**: a URL "Terminate" da instância de orquestração.
* **PurgeHistoryDeleteUri**: a URL "limpar histórico" da instância de orquestração.

O Functions pode enviar instâncias desses objetos a sistemas externos para monitorar ou gerar eventos nas orquestrações correspondentes, conforme mostrado nos exemplos a seguir:

### <a name="c"></a>C#

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
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableActivityContext` em vez de `IDurableActivityContext`, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e você deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

Se você tiver uma falha de orquestração por um motivo inesperado, poderá *retroceder* a instância para um estado de integridade anterior usando uma API criada para essa finalidade.

> [!NOTE]
> Essa API não se destina a ser uma substituição para o tratamento de erros e as políticas de repetição adequados. Em vez disso, ele se destina a ser usado apenas nos casos em que as instâncias de orquestração falham por motivos inesperados. Para obter mais informações sobre tratamento de erros e políticas de repetição, consulte o artigo [tratamento de erros](durable-functions-error-handling.md) .

Use o método `RewindAsync` (.NET) ou `rewind` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para colocar a orquestração de volta no estado de *execução* . Esse método também executará novamente as falhas de execução da atividade ou da suborquestração que causaram a falha de orquestração.

Por exemplo, digamos que você tenha um fluxo de trabalho que envolva uma série de [aprovações humanas](durable-functions-overview.md#human). Suponha que haja uma série de funções de atividade que notifique alguém de que sua aprovação é necessária e aguarde a resposta em tempo real. Depois que todas as atividades de aprovação receberam respostas ou atingiram o tempo limite, suponha que outra atividade falhe devido a uma configuração incorreta do aplicativo, como uma cadeia de conexão de banco de dados inválida. O resultado é uma falha de orquestração profunda no fluxo de trabalho. Com a API `RewindAsync` (.NET) ou `rewind` (JavaScript), um administrador de aplicativos pode corrigir o erro de configuração e rebobinar a orquestração com falha de volta para o estado imediatamente antes da falha. Nenhuma das etapas de interação humana precisa ser aprovada novamente e a orquestração agora pode ser concluída com êxito.

> [!NOTE]
> O recurso de *retrocesso* não dá suporte à rebobinagem de instâncias de orquestração que usam temporizadores duráveis.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

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

Você também pode rebobinar uma instância de orquestração diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable rewind`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`reason` (opcional)** : motivo para retroceder a instância de orquestração.
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. Por padrão, o nome do hub de tarefas no arquivo [host. JSON](durable-functions-bindings.md#host-json) é usado.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar histórico de instância

Para remover todos os dados associados a uma orquestração, você pode limpar o histórico de instâncias. Por exemplo, talvez você queira excluir todas as linhas da tabela do Azure e blobs de mensagens grandes associadas a uma instância concluída. Para fazer isso, use o método `PurgeInstanceHistoryAsync` (.NET) ou `purgeInstanceHistory` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client).

Esse método tem duas sobrecargas. A primeira sobrecarga limpa o histórico pela ID da instância de orquestração:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

O exemplo a seguir mostra uma função disparada por temporizador que limpa o histórico de todas as instâncias de orquestração concluídas após o intervalo de tempo especificado. Nesse caso, ele remove dados de todas as instâncias concluídas 30 ou mais dias atrás. Ele está agendado para ser executado uma vez por dia, às 12:

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
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

**JavaScript** O método `purgeInstanceHistoryBy` pode ser usado para limpar condicionalmente o histórico de instâncias para várias instâncias.

> [!NOTE]
> Para que a operação limpar histórico seja concluída com êxito, o status de tempo de execução da instância de destino deve ser **concluído**, **encerrado**ou **com falha**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você pode limpar o histórico de uma instância de orquestração usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`. Semelhante ao segundo C# exemplo na seção anterior, ele limpa o histórico de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. Você pode filtrar ainda mais as instâncias limpas por status de tempo de execução. O comando tem vários parâmetros:

* **`created-after` (opcional)** : limpar o histórico de instâncias criadas após esta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* **`created-before` (opcional)** : limpar o histórico de instâncias criadas antes desta data/hora (UTC). Data e hora formatadas ISO 8601 aceitas.
* **`runtime-status` (opcional)** : limpar o histórico de instâncias com um status específico (por exemplo, executando ou concluído). Pode fornecer vários status (separados por espaço).
* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. Por padrão, o nome do hub de tarefas no arquivo [host. JSON](durable-functions-bindings.md#host-json) é usado.

O comando a seguir exclui o histórico de todas as instâncias com falha criadas antes de 14 de novembro de 2018 às 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Excluir um hub de tarefas

Usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub`, você pode excluir todos os artefatos de armazenamento associados a um hub de tarefas específico, incluindo tabelas de armazenamento do Azure, filas e blobs. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)** : o nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nome do hub de tarefas de durable Functions a ser usado. Por padrão, o nome do hub de tarefas no arquivo [host. JSON](durable-functions-bindings.md#host-json) é usado.

O comando a seguir exclui todos os dados do armazenamento do Azure associados ao Hub de tarefas `UserTest`.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com controle de versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referência de API HTTP interna para gerenciamento de instância](durable-functions-http-api.md)