---
title: Gerir casos em Funções Duráveis - Azure
description: Aprenda a gerir casos na extensão funções duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476823"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerir casos em Funções Duráveis em Azure

Se estiver a utilizar a extensão [Funções Duráveis](durable-functions-overview.md) para funções Azure, ou se quiser começar a fazê-lo, certifique-se de que está a tirar o melhor partido dela. Pode otimizar os seus casos de orquestração de Funções Duráveis aprendendo mais sobre como geri-las. Este artigo vai para os detalhes de cada operação de gestão de instâncias.

Pode iniciar e terminar instâncias, por exemplo, e pode consultar casos, incluindo a capacidade de consultar todos os casos e consultar casos com filtros. Além disso, pode enviar eventos para instâncias, esperar pela conclusão da orquestração e recuperar URLs de webhook de gestão HTTP. Este artigo abrange também outras operações de gestão, incluindo casos de rebobinação, purgamento da história da instância e eliminação de um centro de tarefas.

Em Funções Duráveis, tem opções para como pretende implementar cada uma destas operações de gestão. Este artigo fornece exemplos que utilizam as [Ferramentas Core funções Azure](../functions-run-local.md) para ambas as ferramentas .NET (C#) e JavaScript.

## <a name="start-instances"></a>Iniciar instâncias

É importante poder começar um exemplo de orquestração. Isto é feito comumente quando se está a usar uma ligação de funções duráveis no gatilho de outra função.

O `StartNewAsync` método (.NET) ou `startNew` (JavaScript) sobre a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) inicia uma nova instância. Internamente, este método inaque uma mensagem na fila de controlo, que depois aciona o início de uma função com o nome especificado que utiliza a ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger).

Esta operação de asincronização completa quando o processo de orquestração é programado com sucesso.

Os parâmetros para iniciar uma nova instância de orquestração são os seguintes:

* **Nome**: O nome da função de orquestrador para agendar.
* **Entrada**: Quaisquer dados jSON-serializáveis que devem ser passados como entrada para a função orquestradora.
* **Instância:**(Opcional) A identificação única da instância. Se não especificar este parâmetro, o método utiliza uma identificação aleatória.

> [!TIP]
> Utilize um identificador aleatório para a identificação por exemplo. IDs de instância aleatória ajudam a garantir uma distribuição de carga igual quando você está escalando funções de orquestrador em vários VMs. O momento adequado para usar iDs de instância não aleatória é quando o ID deve vir de uma fonte externa, ou quando você está implementando o padrão [de orquestrador singleton.](durable-functions-singletons.md)

O seguinte código é uma função de exemplo que inicia uma nova instância de orquestração:

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Salvo especificação em contrário, os exemplos desta página utilizam o gatilho HTTP com a seguinte função.json.

