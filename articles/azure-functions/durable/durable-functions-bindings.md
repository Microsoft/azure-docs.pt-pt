---
title: Encadernações para funções duradouras - Azure
description: Como utilizar gatilhos e encadernações para a extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: fe64945aeb71011e769ba4016ae36cb557f41f33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424262"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Encadernações para funções duradouras (Funções Azure)

A extensão [Funções Duradouras](durable-functions-overview.md) introduz duas novas ligações de gatilho que controlam a execução de funções orquestradoras e de atividade. Também introduz uma ligação de saída que atua como cliente para o tempo de funcionamento das Funções Duradouras.

## <a name="orchestration-trigger"></a>Gatilho de orquestração

O gatilho de orquestração permite-lhe autorizar [funções orquestradoras duráveis](durable-functions-types-features-overview.md#orchestrator-functions). Este gatilho suporta o início de novas instâncias de função orquestradora e o reinício de instâncias de função orquestradora existentes que estão "à espera" de uma tarefa.

Quando utiliza as ferramentas Visual Studio para Funções Azure, o gatilho de orquestração é configurado utilizando o atributo [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET.

Quando escreve funções de orquestrador em idiomas de script (por exemplo, scripts JavaScript ou C#), o gatilho de orquestração é definido pelo seguinte objeto JSON na `bindings` matriz dofunction.js *no* ficheiro:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` é o nome da orquestração que os clientes devem usar quando querem iniciar novos casos desta função orquestradora. Esta propriedade é opcional. Se não for especificado, o nome da função é utilizado.

Internamente, este gatilho encaderna uma série de filas na conta de armazenamento predefinido para a aplicação de função. Estas filas são detalhes de implementação interna da extensão, razão pela qual não estão explicitamente configuradas nas propriedades vinculativas.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui estão algumas notas sobre o gatilho da orquestração:

* **Threading único** - Um único fio de despachante é usado para toda a execução de funções orquestradoras em uma única instância hospedeira. Por esta razão, é importante garantir que o código de função do orquestrador é eficiente e não executa qualquer E/S. É igualmente importante garantir que este fio não faça qualquer trabalho de assíduo, exceto quando aguarda por tipos de tarefas específicos de Funções Duradouras.
* **Tratamento de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos da orquestração.
* **Visibilidade da mensagem** - As mensagens de disparo de orquestração são desocionadas e mantidas invisíveis por uma duração configurável. A visibilidade destas mensagens é renovada automaticamente desde que a aplicação de função esteja a funcionar e saudável.
* **Valores de retorno** - Os valores de retorno são serializados para JSON e persistidos para a tabela de história da orquestração no armazenamento da Tabela Azure. Estes valores de retorno podem ser consultados pela ligação do cliente da orquestração, descrita mais tarde.

> [!WARNING]
> As funções do orquestrador nunca devem utilizar qualquer entrada ou encadernação de saída que não seja a ligação do gatilho de orquestração. Ao fazê-lo, tem o potencial de causar problemas com a extensão de Tarefa Durável, uma vez que essas ligações podem não obedecer às regras de simples roscas e de I/S. Se quiser utilizar outras ligações, adicione-as a uma função de Atividade chamada da sua função Orquestradora.

> [!WARNING]
> As funções de orquestrador JavaScript nunca devem ser declaradas `async` .

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

A ligação do gatilho de orquestração suporta entradas e saídas. Aqui ficam algumas coisas a saber sobre o tratamento de entradas e saídas:

* **entradas** - .NET orchestration funções suportam apenas `DurableOrchestrationContext` como um tipo de parâmetro. A deserialização das entradas diretamente na assinatura da função não é suportada. O código deve utilizar o `GetInput<T>` método (.NET) ou `getInput` (JavaScript) para obter entradas de funções orquestradoras. Estas entradas devem ser tipos serializáveis JSON.
* **saídas** - A orquestração desencadeia valores de produção de suporte, bem como entradas. O valor de retorno da função é utilizado para atribuir o valor de saída e deve ser json-serializável. Se uma função .NET voltar `Task` `void` ou, um `null` valor será guardado como saída.

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
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo ['Funções Duradouras'.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (apenas funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto em JavaScript não representa o Contexto de Durabilidade, mas o contexto de [função como um todo](../functions-reference-node.md#context-object). Você pode aceder a métodos de orquestração através `context` da propriedade do `df` objeto.

> [!NOTE]
> Os orquestradores JavaScript devem usar `return` . A `durable-functions` biblioteca cuida de chamar o `context.done` método.

A maioria das funções orquestradoras chamam funções de atividade, por isso aqui está um exemplo "Hello World" que demonstra como chamar uma função de atividade:

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
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

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

O gatilho de atividade permite-lhe autorizar funções que são chamadas por funções orquestradoras, conhecidas como [funções de atividade.](durable-functions-types-features-overview.md#activity-functions)

Se estiver a utilizar o Visual Studio, o gatilho de atividade é configurado utilizando o `ActivityTriggerAttribute` atributo .NET.

Se estiver a utilizar o Código VS ou o portal Azure para o desenvolvimento, o gatilho de atividade é definido pelo seguinte objeto JSON na `bindings` matriz defunction.js *em*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Este valor é o nome que as funções do orquestrador usam para invocar esta função de atividade. Esta propriedade é opcional. Se não for especificado, o nome da função é utilizado.

Internamente, este gatilho encaderna uma fila na conta de armazenamento predefinido para a aplicação de função. Esta fila é um detalhe de implementação interna da extensão, razão pela qual não está explicitamente configurada nas propriedades de encadernação.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui estão algumas notas sobre o gatilho da atividade:

* **Threading** - Ao contrário do gatilho da orquestração, os gatilhos de atividade não têm quaisquer restrições em torno do roscar ou i/O. Podem ser tratados como funções regulares.
* **Tratamento de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos da atividade.
* **Visibilidade da mensagem** - As mensagens de desencadeamento de atividade são desocionadas e mantidas invisíveis durante uma duração configurável. A visibilidade destas mensagens é renovada automaticamente desde que a aplicação de função esteja a funcionar e saudável.
* **Valores de retorno** - Os valores de retorno são serializados para JSON e persistidos para a tabela de história da orquestração no armazenamento da Tabela Azure.

> [!WARNING]
> O backend de armazenamento para funções de atividade é um detalhe de implementação e o código do utilizador não deve interagir diretamente com estas entidades de armazenamento.

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

A atividade desencadeada por encadernação suporta entradas e saídas, tal como o gatilho da orquestração. Aqui ficam algumas coisas a saber sobre o tratamento de entradas e saídas:

* **entradas** - .NET a atividade funciona de forma nativa `DurableActivityContext` como tipo de parâmetro. Em alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que seja json-serializável. Quando `DurableActivityContext` utilizar, pode ligar `GetInput<T>` para buscar e deserizar a entrada da função de atividade.
* **saídas** - As funções de atividade suportam valores de saída, bem como entradas. O valor de retorno da função é utilizado para atribuir o valor de saída e deve ser json-serializável. Se uma função .NET voltar `Task` `void` ou, um `null` valor será guardado como saída.
* **Metadados** - As funções de atividade .NET podem ligar-se a um `string instanceId` parâmetro para obter o ID de instância da orquestração dos pais.

### <a name="trigger-sample"></a>Amostra de gatilho

O seguinte código de exemplo mostra como uma função de atividade simples "Hello World" pode parecer:

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
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableActivityContext` em vez de `IDurableActivityContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo ['Funções Duradouras'.](durable-functions-versions.md)

O tipo de parâmetro padrão para a ligação .NET `ActivityTriggerAttribute` é `IDurableActivityContext` . No entanto, a atividade .NET também suporta a ligação diretamente aos tipos serializáveis json (incluindo tipos primitivos), de modo que a mesma função poderia ser simplificada da seguinte forma:

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

As ligações JavaScript também podem ser passadas como parâmetros adicionais, pelo que a mesma função pode ser simplificada da seguinte forma:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Utilizando encadernações de entrada e saída

Pode utilizar encadernações regulares de entrada e saída para além da ligação do gatilho da atividade. Por exemplo, pode levar a entrada para a sua atividade vinculativa e enviar uma mensagem para um EventHub utilizando a ligação de saída eventHub:

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

* Começa-os.
* Questione o seu estado.
* Desemter-los.
* Envia-lhes eventos enquanto estão a correr.
* Purgar a história do caso.

Se estiver a utilizar o Visual Studio, pode ligar-se ao cliente de orquestração utilizando o `OrchestrationClientAttribute` atributo .NET para Funções Duradouras 1.0. A partir das Funções Duráveis 2.0, pode ligar-se ao cliente de orquestração utilizando o `DurableClientAttribute` atributo .NET.

Se estiver a utilizar linguagens de script (por exemplo, *ficheiros .csx* ou *.js)* para desenvolvimento, o gatilho de orquestração é definido pelo seguinte objeto JSON na `bindings` matriz defunction.js *em*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - Usado em cenários onde várias aplicações de funções partilham a mesma conta de armazenamento, mas precisam de ser isoladas umas das outras. Se não for especificado, o valor predefinido `host.json` é utilizado. Este valor deve corresponder ao valor utilizado pelas funções do orquestrador-alvo.
* `connectionName` - O nome de uma definição de aplicação que contém uma cadeia de ligação de conta de armazenamento. A conta de armazenamento representada por esta cadeia de ligação deve ser a mesma utilizada pelas funções do orquestrador-alvo. Se não for especificado, é utilizado o fio de ligação da conta de armazenamento predefinido para a aplicação de função.

> [!NOTE]
> Na maioria dos casos, recomendamos que omita estas propriedades e confie no comportamento padrão.

### <a name="client-usage"></a>Utilização do cliente

Nas funções .NET, normalmente liga-se a `IDurableOrchestrationClient` , o que lhe dá acesso total a todas as APIs do cliente de orquestração suportadas por Funções Duráveis. Nas funções duradouras mais antigas 2.x, liga-se à `DurableOrchestrationClient` classe. No JavaScript, as mesmas APIs são expostas pelo objeto devolvido de `getClient` . As APIs no objeto do cliente incluem:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativamente, as funções .NET podem ligar-se a `IAsyncCollector<T>` onde `T` está ou `StartOrchestrationArgs` `JObject` .

Para obter mais informações sobre estas operações, consulte a documentação da `IDurableOrchestrationClient` API.

### <a name="client-sample-visual-studio-development"></a>Amostra de cliente (desenvolvimento do Estúdio Visual)

Aqui está um exemplo de função desencadeada por filas que inicia uma orquestração "HelloWorld".

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
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo ['Funções Duradouras'.](durable-functions-versions.md)

### <a name="client-sample-not-visual-studio"></a>Amostra de cliente (não Estúdio Visual)

Se não estiver a utilizar o Visual Studio para desenvolvimento, pode criar os *seguintesfunction.jsno* ficheiro. Este exemplo mostra como configurar uma função desencadeada por filas que utiliza a ligação duradoura do cliente de orquestração:

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
> O JSON anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `orchestrationClient` em vez do tipo de `durableClient` gatilho. Para obter mais informações sobre as diferenças entre versões, consulte o artigo ['Funções Duradouras'.](durable-functions-versions.md)

Seguem-se amostras específicas da linguagem que iniciam novos exemplos de função de orquestrador.

#### <a name="c-script-sample"></a>Amostra de script C#

A amostra que se segue mostra como usar a ligação duradoura do cliente de orquestração para iniciar uma nova função a partir de uma função C# desencadeada pela fila:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo ['Funções Duradouras'.](durable-functions-versions.md)

#### <a name="javascript-sample"></a>Amostra javaScript

A amostra que se segue mostra como usar a ligação duradoura do cliente de orquestração para iniciar uma nova instância de função a partir de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre as instâncias iniciais podem ser encontrados na [gestão de exemplos.](durable-functions-instance-management.md)

## <a name="entity-trigger"></a>Gatilho de entidade

Os gatilhos de entidade permitem-lhe ter [funções de entidade autora.](durable-functions-entities.md) Este gatilho suporta eventos de processamento para uma instância específica da entidade.

Quando utiliza as ferramentas Visual Studio para Funções Azure, o gatilho da entidade é configurado utilizando o `EntityTriggerAttribute` atributo .NET.

> [!NOTE]
> Os gatilhos de entidades estão disponíveis a partir de Funções Duráveis 2.x.

Internamente, este gatilho encaderna uma série de filas na conta de armazenamento predefinido para a aplicação de função. Estas filas são detalhes de implementação interna da extensão, razão pela qual não estão explicitamente configuradas nas propriedades vinculativas.

### <a name="trigger-behavior"></a>Comportamento do acionador

Aqui estão algumas notas sobre o gatilho da entidade:

* **Roscado único**: É utilizado um único fio de despachante para processar operações para uma determinada entidade. Se várias mensagens forem enviadas a uma única entidade simultaneamente, as operações serão processadas uma vez por vez.
* **Tratamento de mensagens venenosas** - Não há suporte de mensagem venenosa nos gatilhos da entidade.
* **Visibilidade da mensagem** - As mensagens de gatilho da entidade são desocionadas e mantidas invisíveis por uma duração configurável. A visibilidade destas mensagens é renovada automaticamente desde que a aplicação de função esteja a funcionar e saudável.
* **Valores de devolução** - As funções da entidade não suportam valores de devolução. Existem APIs específicos que podem ser usados para salvar valores de estado ou de passar valores de volta para orquestrações.

Quaisquer alterações estatais feitas a uma entidade durante a sua execução serão automaticamente perduradas após a execução ter sido concluída.

### <a name="trigger-usage-net"></a>Utilização do gatilho (.NET)

Cada função de entidade tem um tipo de parâmetro `IDurableEntityContext` de, que tem os seguintes membros:

* **Nome da Entidade**: o nome da entidade atualmente executando.
* **EntityKey**: a chave da entidade atualmente executando.
* **EntityId**: o ID da entidade atualmente executando.
* **Operação Nome:** o nome da operação em curso.
* **HasState**: se a entidade existe, isto é, tem algum estado. 
* **GetState \<TState> ()**: obtém o estado atual da entidade. Se já não existe, é criada e inicializada para `default<TState>` . O `TState` parâmetro deve ser um tipo primitivo ou json-serializável. 
* **GetState \<TState> (initfunction)**: obtém o estado atual da entidade. Se já não existe, é criado chamando o `initfunction` parâmetro fornecido. O `TState` parâmetro deve ser um tipo primitivo ou json-serializável. 
* **SetState(arg)**: cria ou atualiza o estado da entidade. O `arg` parâmetro deve ser um objeto json-serializável ou primitivo.
* **DeleteState()**: elimina o estado da entidade. 
* **GetInput \<TInput> ()**: obtém a entrada para a operação atual. O `TInput` parâmetro do tipo deve ser um tipo primitivo ou json-serializável.
* **Return(arg)**: devolve um valor à orquestração que chamou a operação. O `arg` parâmetro deve ser um objeto primitivo ou json-serializável.
* **Entidade de Sinalização (EntityId, TempoUtc, operação, entrada)**: envia uma mensagem de ida para uma entidade. O `operation` parâmetro deve ser uma corda não nula, a opcional deve ser uma data `scheduledTimeUtc` UTC para invocar a operação, e o `input` parâmetro deve ser um objeto primitivo ou json-serializeable.
* **CreateNewOrchestration (orchestratorFunctionName, input)**: inicia uma nova orquestração. O `input` parâmetro deve ser um objeto primitivo ou json-serializável.

O `IDurableEntityContext` objeto passado para a função da entidade pode ser acedido usando a propriedade `Entity.Current` async-local. Esta abordagem é conveniente quando se utiliza o modelo de programação baseado em classe.

### <a name="trigger-sample-c-function-based-syntax"></a>Amostra de gatilho (sintaxe baseada na função C)

O código a seguir é um exemplo de uma simples entidade *Counter* implementada como uma função durável. Esta função define três `add` operações, e cada uma das quais opera num estado `reset` `get` inteiro.

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

Para obter mais informações sobre a sintaxe baseada na função e como usá-la, consulte [a Sintaxe Baseada na Função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Amostra de gatilho (sintaxe baseada na classe C)

O exemplo a seguir é uma implementação equivalente da `Counter` entidade utilizando classes e métodos.

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

O estado desta entidade é um objeto de `Counter` tipo, que contém um campo que armazena o valor atual do balcão. Para persistir neste objeto no armazenamento, é serializado e desercializado pela biblioteca [Json.NET.](https://www.newtonsoft.com/json) 

Para obter mais informações sobre a sintaxe baseada na classe e como usá-la, consulte [as classes de entidades definidoras](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> O método do ponto de entrada de função com o `[FunctionName]` atributo *deve* ser declarado quando utilizar as classes `static` de entidades. Os métodos de ponto de entrada não estáticos podem resultar na inicialização de múltiplos objetos e em comportamentos potencialmente indefinidos.

As classes de entidades têm mecanismos especiais para interagir com encadernações e injeção de dependência .NET. Para mais informações, consulte [a construção da Entidade.](durable-functions-dotnet-entities.md#entity-construction)

### <a name="trigger-sample-javascript"></a>Amostra de gatilho (JavaScript)

O código a seguir é um exemplo de uma simples entidade *Counter* implementada como uma função durável escrita no JavaScript. Esta função define três `add` operações, e cada uma das quais opera num estado `reset` `get` inteiro.

**function.json**
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
> As entidades duráveis estão disponíveis no JavaScript a partir da versão **1.3.0** do `durable-functions` pacote npm.

## <a name="entity-client"></a>Cliente de entidade

A ligação do cliente da entidade permite-lhe desencadear assincroticamente [funções de entidades.](#entity-trigger) Estas funções são por vezes referidas como [funções do cliente.](durable-functions-types-features-overview.md#client-functions)

Se estiver a utilizar o Visual Studio, pode ligar-se ao cliente da entidade utilizando o `DurableClientAttribute` atributo .NET.

> [!NOTE]
> O `[DurableClientAttribute]` também pode ser usado para ligar ao cliente da [orquestração.](#orchestration-client)

Se estiver a utilizar linguagens de script (por exemplo, *ficheiros .csx* ou *.js)* para desenvolvimento, o gatilho da entidade é definido pelo seguinte objeto JSON na `bindings` matriz defunction.js *em*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - Usado em cenários onde várias aplicações de funções partilham a mesma conta de armazenamento, mas precisam de ser isoladas umas das outras. Se não for especificado, o valor predefinido `host.json` é utilizado. Este valor deve corresponder ao valor utilizado pelas funções da entidade-alvo.
* `connectionName` - O nome de uma definição de aplicação que contém uma cadeia de ligação de conta de armazenamento. A conta de armazenamento representada por esta cadeia de ligação deve ser a mesma utilizada pelas funções da entidade-alvo. Se não for especificado, é utilizado o fio de ligação da conta de armazenamento predefinido para a aplicação de função.

> [!NOTE]
> Na maioria dos casos, recomendamos que omita as propriedades opcionais e confie no comportamento padrão.

### <a name="entity-client-usage"></a>Utilização do cliente da entidade

Nas funções .NET, normalmente liga-se `IDurableEntityClient` a, o que lhe dá acesso total a todas as APIs do cliente suportadas por Entidades Duráveis. Também pode ligar-se à `IDurableOrchestrationClient` interface, que dá acesso às APIs dos clientes tanto para entidades como para orquestrações. As APIs no objeto do cliente incluem:

* **ReadEntityStateAsync \<T>**: lê o estado de uma entidade. Devolve uma resposta que indica se a entidade-alvo existe e, em caso afirmativo, qual é o seu estado.
* **SignalEntityAsync**: envia uma mensagem de ida para uma entidade e aguarda que seja encosta.
* **ListEntitiesAsync**: consultas para o estado de várias entidades. As entidades podem ser questionadas pelo *nome* e *pela última vez de funcionamento.*

Não há necessidade de criar a entidade-alvo antes de enviar um sinal - o estado da entidade pode ser criado a partir da função de entidade que lida com o sinal.

> [!NOTE]
> É importante entender que os "sinais" enviados do cliente são simplesmente encadeados, para serem processados assíncroneamente mais tarde. Em particular, o `SignalEntityAsync` normalmente retorna antes mesmo de a entidade iniciar a operação, e não é possível recuperar o valor de retorno ou observar exceções. Se forem necessárias garantias mais fortes (por exemplo, para fluxos de trabalho), devem ser *utilizadas funções orquestradoras,* que podem aguardar a conclusão das operações da entidade, podendo processar valores de devolução e observar exceções.

### <a name="example-client-signals-entity-directly---c"></a>Exemplo: cliente sinaliza entidade diretamente - C #

Aqui está um exemplo de função desencadeada por filas que invoca uma entidade "Counter".

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

### <a name="example-client-signals-entity-via-interface---c"></a>Exemplo: cliente sinaliza entidade via interface - C #

Sempre que possível, recomendamos [o acesso às entidades através de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque fornece mais verificação de tipo. Por exemplo, suponha que a `Counter` entidade mencionada anteriormente implementou uma `ICounter` interface, definida da seguinte forma:

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

O código do cliente pode então ser utilizado `SignalEntityAsync<ICounter>` para gerar um proxy tipo-seguro:

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

O `proxy` parâmetro é um exemplo gerado dinamicamente de , que traduz `ICounter` internamente a chamada para a chamada equivalente `Add` (não escrita) para `SignalEntityAsync` .

> [!NOTE]
> As `SignalEntityAsync` APIs representam operações unidirecionais. Se uma entidade interagir as interfaces `Task<T>` voltar, o valor do `T` parâmetro será sempre nulo ou `default` .

Em particular, não faz sentido sinalizar a `Get` operação, uma vez que nenhum valor é devolvido. Em vez disso, os clientes podem usar `ReadStateAsync` o acesso direto ao estado do contador, ou podem iniciar uma função de orquestrador que chama a `Get` operação.

### <a name="example-client-signals-entity---javascript"></a>Exemplo: entidade de sinais de cliente - JavaScript

Aqui está um exemplo de função desencadeada por filas que assinala uma entidade "Counter" no JavaScript.

**function.json**
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
> As entidades duráveis estão disponíveis no JavaScript a partir da versão **1.3.0** do `durable-functions` pacote npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.jsnas definições

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de API HTTP incorporada para gestão de exemplo](durable-functions-http-api.md)
