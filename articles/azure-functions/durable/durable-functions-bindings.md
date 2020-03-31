---
title: Encadernações para funções duráveis - Azure
description: Como utilizar gatilhos e encadernações para a extensão funções duráveis para funções azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278223"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Encadernações para funções duráveis (Funções Azure)

A extensão [Das Funções Duráveis](durable-functions-overview.md) introduz duas novas ligações de gatilho que controlam a execução de funções de orquestrador e atividade. Introduz também uma ligação de saída que funciona como cliente para o tempo de funcionamento das Funções Duráveis.

## <a name="orchestration-trigger"></a>Gatilho de orquestração

O gatilho de orquestração permite-lhe autor de [funções orquestradoras duráveis.](durable-functions-types-features-overview.md#orchestrator-functions) Este gatilho suporta iniciar novas instâncias de função orquestradora e retomar as instâncias de função orquestradoras existentes que estão "à espera" de uma tarefa.

Quando utiliza as ferramentas do Estúdio Visual para funções Azure, o gatilho de orquestração é configurado utilizando o atributo [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET.

Quando escreve funções de orquestrador em línguas de script (por exemplo, escrita JavaScript ou C#), o `bindings` gatilho de orquestração é definido pelo seguinte objeto JSON na matriz do ficheiro *função.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`é o nome da orquestração que os clientes devem usar quando querem iniciar novos casos desta função orquestradora. Esta propriedade é opcional. Se não especificado, o nome da função é utilizado.

Internamente, este gatilho desencadeou sondagens vinculativas uma série de filas na conta de armazenamento padrão para a aplicação de função. Estas filas são detalhes internos de implementação da extensão, razão pela qual não estão explicitamente configuradas nas propriedades vinculativas.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui estão algumas notas sobre o gatilho da orquestração:

* **Fio único** - Um único fio de despacho é usado para qualquer execução de função orquestradornuma única instância anfitriã. Por esta razão, é importante garantir que o código de função orquestrador é eficiente e não executa nenhum E/O. Também é importante garantir que este fio não faça nenhum trabalho de sincronia, exceto quando se aguarda os tipos de tarefas específicos de Funções Duráveis.
* **Manipulação de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos da orquestração.
* **Visibilidade da mensagem** - As mensagens de gatilho de orquestração são desnorteadas e mantidas invisíveis por uma duração configurável. A visibilidade destas mensagens é renovada automaticamente enquanto a aplicação de funções estiver em execução e saudável.
* **Valores de retorno** - Os valores de retorno são serializados para a JSON e persistem na tabela de história da orquestração no armazenamento da Mesa Azure. Estes valores de retorno podem ser consultados pela ligação do cliente da orquestração, descrito mais tarde.

> [!WARNING]
> As funções de orquestrador nunca devem utilizar nenhuma ligação de entrada ou saída que não seja a ligação do gatilho da orquestração. Fazê-lo tem o potencial de causar problemas com a extensão da Tarefa Durável, uma vez que essas ligações podem não obedecer às regras de encaixe único e de I/S. Se quiser usar outras encadernações, adicione-as a uma função de Atividade chamada da sua função De Orquestrador.

> [!WARNING]
> As funções de orquestrador `async`JavaScript nunca devem ser declaradas .

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

A ligação do gatilho da orquestração suporta tanto as inputs como as saídas. Aqui estão algumas coisas para saber sobre a entrada e o manuseamento da saída:

* **inputs** - .NET funções `DurableOrchestrationContext` de orquestração suportam apenas como um tipo de parâmetro. A desserialização das inputs diretamente na assinatura da função não é suportada. O código `GetInput<T>` deve utilizar o `getInput` método (.NET) ou (JavaScript) para obter inputs de função orquestrador. Estas inputs devem ser tipos jSON-serializáveis.
* **saídas** - Orquestração desencadeia valores de saída de suporte, bem como inputs. O valor de retorno da função é utilizado para atribuir o valor de saída e deve ser jSON-serializável. Se uma função `Task` `void`.NET `null` retornar ou , um valor será guardado como a saída.

### <a name="trigger-sample"></a>Amostra de gatilho

O seguinte código de exemplo mostra como pode ser a função orquestradora "Hello World" mais simples:

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Versões de Funções Duráveis.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto no JavaScript não representa o Contexto de Orquestração Durável, mas sim o contexto de [função como um todo](../functions-reference-node.md#context-object). Você pode aceder a `context` métodos `df` de orquestração através da propriedade do objeto.

> [!NOTE]
> Os orquestradores JavaScript `return`devem usar . A `durable-functions` biblioteca cuida de `context.done` chamar o método.

A maioria das funções de orquestrador apela às funções de atividade, por isso aqui está um exemplo "Hello World" que demonstra como chamar uma função de atividade:

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Gatilho de atividade

O gatilho de atividade permite-lhe funções autorais que são chamadas por funções orquestradoras, conhecidas como funções de [atividade.](durable-functions-types-features-overview.md#activity-functions)

Se estiver a utilizar o Visual Studio, o `ActivityTriggerAttribute` gatilho de atividade está configurado utilizando o atributo .NET.

Se estiver a utilizar o Código VS ou o portal Azure para desenvolvimento, o `bindings` gatilho de atividade é definido pelo seguinte objeto JSON na matriz de *funções.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`é o nome da atividade. Este valor é o nome que as funções orquestradoras usam para invocar esta função de atividade. Esta propriedade é opcional. Se não especificado, o nome da função é utilizado.

Internamente, este gatilho dispara sondagens vinculativas uma fila na conta de armazenamento padrão para a aplicação de função. Esta fila é um detalhe de implementação interna da extensão, razão pela qual não está explicitamente configurada nas propriedades vinculativas.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui estão algumas notas sobre o gatilho da atividade:

* **Threading** - Ao contrário do gatilho da orquestração, os gatilhos de atividade não têm restrições em torno de threading ou I/O. Podem ser tratados como funções regulares.
* **Manipulação de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos de atividade.
* **Visibilidade da mensagem** - As mensagens de gatilho de atividade são desnorteadas e mantidas invisíveis durante uma duração configurável. A visibilidade destas mensagens é renovada automaticamente enquanto a aplicação de funções estiver em execução e saudável.
* **Valores de retorno** - Os valores de retorno são serializados para a JSON e persistem na tabela de história da orquestração no armazenamento da Mesa Azure.

> [!WARNING]
> O backend de armazenamento para funções de atividade é um detalhe de implementação e o código do utilizador não deve interagir diretamente com estas entidades de armazenamento.

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

A ligação do gatilho da atividade suporta tanto as inputs como as saídas, tal como o gatilho da orquestração. Aqui estão algumas coisas para saber sobre a entrada e o manuseamento da saída:

* **inputs** - .NET funções `DurableActivityContext` de atividade utilizam de forma nativa como um tipo de parâmetro. Alternativamente, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que seja jSON-serializável. Quando utilizar, `DurableActivityContext`pode `GetInput<T>` ligar para recolher e desserializar a entrada da função de atividade.
* **saídas** - Funções de atividade suportam valores de saída, bem como inputs. O valor de retorno da função é utilizado para atribuir o valor de saída e deve ser jSON-serializável. Se uma função `Task` `void`.NET `null` retornar ou , um valor será guardado como a saída.
* **metadados** - .NET funções de `string instanceId` atividade podem ligar-se a um parâmetro para obter a identificação da orquestração dos pais.

### <a name="trigger-sample"></a>Amostra de gatilho

O seguinte código de exemplo mostra como uma simples função de atividade "Hello World" pode parecer:

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableActivityContext` em vez de `IDurableActivityContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Versões de Funções Duráveis.](durable-functions-versions.md)

O tipo de parâmetro predefinido para a ligação .NET `ActivityTriggerAttribute` é `IDurableActivityContext`. No entanto, os gatilhos de atividade .NET também suportam a ligação direta aos tipos sérielizáveis da JSON (incluindo tipos primitivos), pelo que a mesma função poderia ser simplificada da seguinte forma:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

As encadernações JavaScript também podem ser transmitidas como parâmetros adicionais, pelo que a mesma função pode ser simplificada da seguinte forma:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Utilização de encadernações de entrada e saída

Pode utilizar encadernações regulares de entrada e saída para além da ligação do gatilho de atividade. Por exemplo, pode levar a entrada para a sua atividade encadernação e enviar uma mensagem para um EventHub usando a ligação de saída eventHub:

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

A ligação do cliente de orquestração permite-lhe escrever funções que interagem com funções orquestradoras. Estas funções são por vezes referidas como [funções do cliente.](durable-functions-types-features-overview.md#client-functions) Por exemplo, pode atuar em casos de orquestração das seguintes formas:

* Comecem-nos.
* Pergunta-lhes o estado.
* Acabar com eles.
* Envie-lhes eventos enquanto correm.
* Purgue a história da instância.

Se estiver a usar o Visual Studio, pode ligar-se ao cliente de orquestração utilizando o `OrchestrationClientAttribute` atributo .NET para Funções Duráveis 1.0. A partir das Funções Duráveis 2.0, pode ligar-se `DurableClientAttribute` ao cliente da orquestração utilizando o atributo .NET.

Se estiver a utilizar linguagens de script (por exemplo, *ficheiros .csx* ou *.js)* para desenvolvimento, `bindings` o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz de *funções.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Usados em cenários em que várias aplicações de função partilham a mesma conta de armazenamento, mas precisam de ser isoladas umas das outras. Se não especificado, o `host.json` valor predefinido é utilizado. Este valor deve corresponder ao valor utilizado pelas funções de orquestrador-alvo.
* `connectionName`- O nome de uma definição de aplicação que contém uma cadeia de ligação à conta de armazenamento. A conta de armazenamento representada por esta cadeia de ligação deve ser a mesma utilizada pelas funções de orquestrador-alvo. Se não especificado, a cadeia de ligação à conta de armazenamento predefinida para a aplicação de função é utilizada.

> [!NOTE]
> Na maioria dos casos, recomendamos que omita estas propriedades e confie no comportamento padrão.

### <a name="client-usage"></a>Utilização do cliente

Nas funções .NET, normalmente `IDurableOrchestrationClient`liga-se a , o que lhe dá acesso total a todas as APIs do cliente de orquestração suportadas por Funções Duráveis. Nas funções duráveis mais antigas 2.x, `DurableOrchestrationClient` em vez disso, liga-se à classe. No JavaScript, as mesmas APIs são `getClient`expostas pelo objeto devolvido de . As APIs sobre o objeto do cliente incluem:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativamente, as funções .NET `T` `StartOrchestrationArgs` podem `JObject`ligar-se ao `IAsyncCollector<T>` local onde está ou .

Para obter mais informações `IDurableOrchestrationClient` sobre estas operações, consulte a documentação da API.

### <a name="client-sample-visual-studio-development"></a>Amostra de cliente (desenvolvimento do Estúdio Visual)

Aqui está uma função de exemplo desencadeada pela fila que inicia uma orquestração "HelloWorld".

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Versões de Funções Duráveis.](durable-functions-versions.md)

### <a name="client-sample-not-visual-studio"></a>Amostra de cliente (não Estúdio Visual)

Se não estiver a utilizar o Visual Studio para o desenvolvimento, pode criar o seguinte ficheiro *função.json.* Este exemplo mostra como configurar uma função desencadeada pela fila que utiliza a ligação duradoura do cliente da orquestração:

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
> O JSON anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `orchestrationClient` em vez do `durableClient` tipo de gatilho. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Versões de Funções Duráveis.](durable-functions-versions.md)

Seguem-se amostras específicas da linguagem que iniciam novas instâncias de função orquestradora.

#### <a name="c-script-sample"></a>C# Script Sample

A amostra seguinte mostra como usar a ligação do cliente de orquestração durável para iniciar uma nova função a partir de uma função C# desencadeada pela fila:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, `DurableOrchestrationClient` deve utilizar `IDurableOrchestrationClient`o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Versões de Funções Duráveis.](durable-functions-versions.md)

#### <a name="javascript-sample"></a>Amostra javascript

A amostra que se segue mostra como utilizar a ligação do cliente de orquestração durável para iniciar uma nova função a partir de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre as instâncias iniciantes podem ser encontrados na [gestão](durable-functions-instance-management.md)de casos.

## <a name="entity-trigger"></a>Gatilho de entidade

Os gatilhos da entidade permitem-lhe o autor de funções de [entidade.](durable-functions-entities.md) Este gatilho suporta eventos de processamento para uma instância de entidade específica.

Quando utiliza as ferramentas do Estúdio Visual para funções Azure, o gatilho da entidade é configurado utilizando o `EntityTriggerAttribute` atributo .NET.

> [!NOTE]
> Os gatilhos da entidade estão disponíveis a partir de Funções Duráveis 2.x.

Internamente, este gatilho desencadeou sondagens vinculativas uma série de filas na conta de armazenamento padrão para a aplicação de função. Estas filas são detalhes internos de implementação da extensão, razão pela qual não estão explicitamente configuradas nas propriedades vinculativas.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui ficam algumas notas sobre o gatilho da entidade:

* **Fio único**: Um único fio de expedidor é utilizado para processar operações para uma determinada entidade. Se várias mensagens forem enviadas para uma única entidade simultaneamente, as operações serão processadas de uma vez por cada vez.
* **Manipulação de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos da entidade.
* **Visibilidade da mensagem** - As mensagens de gatilho da entidade são desnorteadas e mantidas invisíveis por uma duração configurável. A visibilidade destas mensagens é renovada automaticamente enquanto a aplicação de funções estiver em execução e saudável.
* **Valores de devolução** - As funções da entidade não suportam valores de retorno. Existem APIs específicos que podem ser usados para salvar o estado ou passar valores de volta para orquestrações.

Quaisquer alterações estatais efetuadas a uma entidade durante a sua execução serão automaticamente persistidas após a execução ter sido concluída.

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

Cada função de entidade `IDurableEntityContext`tem um tipo de parâmetro, que tem os seguintes membros:

* **Nome da entidade**: o nome da entidade atualmente em execução.
* **EntidadeChave**: a chave da entidade atualmente executante.
* **EntidadeId**: a identificação da entidade atualmente em execução.
* **Nome da operação**: o nome da operação atual.
* **HasState**: se a entidade existe, isto é, tem algum estado. 
* **GetState\<TState>()**: obtém o estado atual da entidade. Se já não existe, é criado e `default<TState>`inicializado para . O `TState` parâmetro deve ser um tipo primitivo ou sérievel jSON. 
* **GetState\<TState> (initfunction)**: obtém o estado atual da entidade. Se já não existe, é criado através `initfunction` da chamada parametómetro fornecida. O `TState` parâmetro deve ser um tipo primitivo ou sérievel jSON. 
* **SetState(arg)**: cria ou atualiza o estado da entidade. O `arg` parâmetro deve ser um objeto sérielizável pela JSON ou primitivo.
* **DeleteState()**: elimina o estado da entidade. 
* **GetInput\<TInput>()**: obtém a entrada para a operação atual. O `TInput` parâmetro do tipo deve ser um tipo primitivo ou sérievel jSON.
* **Devolução(arg)**: devolve um valor à orquestração que chamou a operação. O `arg` parâmetro deve ser um objeto primitivo ou sérielizável pela JSON.
* **SignalEntity (EntityId, timeUtc agendado, operação, entrada)**: envia uma mensagem de ida a uma entidade. O `operation` parâmetro deve ser uma corda não `scheduledTimeUtc` nula, o opcional deve ser uma data `input` de data UTC para invocar a operação, e o parâmetro deve ser um objeto primitivo ou sériedo JSON.
* **CreateNewOrchestration (orchestratorFunctionName, entrada)**: inicia uma nova orquestração. O `input` parâmetro deve ser um objeto primitivo ou sérielizável pela JSON.

O `IDurableEntityContext` objeto passado para a função `Entity.Current` da entidade pode ser acedido usando a propriedade async-local. Esta abordagem é conveniente quando se utiliza o modelo de programação baseado na classe.

### <a name="trigger-sample-c-function-based-syntax"></a>Amostra de gatilho (sintaxe baseada na função C#)

O seguinte código é um exemplo de uma simples entidade *Contra* implementada como uma função durável. Esta função define `add`três `reset`operações, e, `get`cada uma das quais opera num estado inteiro.

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

Para obter mais informações sobre a sintaxe baseada na função e como usá-la, consulte a [Sintaxe baseada na função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Amostra de gatilho (sintaxe baseada em classe C#)

O exemplo seguinte é uma `Counter` implementação equivalente da entidade utilizando classes e métodos.

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

O estado desta entidade é `Counter`um objeto de tipo, que contém um campo que armazena o valor atual do balcão. Para persistir este objeto no armazenamento, é serializado e desserializado pela biblioteca [Json.NET.](https://www.newtonsoft.com/json) 

Para obter mais informações sobre a sintaxe baseada na classe e como usá-la, consulte [as classes de entidades Definintes.](durable-functions-dotnet-entities.md#defining-entity-classes)

> [!NOTE]
> O método do ponto `[FunctionName]` de entrada `static` da função com o atributo *deve* ser declarado quando se utilizam classes de entidades. Os métodos não estáticos de pontode entrada podem resultar na inicialização de múltiplos objetos e potencialmente em outros comportamentos indefinidos.

As classes de entidades têm mecanismos especiais para interagir com encadernações e .NET de dependência. Para mais informações, consulte [a construção da Entidade.](durable-functions-dotnet-entities.md#entity-construction)

### <a name="trigger-sample-javascript"></a>Amostra de gatilho (JavaScript)

O código seguinte é um exemplo de uma simples entidade *Contra* implementada como uma função durável escrita no JavaScript. Esta função define `add`três `reset`operações, e, `get`cada uma das quais opera num estado inteiro.

**função.json**
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

**index.js**
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
> Entidades duráveis estão disponíveis no JavaScript a partir `durable-functions` da versão **1.3.0** do pacote npm.

## <a name="entity-client"></a>Cliente de entidade

A ligação do cliente da entidade permite-lhe ativar [assincronicamente funções](#entity-trigger)da entidade. Estas funções são por vezes referidas como [funções do cliente.](durable-functions-types-features-overview.md#client-functions)

Se estiver a utilizar o Visual Studio, pode ligar-se ao cliente da entidade utilizando o `DurableClientAttribute` atributo .NET.

> [!NOTE]
> A `[DurableClientAttribute]` lata também pode ser usada para se ligar ao cliente da [orquestração.](#orchestration-client)

Se estiver a utilizar linguagens de script (por exemplo, *ficheiros .csx* ou *.js)* para desenvolvimento, o gatilho da entidade é definido pelo seguinte objeto JSON na `bindings` matriz de *funções.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Usados em cenários em que várias aplicações de função partilham a mesma conta de armazenamento, mas precisam de ser isoladas umas das outras. Se não especificado, o `host.json` valor predefinido é utilizado. Este valor deve corresponder ao valor utilizado pelas funções da entidade-alvo.
* `connectionName`- O nome de uma definição de aplicação que contém uma cadeia de ligação à conta de armazenamento. A conta de armazenamento representada por esta cadeia de ligação deve ser a mesma utilizada pelas funções da entidade-alvo. Se não especificado, a cadeia de ligação à conta de armazenamento predefinida para a aplicação de função é utilizada.

> [!NOTE]
> Na maioria dos casos, recomendamos que omita as propriedades opcionais e confie no comportamento padrão.

### <a name="entity-client-usage"></a>Utilização do cliente da entidade

Nas funções .NET, normalmente `IDurableEntityClient`liga-se a, o que lhe dá acesso total a todas as APIs do cliente suportadas por Entidades Duráveis. Também pode ligar-se à `IDurableOrchestrationClient` interface, que dá acesso a APIs clientes tanto para entidades como para orquestrações. As APIs sobre o objeto do cliente incluem:

* **ReadEntityStateAsync\<T>**: lê o estado de uma entidade. Devolve uma resposta que indica se a entidade alvo existe, e se sim, qual é o seu estado.
* **SignalEntityAsync**: envia uma mensagem de ida a uma entidade e aguarda que seja enqueced.
* **ListEntitiesAsync**: consultas para o estado de várias entidades. As entidades podem ser consultadas pelo *nome* e *pela última operação.*

Não há necessidade de criar a entidade-alvo antes de enviar um sinal - o estado da entidade pode ser criado a partir da função da entidade que trata do sinal.

> [!NOTE]
> É importante entender que os "sinais" enviados pelo cliente são simplesmente enqueuados, para serem processados assíncronamente mais tarde. Em particular, `SignalEntityAsync` o normalmente regressa antes mesmo de a entidade iniciar a operação, e não é possível recuperar o valor de retorno ou observar exceções. Se forem necessárias garantias mais fortes (por exemplo, para fluxos de trabalho), devem ser *utilizadas funções de orquestração,* que podem esperar que as operações da entidade sejam concluídas, podendo processar valores de retorno e observar exceções.

### <a name="example-client-signals-entity-directly---c"></a>Exemplo: entidade de sinais de cliente diretamente - C #

Aqui está uma função de exemplo desencadeada pela fila que invoca uma entidade "Contador".

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

### <a name="example-client-signals-entity-via-interface---c"></a>Exemplo: entidade de sinais de cliente via interface - C #

Sempre que possível, recomendamos [o acesso a entidades através](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) de interfaces porque fornece mais verificação de tipo. Por exemplo, `Counter` suponha que a `ICounter` entidade mencionada anteriormente implementou uma interface, definida da seguinte forma:

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

O código do `SignalEntityAsync<ICounter>` cliente pode então ser usado para gerar um proxy seguro do tipo:

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

O `proxy` parâmetro é um exemplo gerado `ICounter`dinamicamente de , `Add` que traduz internamente a `SignalEntityAsync`chamada para a chamada equivalente (não digitada) para .

> [!NOTE]
> As `SignalEntityAsync` APIs representam operações de sentido único. Se uma entidade `Task<T>`não o fizer, o valor `T` do `default`parâmetro será sempre nulo ou .

Em particular, não faz sentido `Get` sinalizar a operação, uma vez que não é devolvido qualquer valor. Em vez disso, `ReadStateAsync` os clientes podem usar para aceder diretamente ao contraestado, ou podem iniciar uma função orquestradora que chama a `Get` operação.

### <a name="example-client-signals-entity---javascript"></a>Exemplo: entidade de sinais de cliente - JavaScript

Aqui está uma função de exemplo desencadeada pela fila que assinala uma entidade "Counter" no JavaScript.

**função.json**
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Entidades duráveis estão disponíveis no JavaScript a partir `durable-functions` da versão **1.3.0** do pacote npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>configurações host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência http aPI incorporada para gestão por exemplo](durable-functions-http-api.md)