**função.json**

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
> Este exemplo visa a versão 2.x das Funções Duráveis. Na versão 1.x, `orchestrationClient` `durableClient`use em vez de .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também pode iniciar uma instância diretamente utilizando o `durable start-new` comando [Ferramentas Core funções Do Azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (obrigatório) : Nome da função a iniciar. ** `function-name` **
* (opcional) : Entrada na função, inline ou através de um ficheiro JSON. ** `input` ** Para ficheiros, adicione um prefixo ao `@`caminho `@path/to/file.json`do ficheiro com , como .
* (opcional) : Identificação da instância de orquestração. ** `id` ** Se não especificar este parâmetro, o comando utiliza um GUID aleatório.
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** O predefinido é AzureWebJobsStorage.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** O padrão é DurableFunctionsHub. Também pode defini-lo em [host.json](durable-functions-bindings.md#host-json) utilizando o durableTask:HubName.

> [!NOTE]
> Os comandos Core Tools assumem que está a executá-los a partir do diretório raiz de uma aplicação de função. Se fornecer explicitamente os `connection-string-setting` e `task-hub-name` parâmetros, pode executar os comandos de qualquer diretório. Embora possa executar estes comandos sem que um anfitrião de aplicação de funções esteja em execução, pode descobrir que não pode observar alguns efeitos a menos que o anfitrião esteja em execução. Por exemplo, `start-new` o comando incorre numa mensagem de início no centro de tarefas alvo, mas a orquestração não funciona a menos que haja um processo de execução de app de função que possa processar a mensagem.

O seguinte comando inicia a função denominada HelloWorld, e passa o conteúdo do ficheiro `counter-data.json` para ele:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Casos de consulta

Como parte do seu esforço para gerir as suas orquestrações, provavelmente terá de recolher informações sobre o estado de uma instância de orquestração (por exemplo, se já terminou normalmente ou falhou).

O `GetStatusAsync` método (.NET) ou (JavaScript) `getStatus` sobre a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) consulta o estado de uma instância de orquestração.

É preciso `instanceId` um (obrigatório), `showHistory` `showHistoryOutput` (opcional), `showInput` (opcional) e (opcional) como parâmetros.

* **`showHistory`**: Se `true`definido, a resposta contém o histórico de execução.
* **`showHistoryOutput`**: Se `true`definido para, o histórico de execução contém saídas de atividade.
* **`showInput`**: Se `false`definido para, a resposta não conterá a entrada da função. O valor predefinido é `true`.

O método devolve um objeto com as seguintes propriedades:

* **Nome**: O nome da função orquestradora.
* **InstânciaId**: A identificação da instância da orquestração (deve ser a mesma que a `instanceId` entrada).
* **Tempo criado**: O tempo em que a função de orquestrador começou a funcionar.
* **Última Atualização**: O tempo em que a orquestração foi o último a ser posto de controlo.
* **Entrada**: A entrada da função como valor JSON. Este campo não é `showInput` povoado se for falso.
* **CustomStatus**: Estatuto de orquestração personalizada no formato JSON.
* **Saída**: A saída da função como valor JSON (se a função tiver terminado). Se a função de orquestrador falhou, esta propriedade inclui os detalhes da falha. Se a função de orquestrador foi encerrada, esta propriedade inclui a razão da rescisão (se houver).
* Tempo de **execução**: Um dos seguintes valores:
  * **Pendente**: A instância foi agendada mas ainda não começou a funcionar.
  * **Execução**: A instância começou a correr.
  * **Concluído**: A instância está concluída normalmente.
  * **ContinuaasNew**: O caso recomeçou-se com uma nova história. Este estado é um estado transitório.
  * **Falha:** A instância falhou com um erro.
  * **Terminada:** A ocorrência foi interrompida abruptamente.
* **História**: A história da execução da orquestração. Este campo só é `showHistory` povoado se estiver definido para `true`.

Este método `null` retorna (.NET) ou `undefined` (JavaScript) se a instância não existir.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também é possível obter o estatuto de uma instância de orquestração diretamente, usando o `durable get-runtime-status` comando Ferramentas Centrais de [Funções Azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (obrigatório) : Identificação da instância de orquestração. ** `id` **
* (opcional) : Se `true`definido para, a resposta contém a entrada da função. ** `show-input` ** O valor predefinido é `false`.
* (opcional) : Se `true`definido para, a resposta contém a saída da função. ** `show-output` ** O valor predefinido é `false`.
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)utilizando o durableTask:HubName.

O comando seguinte recupera o estatuto (incluindo entrada e saída) de um caso com uma instância de orquestração ID de 0ab8c55a6644d68a3a8b220b12d20209c. Assume que está a `func` executar o comando a partir do diretório raiz da aplicação de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Pode usar `durable get-history` o comando para recuperar a história de um caso de orquestração. São precisos os seguintes parâmetros:

* (obrigatório) : Identificação da instância de orquestração. ** `id` **
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em host.json, utilizando o durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consulta a todos os casos

Em vez de consultar um caso na sua orquestração de cada vez, você pode achar mais eficiente consultar todos de uma vez.

Pode utilizar `GetStatusAsync` o método (.NET) ou `getStatusAll` (JavaScript) para consultar os estados de todas as instâncias de orquestração. Em .NET, pode `CancellationToken` passar um objeto no caso de o cancelar. O método devolve objetos com `GetStatusAsync` as mesmas propriedades que o método com parâmetros.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também é possível consultar casos diretamente, utilizando o `durable get-instances` comando ferramentas nucleares de [funções Azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (opcional) : Este comando suporta a pagagem. ** `top` ** Este parâmetro corresponde ao número de instâncias recuperadas por pedido. O padrão é 10.
* (opcional) : Um símbolo que indique qual a página ou secção de instâncias a recuperar. ** `continuation-token` ** Cada `get-instances` execução devolve um símbolo ao próximo conjunto de instâncias.
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)utilizando o durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Consultas de casos com filtros

E se não precisar de toda a informação que uma consulta padrão pode fornecer? Por exemplo, e se estiveres apenas à procura do tempo de criação da orquestração, ou do estado de execução da orquestração? Pode reduzir a sua consulta aplicando filtros.

Utilize `GetStatusAsync` o método (.NET) ou `getStatusBy` (JavaScript) para obter uma lista de instâncias de orquestração que correspondam a um conjunto de filtros predefinidos.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Nas Ferramentas Core Funções Azure, `durable get-instances` também pode utilizar o comando com filtros. Para além dos parâmetros `continuation-token` `connection-string-setting`acima `task-hub-name` referidos, `top`e dos parâmetros, `created-before`pode `runtime-status`utilizar três parâmetros de filtro (`created-after`, , e ).

* (opcional) : Recuperar as instâncias criadas após esta data/hora (UTC). ** `created-after` ** Iso 8601 datas formatadas aceites.
* (opcional) : Recuperar as instâncias criadas antes desta data/hora (UTC). ** `created-before` ** Iso 8601 datas formatadas aceites.
* (opcional) : Recuperar as instâncias com um determinado estatuto (por exemplo, correr ou completar). ** `runtime-status` ** Pode fornecer vários estados (espaço separados).
* (opcional) : Número de ocorrências recuperadas por pedido. ** `top` ** O padrão é 10.
* (opcional) : Um símbolo que indique qual a página ou secção de instâncias a recuperar. ** `continuation-token` ** Cada `get-instances` execução devolve um símbolo ao próximo conjunto de instâncias.
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)utilizando o durableTask:HubName.

