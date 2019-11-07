---
title: Associações para o Durable Functions-Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d7e77907e2d394d2a4c1679ec50af8d4f72fa6f1
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615058"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Durable Functions (Azure Functions)

A extensão [Durable Functions](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução do orquestrador e funções de atividade. Ele também apresenta uma associação de saída que atua como um cliente para o tempo de execução de Durable Functions.

## <a name="orchestration-trigger"></a>Gatilho de orquestração

O gatilho de orquestração permite que você crie [funções de orquestrador duráveis](durable-functions-types-features-overview.md#orchestrator-functions). Esse gatilho dá suporte à inicialização de novas instâncias de função de orquestrador e à retomada das instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

Quando você usa as ferramentas do Visual Studio para Azure Functions, o gatilho Orchestration é configurado usando o atributo .NET [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .

Quando você escreve funções de orquestrador em linguagens de script (por exemplo, C# JavaScript ou scripts), o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz de `bindings` do arquivo *Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` é o nome da orquestração que os clientes devem usar quando desejarem iniciar novas instâncias dessa função de orquestrador. Esta propriedade é opcional. Se não for especificado, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão para o aplicativo de funções. Essas filas são detalhes de implementação interna da extensão, que é o motivo pelo qual elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de orquestração:

* **Thread único** -um único thread de Dispatcher é usado para toda a execução da função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma e/s. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar em tipos de tarefa específicos de Durable Functions.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas em gatilhos de orquestração.
* **Visibilidade de mensagem** – as mensagens de gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, contanto que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** – os valores de retorno são serializados para JSON e persistidos para a tabela de histórico de orquestração no armazenamento de tabelas do Azure. Esses valores de retorno podem ser consultados pela Associação de cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída diferente da Associação de gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de tarefa durável, pois essas associações podem não obedecer às regras de thread único e e/s. Se você quiser usar outras associações, adicione-as a uma função de atividade chamada de sua função de orquestrador.

> [!WARNING]
> As funções de orquestrador do JavaScript nunca devem ser declaradas `async`.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Aqui estão algumas coisas que você precisa saber sobre o tratamento de entrada e saída:

* **entradas** – as funções de orquestração do .NET dão suporte apenas `DurableOrchestrationContext` como um tipo de parâmetro. Não há suporte para a desserialização de entradas diretamente na assinatura de função. O código deve usar o método `GetInput<T>` (.NET) ou `getInput` (JavaScript) para buscar entradas de função de orquestrador. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – os gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor de retorno da função é usado para atribuir o valor de saída e deve ser serializável por JSON. Se uma função do .NET retornar `Task` ou `void`, um valor de `null` será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como a função de orquestrador "Olá, Mundo" mais simples pode ser semelhante a:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O objeto `context` no JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo](../functions-reference-node.md#context-object). Você pode acessar os métodos de orquestração por meio da propriedade `df` do objeto de `context`.

> [!NOTE]
> Os orquestradores de JavaScript devem usar `return`. A biblioteca de `durable-functions` cuida da chamada do método `context.done`.

A maioria das funções de orquestrador chamam funções de atividade, portanto, aqui está um exemplo de "Olá, Mundo" que demonstra como chamar uma função de atividade:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Gatilho de atividade

O gatilho de atividade permite que você crie funções que são chamadas por funções de orquestrador, conhecidas como [funções de atividade](durable-functions-types-features-overview.md#activity-functions).

Se você estiver usando o Visual Studio, o gatilho de atividade será configurado usando o atributo `ActivityTriggerAttribute` .NET.

Se você estiver usando VS Code ou o portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na matriz de `bindings` de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Esse valor é o nome que as funções de orquestrador usam para invocar essa função de atividade. Esta propriedade é opcional. Se não for especificado, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma fila na conta de armazenamento padrão para o aplicativo de funções. Essa fila é um detalhe de implementação interna da extensão, que é o motivo pelo qual ela não é configurada explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de atividade:

* **Threading** – diferentemente do gatilho de orquestração, os gatilhos de atividade não têm restrições em relação ao Threading ou e/s. Eles podem ser tratados como funções regulares.
* **Manipulação de mensagens suspeitas** -não há suporte para mensagens suspeitas em gatilhos de atividade.
* **Visibilidade de mensagem** -as mensagens de gatilho de atividade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, contanto que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** – os valores de retorno são serializados para JSON e persistidos para a tabela de histórico de orquestração no armazenamento de tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento para funções de atividade é um detalhe de implementação e o código do usuário não deve interagir com essas entidades de armazenamento diretamente.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de atividade dá suporte a entradas e saídas, assim como o gatilho de orquestração. Aqui estão algumas coisas que você precisa saber sobre o tratamento de entrada e saída:

* **entradas** – as funções de atividade do .NET usam nativamente `DurableActivityContext` como um tipo de parâmetro. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que seja serializável por JSON. Ao usar `DurableActivityContext`, você pode chamar `GetInput<T>` para buscar e desserializar a entrada da função de atividade.
* **Outputs** -as funções de atividade dão suporte a valores de saída, bem como entradas. O valor de retorno da função é usado para atribuir o valor de saída e deve ser serializável por JSON. Se uma função do .NET retornar `Task` ou `void`, um valor de `null` será salvo como a saída.
* **metadados** -as funções de atividade .net podem ser associadas a um parâmetro `string instanceId` para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como uma função de atividade "Olá, Mundo" simples pode ser semelhante a:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableActivityContext` em vez de `IDurableActivityContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

O tipo de parâmetro padrão para a associação .NET `ActivityTriggerAttribute` é `IDurableActivityContext`. No entanto, os gatilhos de atividade do .NET também dão suporte à associação diretamente a tipos serializados em JSON (incluindo tipos primitivos), portanto, a mesma função pode ser simplificada da seguinte maneira:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

As associações de JavaScript também podem ser passadas como parâmetros adicionais, portanto, a mesma função pode ser simplificada da seguinte maneira:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Usando associações de entrada e saída

Você pode usar associações de entrada e saída regulares, além da Associação de gatilho de atividade. Por exemplo, você pode pegar a entrada para sua associação de atividade e enviar uma mensagem para um EventHub usando a associação de saída do EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Cliente de orquestração

A associação de cliente de orquestração permite que você grave funções que interagem com funções de orquestrador. Essas funções são, às vezes, chamadas de [funções de cliente](durable-functions-types-features-overview.md#client-functions). Por exemplo, você pode agir em instâncias de orquestração das seguintes maneiras:

* Inicie-os.
* Consultar seu status.
* Encerre-os.
* Enviar eventos para eles enquanto eles estiverem em execução.
* Limpar o histórico de instância.

Se você estiver usando o Visual Studio, poderá associar ao cliente Orchestration usando o atributo `OrchestrationClientAttribute` .NET para Durable Functions 1,0. A partir do Durable Functions 2,0, você pode associar ao cliente de orquestração usando o atributo `DurableClientAttribute` .NET.

Se você estiver usando linguagens de script (por exemplo, arquivos *. CSX* ou *. js* ) para desenvolvimento, o gatilho de orquestração será definido pelo seguinte objeto JSON na `bindings` matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`-usados em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de orquestrador de destino.
* `connectionName`-o nome de uma configuração de aplicativo que contém uma cadeia de conexão da conta de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de orquestrador de destino. Se não for especificado, a cadeia de conexão de conta de armazenamento padrão para o aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, é recomendável omitir essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Uso do cliente

Em funções do .NET, você normalmente se associa a `IDurableOrchestrationClient`, que fornece acesso completo a todas as APIs de cliente de orquestração suportadas pelo Durable Functions. Nas versões mais antigas Durable Functions 2. x, em vez disso, você faz a ligação com a classe `DurableOrchestrationClient`. No JavaScript, as mesmas APIs são expostas pelo objeto retornado de `getClient`. As APIs no objeto de cliente incluem:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Como alternativa, as funções do .NET podem ser associadas a `IAsyncCollector<T>` onde `T` é `StartOrchestrationArgs` ou `JObject`.

Para obter mais informações sobre essas operações, consulte a documentação da API do `IDurableOrchestrationClient`.

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento do Visual Studio)

Aqui está um exemplo de função disparada por fila que inicia uma orquestração "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (não Visual Studio)

Se você não estiver usando o Visual Studio para desenvolvimento, poderá criar o seguinte arquivo *Function. JSON* . Este exemplo mostra como configurar uma função disparada por fila que usa a associação de cliente de orquestração durável:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> O JSON anterior é para o Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `orchestrationClient` em vez do `durableClient` como o tipo de gatilho. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

Veja a seguir exemplos específicos de idioma que iniciam novas instâncias de função de orquestrador.

#### <a name="c-script-sample"></a>C#Exemplo de script

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma C# função disparada por fila:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre como iniciar instâncias podem ser encontrados no [Gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Gatilho de entidade

Os gatilhos de entidade permitem que você crie [funções de entidade](durable-functions-entities.md). Esse gatilho dá suporte ao processamento de eventos para uma instância de entidade específica.

Quando você usa as ferramentas do Visual Studio para Azure Functions, o gatilho de entidade é configurado usando o atributo `EntityTriggerAttribute` .NET.

> [!NOTE]
> Os gatilhos de entidade estão disponíveis a partir do Durable Functions 2. x.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão para o aplicativo de funções. Essas filas são detalhes de implementação interna da extensão, que é o motivo pelo qual elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de entidade:

* **Thread único**: um único thread de Dispatcher é usado para processar operações para uma entidade específica. Se várias mensagens forem enviadas a uma única entidade simultaneamente, as operações serão processadas uma por vez.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas em gatilhos de entidade.
* **Visibilidade de mensagem** -as mensagens de gatilho de entidade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, contanto que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** -as funções de entidade não dão suporte a valores de retorno. Há APIs específicas que podem ser usadas para salvar os valores de estado ou passar de volta para orquestrações.

Qualquer alteração de estado feita em uma entidade durante sua execução será persistida automaticamente após a conclusão da execução.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

Cada função de entidade tem um tipo de parâmetro de `IDurableEntityContext`, que tem os seguintes membros:

* **EntityName**: o nome da entidade atualmente em execução.
* **EntityKey**: a chave da entidade atualmente em execução.
* **EntityId**: a ID da entidade em execução no momento.
* **OperationName**: o nome da operação atual.
* **HasState**: se a entidade existe, ou seja, tem algum estado. 
* **GetState\<TState > ()** : Obtém o estado atual da entidade. Se ele ainda não existir, ele será criado e inicializado para `default<TState>`. O parâmetro `TState` deve ser um tipo primitivo ou serializável por JSON. 
* **GetState\<TState > (initfunction)** : Obtém o estado atual da entidade. Se ele ainda não existir, ele será criado chamando o parâmetro de `initfunction` fornecido. O parâmetro `TState` deve ser um tipo primitivo ou serializável por JSON. 
* **SetState (ARG)** : cria ou atualiza o estado da entidade. O parâmetro `arg` deve ser um objeto serializável em JSON ou primitivo.
* **Deletestate ()** : exclui o estado da entidade. 
* **Getinput\<TInput > ()** : Obtém a entrada para a operação atual. O parâmetro de tipo de `TInput` deve ser um tipo primitivo ou serializável por JSON.
* **Return (ARG)** : retorna um valor para a orquestração que chamou a operação. O parâmetro `arg` deve ser um objeto primitivo ou serializável por JSON.
* **SignalEntity (EntityId, operação, entrada)** : envia uma mensagem unidirecional para uma entidade. O parâmetro `operation` deve ser uma cadeia de caracteres não nula e o parâmetro `input` deve ser um objeto primitivo ou serializável em JSON.
* **CreateNewOrchestration (orchestratorFunctionName, entrada)** : inicia uma nova orquestração. O parâmetro `input` deve ser um objeto primitivo ou serializável por JSON.

O objeto `IDurableEntityContext` passado para a função de entidade pode ser acessado usando a propriedade `Entity.Current` Async-local. Essa abordagem é conveniente ao usar o modelo de programação baseado em classe.

### <a name="trigger-sample-c-function-based-syntax"></a>Exemplo de gatilhoC# (sintaxe baseada em função)

O código a seguir é um exemplo de uma entidade de *contador* simples implementada como uma função durável. Essa função define três operações, `add`, `reset`e `get`, cada uma operando em um estado de inteiro.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Para obter mais informações sobre a sintaxe baseada em função e como usá-la, consulte [sintaxe baseada em função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Exemplo de gatilhoC# (sintaxe baseada em classe)

O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando classes e métodos.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

O estado dessa entidade é um objeto do tipo `Counter`, que contém um campo que armazena o valor atual do contador. Para persistir esse objeto no armazenamento, ele é serializado e desserializado pela biblioteca [JSON.net](https://www.newtonsoft.com/json) . 

Para obter mais informações sobre a sintaxe baseada em classe e como usá-la, consulte [definindo classes de entidade](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> O método de ponto de entrada de função com o atributo `[FunctionName]` *deve* ser declarado `static` ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

As classes de entidade têm mecanismos especiais para interagir com associações e injeção de dependência do .NET. Para obter mais informações, consulte [construção de entidade](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Exemplo de gatilho (JavaScript)

O código a seguir é um exemplo de uma entidade de *contador* simples implementada como uma função durável escrita em JavaScript. Essa função define três operações, `add`, `reset`e `get`, cada uma operando em um estado de inteiro.

**function. JSON**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index. js**
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

> [!NOTE]
> As entidades duráveis estão disponíveis em JavaScript, começando com a versão **1.3.0** do pacote do `durable-functions` NPM.

## <a name="entity-client"></a>Cliente de entidade

A associação de cliente de entidade permite que você dispare de forma assíncrona [funções de entidade](#entity-trigger). Essas funções são, às vezes, chamadas de [funções de cliente](durable-functions-types-features-overview.md#client-functions).

Se você estiver usando o Visual Studio, poderá associar ao cliente da entidade usando o atributo `DurableClientAttribute` .NET.

> [!NOTE]
> O `[DurableClientAttribute]` também pode ser usado para associar ao [cliente de orquestração](#orchestration-client).

Se você estiver usando linguagens de script (por exemplo, arquivos *. CSX* ou *. js* ) para desenvolvimento, o gatilho de entidade será definido pelo seguinte objeto JSON na `bindings` matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`-usados em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de entidade de destino.
* `connectionName`-o nome de uma configuração de aplicativo que contém uma cadeia de conexão da conta de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de entidade de destino. Se não for especificado, a cadeia de conexão de conta de armazenamento padrão para o aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, recomendamos que você omita as propriedades opcionais e dependa do comportamento padrão.

### <a name="entity-client-usage"></a>Uso de cliente de entidade

Em funções do .NET, você normalmente se associa a `IDurableEntityClient`, que fornece acesso completo a todas as APIs de cliente com suporte de entidades duráveis. Você também pode associar à interface `IDurableOrchestrationClient`, que fornece acesso a APIs de cliente para entidades e orquestrações. As APIs no objeto de cliente incluem:

* **ReadEntityStateAsync\<t >** : lê o estado de uma entidade. Ele retorna uma resposta que indica se a entidade de destino existe e, em caso afirmativo, qual é seu estado.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda que ela seja enfileirada.

Não é necessário criar a entidade de destino antes de enviar um sinal-o estado da entidade pode ser criado de dentro da função de entidade que manipula o sinal.

> [!NOTE]
> É importante entender que os "sinais" enviados do cliente são simplesmente enfileirados, para serem processados de forma assíncrona em um momento posterior. Em particular, a `SignalEntityAsync` geralmente retorna antes que a entidade inicie a operação, e não é possível retornar o valor de retorno ou observar as exceções. Se forem necessárias garantias mais fortes (por exemplo, para fluxos de trabalho), as *funções de orquestrador* deverão ser usadas, o que pode esperar que as operações de entidade sejam concluídas e pode processar valores de retorno e observar exceções.

### <a name="example-client-signals-entity-directly---c"></a>Exemplo: cliente sinaliza a entidade diretamente-C#

Aqui está um exemplo de função disparada por fila que invoca uma entidade "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Exemplo: o cliente sinaliza a entidade via interface-C#

Sempre que possível, é recomendável [acessar entidades por meio de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque ela fornece mais verificação de tipo. Por exemplo, suponha que a entidade `Counter` mencionada anteriormente implementou uma interface `ICounter`, definida da seguinte maneira:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

O código do cliente pode usar `SignalEntityAsync<ICounter>` para gerar um proxy de tipo seguro:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

O parâmetro `proxy` é uma instância gerada dinamicamente de `ICounter`, que converte internamente a chamada para `Add` na chamada equivalente (não tipada) para `SignalEntityAsync`.

> [!NOTE]
> As APIs de `SignalEntityAsync` representam operações unidirecionais. Se uma interface de entidade retornar `Task<T>`, o valor do parâmetro `T` será sempre nulo ou `default`.

Em particular, não faz sentido sinalizar a operação de `Get`, uma vez que nenhum valor é retornado. Em vez disso, os clientes podem usar qualquer `ReadStateAsync` para acessar o estado do contador diretamente ou podem iniciar uma função de orquestrador que chama a operação de `Get`.

### <a name="example-client-signals-entity---javascript"></a>Exemplo: entidade de sinais de cliente-JavaScript

Aqui está um exemplo de função disparada por fila que sinaliza uma entidade "Counter" em JavaScript.

**function. JSON**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index. js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> As entidades duráveis estão disponíveis em JavaScript, começando com a versão **1.3.0** do pacote do `durable-functions` NPM.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>configurações de host. JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de API HTTP interna para gerenciamento de instância](durable-functions-http-api.md)
