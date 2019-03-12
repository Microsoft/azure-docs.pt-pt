---
title: Gerir instâncias de funções duráveis - Azure
description: Saiba como gerir instâncias na extensão de funções duráveis para as funções do Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547320"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerir instâncias de funções duráveis no Azure

Se estiver a utilizar o [funções duráveis](durable-functions-overview.md) extensão para as funções do Azure, ou se quiser começar a fazer isso, certifique-se de que esteja tirando o melhor uso dele. Pode otimizar as instâncias de orquestração de funções duráveis aprendendo mais sobre como geri-los. Este artigo vai os detalhes de cada operação de gestão de instância.

Pode começar e terminar instâncias, por exemplo, e pode consultar instâncias, incluindo a capacidade de consultar todas as instâncias e instâncias de consulta com filtros. Além disso, pode enviar eventos para instâncias, aguardar pela conclusão de orquestração e obter URLs de webhook de gestão de HTTP. Este artigo aborda outras operações de gestão, também, incluindo o avanço rápido instâncias, remover o histórico de instância e a eliminação de um hub de tarefa.

Nas funções durável, tem opções para como pretende implementar cada uma destas operações de gestão. Este artigo fornece exemplos que utilizam o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) para ambas as .NET (C#) e JavaScript.

## <a name="start-instances"></a>Iniciar as instâncias

É importante ser capaz de iniciar uma instância de orquestração. Isso normalmente é feito quando estiver a utilizar uma associação de funções duráveis no acionador de outra função.

O [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) método no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou `startNew` no `DurableOrchestrationClient` (JavaScript) inicia uma nova instância. Adquirir instâncias desta classe usando o `orchestrationClient` enlace. Internamente, esse método coloca em fila uma mensagem na fila de controle, que dispara o início de uma função com o nome especificado, que utiliza o `orchestrationTrigger` acionar a ligação.

Esta operação assíncrona é concluída quando o processo de orquestração é agendado com êxito. O processo de orquestração deverá ser iniciado dentro de 30 segundos. Se demorar mais tempo, verá um `TimeoutException`.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, defina a variável de ambiente `WEBSITE_HOSTNAME` para `localhost:<port>` (por exemplo, `localhost:7071`) para utilizar os métodos em `DurableOrchestrationClient`. Para obter mais informações sobre este requisito, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Os parâmetros a serem [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) são os seguintes:

* **Nome**: O nome da função de orquestrador para agendar.
* **Entrada**: Todos os dados JSON serializável que devem ser passados como entrada para a função de orquestrador.
* **InstanceId**: (Opcional) O ID exclusivo da instância. Se não especificar este parâmetro, o método usa um ID aleatório.

Eis um exemplo do c# simples:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

Os parâmetros para `startNew` são os seguintes:

* **Nome**: O nome da função de orquestrador para agendar.
* **InstanceId**: (Opcional) O ID exclusivo da instância. Se não especificar este parâmetro, o método usa um ID aleatório.
* **Entrada**: (Opcional) Todos os dados JSON serializável que devem ser passados como entrada para a função de orquestrador.

Eis um exemplo de JavaScript simples:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Utilizar um identificador aleatório para o ID de instância. Isto ajuda a garantir uma distribuição de carga igual quando está a dimensionar as funções do orchestrator em várias VMs. O tempo adequado para utilizar os IDs de instância não aleatórios é quando o ID tem de ser uma origem externa ou ao implementar o [singleton orchestrator](durable-functions-singletons.md) padrão.

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Também pode iniciar uma instância diretamente, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable start-new` comando. Ele usa os seguintes parâmetros:

* **`function-name` (obrigatório)** : Nome da função para iniciar.
* **`input` (opcional)** : A função, qualquer um dos inline ou por meio de um ficheiro JSON de entrada. Para os ficheiros, adicione um prefixo para o caminho para o ficheiro com `@`, tais como `@path/to/file.json`.
* **`id` (opcional)** : ID da instância de orquestração. Se não especificar este parâmetro, o comando utiliza um GUID aleatório.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é AzureWebJobsStorage.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é DurableFunctionsHub. Também pode definir isso no [Host. JSON](durable-functions-bindings.md#host-json) utilizando durableTask:HubName.

> [!NOTE]
> Principais ferramentas comandos partem do princípio de que estiver a executar no diretório raiz de uma aplicação de funções. Se fornecer explicitamente a `connection-string-setting` e `task-hub-name` parâmetros, pode executar os comandos a partir de qualquer diretório. Embora seja possível executar estes comandos sem um anfitrião de aplicação de função em execução, pode achar que não é possível observar alguns efeitos, a menos que o anfitrião está em execução. Por exemplo, o `start-new` comando coloca em fila uma mensagem de início para o hub de tarefa de destino, mas a orquestração, na verdade, não é executado, exceto se houver um processo de anfitrião de aplicação de função em execução que pode processar a mensagem.

O seguinte comando inicia a função com o nome HelloWorld e transmite o conteúdo do arquivo `counter-data.json` a ele:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte de seu esforço para gerir as suas orquestrações, provavelmente precisará recolher informações sobre o estado de uma instância da orquestração (por exemplo, se ele concluiu normalmente ou não).

O [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) método no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET) ou o `getStatus` método no `DurableOrchestrationClient` classe (JavaScript) consulta o estado de uma orquestração instância.

É necessário um `instanceId` (obrigatório), `showHistory` (opcional), `showHistoryOutput` (opcional), e `showInput` (opcional, apenas o .NET) como parâmetros.

* **`showHistory`**: Se definido como `true`, a resposta contém o histórico de execução.
* **`showHistoryOutput`**: Se definido como `true`, o histórico de execução contém saídas de atividade.
* **`showInput`**: Se definido como `false`, a resposta não contém a entrada da função. O valor predefinido é `true`. (Apenas para o .NET)

O método retorna um objeto JSON com as seguintes propriedades:

* **Nome**: O nome da função de orquestrador.
* **InstanceId**: O ID de instância da orquestração (deve ser o mesmo que o `instanceId` entrada).
* **CreatedTime**: A hora em que a função de orquestrador iniciou a execução.
* **LastUpdatedTime**: O tempo em que a orquestração último foi efetuada a verificação.
* **Entrada**: A entrada da função como um valor JSON. Este campo não é preenchido caso a `showInput` é false.
* **CustomStatus**: Estado de orquestração personalizado no formato JSON.
* **Saída**: A saída da função como um valor JSON (se a função foi concluída). Se a função de orquestrador falhou, esta propriedade inclui os detalhes da falha. Se a função de orquestrador foi terminada, esta propriedade inclui o motivo para a finalização (se houver).
* **RuntimeStatus**: Um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciada em execução.
  * **Executar**: A instância foi iniciado em execução.
  * **Concluído**: A instância foi concluída normalmente.
  * **ContinuedAsNew**: A instância reiniciou-se com um histórico de novo. Este é um estado transitório.
  * **Falha ao**: A instância falhou com um erro.
  * **Terminada**: A instância foi interrompida abruptamente.
* **Histórico de**: O histórico de execução da orquestração. Este campo só é preenchido caso `showHistory` está definido como `true`.

Este método devolve `null` se a instância não existe ou ainda não foi iniciada em execução.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Também é possível obter o estado de uma instância da orquestração diretamente, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable get-runtime-status` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`show-input` (opcional)** : Se definido como `true`, a resposta contém a entrada da função. O valor predefinido é `false`.
* **`show-output` (opcional)** : Se definido como `true`, a resposta contém a saída da função. O valor predefinido é `false`.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

O comando seguinte obtém o estado (incluindo a entrada e saída) de uma instância com um ID de instância da orquestração do 0ab8c55a66644d68a3a8b220b12d209c. Parte do princípio de que está a executar o `func` comando do diretório de raiz da aplicação de função:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Pode utilizar o `durable get-history` comando para obter o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser configurada no Host. JSON, utilizando durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Todas as instâncias de consulta

Em vez de consulta uma instância na sua orquestração ao mesmo tempo, talvez seja mais eficiente para consultar todos eles de uma só vez.

Pode utilizar o `GetStatusAsync` (.NET) ou `getStatusAll` método (JavaScript) para consultar os Estados de todas as instâncias de orquestração. No .NET, pode passar um `CancellationToken` objeto caso queira cancelá-lo. O método retorna objetos com as mesmas propriedades como o `GetStatusAsync` método com parâmetros.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Também é possível para instâncias de consulta diretamente, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable get-instances` comando. Ele usa os seguintes parâmetros:

* **`top` (opcional)** : Este comando suporta paginação. Este parâmetro corresponde ao número de instâncias obtida por cada pedido. A predefinição é 10.
* **`continuation-token` (opcional)** : Um token para indicar qual página ou a secção de instâncias para recuperar. Cada `get-instances` execução devolve um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instâncias de consulta com filtros

E se não precisa realmente todas as informações que pode fornecer uma consulta de instância padrão? Por exemplo, e se estiver à procura de apenas a hora de criação de orquestração, ou o estado de tempo de execução da orquestração? Pode restringir a consulta ao aplicar filtros.

Utilize o `GetStatusAsync` (.NET) ou `getStatusBy` predefinidas de método (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

As ferramentas de núcleo de funções do Azure, pode também utilizar o `durable get-instances` comando com filtros. Para além do mencionados anteriormente `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name` parâmetros, pode utilizar três parâmetros de filtro (`created-after`, `created-before`, e `runtime-status`).

* **`created-after` (opcional)** : Obter as instâncias criadas após esta data/hora (UTC). ISO 8601 formatado datetimes aceites.
* **`created-before` (opcional)** : Obter as instâncias criadas antes desta data/hora (UTC). ISO 8601 formatado datetimes aceites.
* **`runtime-status` (opcional)** : Obter as instâncias com um estado específico (por exemplo, em execução ou concluído). Pode fornecer vários Estados de (espaço separado).
* **`top` (opcional)** : Número de instâncias de obter por pedido. A predefinição é 10.
* **`continuation-token` (opcional)** : Um token para indicar qual página ou a secção de instâncias para recuperar. Cada `get-instances` execução devolve um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

Se não fornecer todos os filtros (`created-after`, `created-before`, ou `runtime-status`), o comando simplesmente obtém `top` instâncias, sem considerar para o tempo de criação ou de estado de runtime.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar as instâncias

Se tiver uma instância de orquestração que está a demorar demasiado tempo ou, só precisa de pará-la antes de concluir por qualquer motivo, tem a opção para encerrá-lo.

Pode utilizar o [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou o `terminate` método o `DurableOrchestrationClient` classe (JavaScript). Os dois parâmetros são um `instanceId` e um `reason` cadeia de caracteres, que são escritos nos registos e para o estado de instância. Uma instância terminada deixa de ser executada assim que atingir o próximo `await` (.NET) ou `yield` ponto (JavaScript) ou encerra imediatamente se ainda estiver `await` ou `yield`.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Atualmente não propagar a terminação de instância. Funções de atividade e orquestrações secundárias executam por completo, independentemente se tiver terminado a instância de orquestração que os chamou.

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Pode também terminar uma instância da orquestração diretamente, com o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable terminate` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração, para terminar.
* **`reason` (opcional)** : Motivo de encerramento.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

O seguinte comando termina uma instância de orquestração com o ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante para as suas funções do orchestrator conseguir a aguardar e escutar eventos externos. Isto inclui [monitorizar as funções](durable-functions-concepts.md#monitoring) e as funções que estão a aguardar [interação humana](durable-functions-concepts.md#human).

Enviar notificações de eventos para instâncias em execução ao utilizar o [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET) ou o `raiseEvent` método da `DurableOrchestrationClient` classe ( JavaScript). Instâncias que podem lidar com esses eventos são aqueles que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) ou `waitForExternalEvent` (JavaScript).

Os parâmetros a serem [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: O ID exclusivo da instância.
* **EventName**: O nome do evento para enviar.
* **EventData**: Um payload JSON serializável para enviar para a instância.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Se não existir nenhuma instância de orquestração com o ID de instância especificado, ou se a instância não está à espera o nome de evento especificado, a mensagem de evento é rejeitada. Para obter mais informações sobre este comportamento, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Pode também levantar um evento para uma instância da orquestração diretamente, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable raise-event` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`event-name` (opcional)** : Nome do evento para gerar. A predefinição é `$"Event_{RandomGUID}"`.
* **`event-data` (opcional)** : Dados para enviar para a instância de orquestração. Isso pode ser o caminho para um ficheiro JSON, ou pode fornecer os dados diretamente na linha de comandos.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar pela conclusão de orquestração

Orquestrações de execução longa, poderá esperar obter os resultados de uma orquestração. Nestes casos, também é útil poder definir um período de tempo limite na orquestração. Se o tempo limite for excedido, o estado da orquestração deverá ser retornado em vez dos resultados.

O [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe expõe um [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API em .NET. Pode utilizar esta API para obter o resultado real de uma instância de orquestração de forma síncrona. No JavaScript, o `DurableOrchestrationClient` classe expõe um `waitForCompletionOrCreateCheckStatusResponse` API com o mesmo objetivo. Quando não estão definidas, os métodos usam um valor predefinido de 10 segundos para `timeout`e 1 segundo para `retryInterval`.  

Eis um exemplo de função de Acionador de HTTP que demonstra como utilizar esta API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Chame a função com a seguinte linha. Utilize 2 segundos para o tempo limite e 0,5 segundos durante o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, existem dois casos:

* As instâncias de orquestração concluir dentro do tempo limite definido (no caso 2 segundos) e a resposta é a saída de instância real de orquestração, entregue de forma síncrona:

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

* As instâncias de orquestração não é possível concluir dentro do tempo limite definido e a resposta é a predefinição um descrito em [deteção de URL da API HTTP](durable-functions-http-api.md):

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
> O formato dos URLs de webhook pode variar, dependendo de qual versão do anfitrião de funções do Azure está a executar. No exemplo anterior é para o anfitrião de 2.x as funções do Azure.

## <a name="retrieve-http-management-webhook-urls"></a>Obter URLs de webhook de gestão de HTTP

Pode utilizar um sistema externo para monitorizar ou acionem eventos para uma orquestração. Sistemas externos podem comunicar com funções duráveis por meio dos URLs de webhook que fazem parte da resposta padrão descrita em [deteção de URL da API HTTP](durable-functions-http-api.md). No entanto, os URLs de webhook também podem ser acessados por meio de programação no cliente de orquestração ou numa função de atividade. Fazer isso usando o [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou o `createHttpManagementPayload` método o `DurableOrchestrationClient` classe (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` ter um parâmetro:

* **instanceId**: O ID exclusivo da instância.

Os métodos de retornam uma instância de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) ou um objeto (JavaScript), com as seguintes propriedades de cadeia de caracteres:

* **Id**: O ID de instância da orquestração (deve ser o mesmo que o `InstanceId` entrada).
* **StatusQueryGetUri**: O URL de estado da instância de orquestração.
* **SendEventPostUri**: O URL de "emitir um evento" da instância de orquestração.
* **TerminatePostUri**: O URL da instância de orquestração "terminar".
* **RewindPostUri**: O URL de "o recuo" da instância de orquestração.

Funções de atividade podem enviar uma instância desses objetos para sistemas externos para monitorizar ou acionem eventos para uma orquestração:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

## <a name="rewind-instances-preview"></a>Retroceder instances (pré-visualização)

Se tiver uma falha de orquestração por um motivo inesperado, pode *Retroceder* a instância de anteriormente bom estado de funcionamento com uma API concebida para essa finalidade.

> [!NOTE]
> Esta API não se destina a ser uma substituição para o tratamento de erros adequado e políticas de repetição. Em vez disso, destina-se a ser usado apenas em casos em que as instâncias de orquestração falharem por motivos de inesperado. Para obter mais detalhes sobre políticas de repetição e manipulação de erro, consulte a [tratamento de erros](durable-functions-error-handling.md) tópico.

Utilize o [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) ou `rewindAsync` API (JavaScript) para colocar a orquestração novamente para o *em execução* estado. Volte a executar as falhas de execução de atividade ou suborchestration que causou a falha de orquestração.

Por exemplo, digamos que tenha um fluxo de trabalho que envolve uma série de [aprovações humanas](durable-functions-concepts.md#human). Suponha que há uma série de funções de atividade que o notificam alguém que sua aprovação é necessária e aguarde que a resposta em tempo real. Afinal de contas da aprovação atividades tenham recebido as respostas ou excedeu o tempo, suponha que outra atividade falha devido a um erro de configuração do aplicativo, como uma cadeia de ligação de base de dados inválido. O resultado é uma falha de orquestração profundamente o fluxo de trabalho. Com o `RewindAsync` (.NET) ou `rewindAsync` administrador (JavaScript) API, um aplicativo pode corrigir o erro de configuração e Retroceder a orquestração com falha para o estado de imediatamente antes da falha. Nenhum dos passos de interação humana tem de ser aprovado novamente e a orquestração pode agora ser concluída com êxito.

> [!NOTE]
> O *Retroceder* funcionalidade não suporta e instâncias de orquestração de avanço rápido, que utilizam os temporizadores duráveis.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Também pode retroceder uma instância da orquestração diretamente, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable rewind` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)** : ID da instância de orquestração.
* **`reason` (opcional)** : Motivo para o avanço rápido a instância de orquestração.
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Histórico de instância

Para remover todos os dados associados a uma orquestração, pode remover o histórico de instância. Por exemplo, pode querer nos livrar de linhas de tabelas do Azure e blobs de mensagem grande, caso existam. Para tal, utilize o [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> O `PurgeInstanceHistoryAsync` API está atualmente disponível apenas para C#.

 O método tem duas sobrecargas. Primeiro remove histórico pelo ID da instância de orquestração de:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

O segundo exemplo mostra uma função acionada por temporizador que remove o histórico de todas as instâncias de orquestração concluída após o intervalo de tempo especificado. Neste caso, remove os dados para todas as instâncias concluídas 30 ou mais dias atrás. Está agendado para ser executado uma vez por dia, às 12 AM:

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
> Para o processo de uma função acionada por hora com êxito, o status de tempo de execução deve estar **concluído**, **Terminated**, ou **falha**.

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Pode remover o histórico de uma instância de orquestração, utilizando o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable purge-history` comando. Semelhante ao segundo C# exemplo na secção anterior, ele remove o histórico de todas as instâncias de orquestração criados durante um intervalo de tempo especificado. Pode filtrar ainda mais instâncias eliminadas por Estado de runtime. O comando tem vários parâmetros:

* **`created-after` (opcional)** : Remover o histórico de instâncias criadas após esta data/hora (UTC). ISO 8601 formatado datetimes aceites.
* **`created-before` (opcional)** : Remover o histórico de instâncias criadas antes desta data/hora (UTC). ISO 8601 formatado datetimes aceites.
* **`runtime-status` (opcional)** : Remover o histórico de instâncias com um estado específico (por exemplo, em execução ou concluído). Pode fornecer vários Estados de (espaço separado).
* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

O comando seguinte elimina o histórico de todas as instâncias com falha criada antes de 14 de Novembro de 2018 em 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminar um hub de tarefa

Utilizar o [ferramentas de núcleo de funções do Azure](../functions-run-local.md) `durable delete-task-hub` de comando, pode eliminar todos os artefactos de armazenamento associados a um hub de tarefa específica. Isto inclui as tabelas de armazenamento do Azure, filas e blobs. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)** : Nome da definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar. A predefinição é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : Nome do hub de tarefa de funções duráveis para utilizar. A predefinição é `DurableFunctionsHub`. Também pode ser definido nas [Host. JSON](durable-functions-bindings.md#host-json), utilizando durableTask:HubName.

O comando seguinte elimina todos os dados de armazenamento do Azure associados a `UserTest` hub de tarefa.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como utilizar as APIs de HTTP por exemplo, gestão](durable-functions-http-api.md)