Se não fornecer filtros (`created-after` `created-before` `runtime-status`ou), o comando simplesmente `top` recupera instâncias, sem ter em conta o estado de execução ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se tiver um caso de orquestração que está a demorar muito tempo a ser executado, ou só precisa de o parar antes que esteja completo por qualquer motivo, tem a opção de o encerrar.

Pode utilizar `TerminateAsync` o método (.NET) ou (JavaScript) `terminate` do cliente de [orquestração vinculado](durable-functions-bindings.md#orchestration-client) a instâncias de terminação. Os dois parâmetros `instanceId` são `reason` uma e uma corda, que são escritas para troncos e para o estado da instância.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

Um caso encerrado acabará por `Terminated` transitar para o estado. No entanto, esta transição não acontecerá imediatamente. Pelo contrário, a operação de cessação será levada à fila no centro de tarefas, juntamente com outras operações, por exemplo. Você pode usar a [consulta de instância](#query-instances) APIs para saber `Terminated` quando uma instância terminada realmente chegou ao estado.

> [!NOTE]
> A rescisão de instância sem propagação atualmente. As funções de atividade e as suborquestrações estão concluídas, independentemente de ter terminado a instância de orquestração que as chamou.

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também pode encerrar uma instância de orquestração diretamente, utilizando o `durable terminate` comando [Ferramentas Core funções Azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (obrigatório) : Identificação da instância de orquestração para terminar. ** `id` **
* (opcional) : Motivo para a rescisão. ** `reason` **
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)utilizando o durableTask:HubName.

O seguinte comando termina uma instância de orquestração com uma identificação de 0ab8c55a6664d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que as suas funções de orquestrador possam esperar e ouvir eventos externos. Isto inclui [funções](durable-functions-overview.md#monitoring) e funções de monitor que aguardam a [interação humana.](durable-functions-overview.md#human)

Envie notificações de eventos para `RaiseEventAsync` instâncias de execução utilizando o método (.NET) ou o `raiseEvent` método (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client). As instâncias que podem lidar com estes `WaitForExternalEvent` eventos são aquelas que `waitForExternalEvent` aguardam uma chamada para (.NET) ou cedendo a uma chamada (JavaScript).

Os parâmetros `RaiseEventAsync` para (.NET) e `raiseEvent` (JavaScript) são os seguintes:

* **InstânciaId**: A identificação única da instância.
* **Nome**do evento : O nome do evento a enviar.
* **EventData**: Uma carga útil json-serializável para enviar para a instância.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

> [!NOTE]
> Se não houver uma instância de orquestração com o ID de instância especificado, a mensagem do evento é descartada. Se existir uma instância mas ainda não estiver à espera do evento, o evento será armazenado no estado do caso até que esteja pronto para ser recebido e processado.

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também pode elevar um evento a uma instância de orquestração diretamente, utilizando o `durable raise-event` comando [Ferramentas Core funções do Azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (obrigatório) : Identificação da instância de orquestração. ** `id` **
* **`event-name`**: Nome do evento para levantar.
* (opcional) : Dados a enviar para a instância de orquestração. ** `event-data` ** Este pode ser o caminho para um ficheiro JSON, ou pode fornecer os dados diretamente na linha de comando.
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** A predefinição é `DurableFunctionsHub`. Também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)utilizando o durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguarde a conclusão da orquestração

Em orquestrações de longa data, talvez queira esperar e obter os resultados de uma orquestração. Nestes casos, também é útil ser capaz de definir um período de tempo na orquestração. Se o prazo for ultrapassado, o estado da orquestração deve ser devolvido em vez dos resultados.

O `WaitForCompletionOrCreateCheckStatusResponseAsync` método (.NET) ou (JavaScript) `waitForCompletionOrCreateCheckStatusResponse` pode ser utilizado para obter a saída real a partir de uma instância de orquestração sincronizadamente. Por defeito, estes métodos utilizam um `timeout`valor predefinido `retryInterval`de 10 segundos para . e 1 segundo para .  

Aqui está um exemplo de função http-trigger que demonstra como usar esta API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

Ligue para a função com a seguinte linha. Utilize 2 segundos para o tempo de paragem e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração completam-se dentro do tempo definido (neste caso 2 segundos), e a resposta é a saída da instância de orquestração real, entregue sincronicamente:

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

* As instâncias de orquestração não podem ser completadas dentro do tempo definido, e a resposta é a descrita na descoberta de [URL HTTP API:](durable-functions-http-api.md)

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
> O formato dos URLs webhook pode diferir, dependendo da versão do anfitrião das Funções Azure que você está executando. O exemplo anterior é para o hospedeiro Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs webhook de gestão HTTP

Você pode usar um sistema externo para monitorizar ou elevar eventos para uma orquestração. Os sistemas externos podem comunicar com funções duráveis através dos URLs de webhook que fazem parte da resposta predefinida descrita na descoberta do [URL HTTP API](durable-functions-http-features.md#http-api-url-discovery). Os URLs webhook podem, em alternativa, ser acedidos programáticamente utilizando a ligação do cliente de [orquestração.](durable-functions-bindings.md#orchestration-client) Os `CreateHttpManagementPayload` métodos `createHttpManagementPayload` (.NET) ou (JavaScript) podem ser usados para obter um objeto serializável que contém estes URLs webhook.

Os `CreateHttpManagementPayload` métodos (.NET) e `createHttpManagementPayload` (JavaScript) têm um parâmetro:

* **instânciaId**: A identificação única da instância.

Os métodos devolvem um objeto com as seguintes propriedades de cordas:

* **Id**: A identificação da orquestração (deve `InstanceId` ser a mesma que a entrada).
* **StatusQueryGetUri**: O URL de estado da instância de orquestração.
* **SendEventPostUri**: O URL "raise event" da instância de orquestração.
* **TerminatePostUri**: O URL "terminate" da instância de orquestração.
* **PurpurHistoryDeleteUri**: O URL "purpur history" da instância de orquestração.

As funções podem enviar instâncias destes objetos para sistemas externos para monitorizar ou levantar eventos sobre as orquestrações correspondentes, como mostram os seguintes exemplos:

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, `DurableActivityContext` deve utilizar em vez de `IDurableActivityContext`, deve utilizar o `OrchestrationClient` `DurableOrchestrationClient` `IDurableOrchestrationClient` `DurableClient` atributo em vez do atributo, e deve utilizar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

## <a name="rewind-instances-preview"></a>Casos de rebobinar (pré-visualização)

Se tiver uma falha de orquestração por uma razão inesperada, pode *rebobinar* a instância para um estado anteriormente saudável usando uma API construída para esse fim.

> [!NOTE]
> Esta API não se destina a substituir as políticas de manipulação e retenção adequadas de erros. Pelo contrário, pretende-se ser utilizado apenas nos casos em que os casos de orquestração falham por razões inesperadas. Para obter mais informações sobre as políticas de manipulação de erros e de retry, consulte o artigo de manipulação de [erros.](durable-functions-error-handling.md)

Utilize `RewindAsync` o método (.NET) ou `rewind` (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) para colocar a orquestração de volta no estado De *Corrente.* Este método também reexecutará as falhas de execução da atividade ou sub-orquestração que causaram a falha da orquestração.

Por exemplo, digamos que tem um fluxo de trabalho envolvendo uma série de [aprovações humanas.](durable-functions-overview.md#human) Suponha que há uma série de funções de atividade que notificam alguém de que a sua aprovação é necessária, e aguardem a resposta em tempo real. Depois de todas as atividades de aprovação terem recebido respostas ou cronometradas, suponha que outra atividade falhe devido a uma configuração errada da aplicação, como uma cadeia de ligação de base de dados inválida. O resultado é uma falha de orquestração no fundo do fluxo de trabalho. Com `RewindAsync` a (.NET) ou `rewind` (JavaScript) API, um administrador de aplicação pode corrigir o erro de configuração e rebobinar a orquestração falhada de volta ao estado imediatamente antes da falha. Nenhum dos passos de interação humana precisa de ser reaprovado, e a orquestração pode agora completar com sucesso.

> [!NOTE]
> A funcionalidade de *rebobinar* não suporta casos de orquestração de rebobinar que usam tempos duráveis.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Também pode rebobinar uma instância de orquestração diretamente utilizando o `durable rewind` comando [Ferramentas Core funções azure.](../functions-run-local.md) São precisos os seguintes parâmetros:

* (obrigatório) : Identificação da instância de orquestração. ** `id` **
* (opcional) : Razão para rebobinar a instância de orquestração. ** `reason` **
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** Por predefinição, o nome do centro de tarefas no ficheiro [host.json](durable-functions-bindings.md#host-json) é utilizado.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar a história da instância

Para remover todos os dados associados a uma orquestração, pode expurgar o histórico da ocorrência. Por exemplo, é possível eliminar quaisquer linhas de mesa Azure e grandes bolhas de mensagem associadas a uma instância completa. Para isso, utilize `PurgeInstanceHistoryAsync` o método (.NET) ou `purgeInstanceHistory` (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client).

Este método tem duas sobrecargas. A primeira sobrecarga purga a história pela identificação da instância de orquestração:

# <a name="c"></a>[C #](#tab/csharp)

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

Consulte as [instâncias de início](#javascript-function-json) para a configuração fun.json.

---

O exemplo seguinte mostra uma função acionada pelo temporizador que expurga a história de todos os casos de orquestração que completaram após o intervalo de tempo especificado. Neste caso, remove dados para todos os casos concluídos há 30 ou mais dias. Está programado para funcionar uma vez por dia, às 00:00:

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O `purgeInstanceHistoryBy` método pode ser usado para purgar condicionalmente a história da instância em vários casos.

**função.json**

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
> Este exemplo visa a versão 2.x das Funções Duráveis. Na versão 1.x, `orchestrationClient` `durableClient`use em vez de .

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

---

> [!NOTE]
> Para que a operação de história da purga tenha sucesso, o estado de funcionamento da instância-alvo deve ser **concluído,** **terminado**ou **falhado**.

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Pode expurgar a história de uma instância de orquestração utilizando o `durable purge-history` comando Ferramentas Nucleares das [Funções Azure.](../functions-run-local.md) Semelhante ao segundo exemplo C# na secção anterior, expurga a história de todos os casos de orquestração criados durante um intervalo de tempo especificado. Pode filtrar ainda mais as instâncias purgadas pelo estado do tempo de execução. O comando tem vários parâmetros:

* (opcional) : Expurgar o histórico de instâncias criadas após esta data/hora (UTC). ** `created-after` ** Iso 8601 datas formatadas aceites.
* (opcional) : Expurgar o histórico de instâncias criadas antes desta data/hora (UTC). ** `created-before` ** Iso 8601 datas formatadas aceites.
* (opcional) : Expurgar o histórico de casos com um determinado estatuto (por exemplo, correr ou completar). ** `runtime-status` ** Pode fornecer vários estados (espaço separados).
* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** Por predefinição, o nome do centro de tarefas no ficheiro [host.json](durable-functions-bindings.md#host-json) é utilizado.

O seguinte comando elimina a história de todos os casos falhados criados antes de 14 de novembro de 2018 às 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminar um centro de tarefas

Utilizando o comando `durable delete-task-hub` [Ferramentas Core Funções Azure,](../functions-run-local.md) pode eliminar todos os artefactos de armazenamento associados a um determinado centro de tarefas, incluindo mesas de armazenamento Azure, filas e bolhas. O comando tem dois parâmetros:

* (opcional) : Nome da definição de aplicação que contém a cadeia de ligação de armazenagem a utilizar. ** `connection-string-setting` ** A predefinição é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas das Funções Duráveis a utilizar. ** `task-hub-name` ** Por predefinição, o nome do centro de tarefas no ficheiro [host.json](durable-functions-bindings.md#host-json) é utilizado.

O comando seguinte elimina todos os dados `UserTest` de armazenamento do Azure associados ao centro de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referência http aPI incorporada para gestão por exemplo](durable-functions-http-api.md)
