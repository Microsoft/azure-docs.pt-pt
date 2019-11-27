---
title: Entidades duráveis-Azure Functions
description: Saiba quais são as entidades duráveis e como usá-las na extensão de Durable Functions para Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: aa4d1c4bfab349659c42a34ca5a73f676a2ea2b8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232919"
---
# <a name="entity-functions"></a>Funções de entidade

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade gerenciam o estado de uma entidade explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.
As entidades fornecem um meio para escalar horizontalmente os aplicativos distribuindo o trabalho entre várias entidades, cada uma com um estado de tamanho modesto.

> [!NOTE]
> Funções de entidade e funcionalidade relacionada só estão disponíveis no Durable Functions 2,0 e superior.

## <a name="general-concepts"></a>Conceitos gerais

As entidades se comportam um pouco como pequenos serviços que se comunicam por meio de mensagens. Cada entidade tem uma identidade exclusiva e um estado interno (se existir). Assim como os serviços ou objetos, as entidades executam operações quando solicitado a fazer isso. Quando uma operação é executada, ela pode atualizar o estado interno da entidade. Ele também pode chamar serviços externos e aguardar uma resposta. As entidades se comunicam com outras entidades, orquestrações e clientes usando mensagens que são enviadas implicitamente por meio de filas confiáveis. 

Para evitar conflitos, todas as operações em uma única entidade têm garantia de execução em série, ou seja, uma após a outra. 

### <a name="entity-id"></a>ID da entidade
As entidades são acessadas por meio de um identificador exclusivo, a *ID da entidade*. Uma ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em um:

* **Nome da entidade**, que é um nome que identifica o tipo da entidade. Um exemplo é "Counter". Esse nome deve corresponder ao nome da função de entidade que implementa a entidade. Não é sensível a maiúsculas e minúsculas.
* **Chave de entidade**, que é uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades de mesmo nome. Um exemplo é um GUID.

Por exemplo, uma função de entidade `Counter` pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo tem uma ID de entidade exclusiva, como `@Counter@Game1` e `@Counter@Game2`. Todas as operações direcionadas a uma entidade específica exigem a especificação de uma ID de entidade como um parâmetro.

### <a name="entity-operations"></a>Operações de entidade ###

Para invocar uma operação em uma entidade, especifique:

* **ID da entidade** da entidade de destino.
* **Nome da operação**, que é uma cadeia de caracteres que especifica a operação a ser executada. Por exemplo, a entidade `Counter` poderia dar suporte a operações `add`, `get`ou `reset`.
* **Entrada de operação**, que é um parâmetro de entrada opcional para a operação. Por exemplo, a operação Adicionar pode usar um valor inteiro como a entrada.

As operações podem retornar um valor de resultado ou um resultado de erro, como um erro de JavaScript ou uma exceção .NET. Esse resultado ou erro pode ser observado por orquestrações que chamaram a operação.

Uma operação de entidade também pode criar, ler, atualizar e excluir o estado da entidade. O estado da entidade é sempre permanentemente persistido no armazenamento.

## <a name="define-entities"></a>Definir entidades

Atualmente, as duas APIs distintas para definir entidades são:

A **sintaxe baseada em função**, em que as entidades são representadas como funções e as operações são expedidas explicitamente pelo aplicativo. Essa sintaxe funciona bem para entidades com estado simples, poucas operações ou um conjunto dinâmico de operações como em estruturas de aplicativo. Essa sintaxe pode ser entediante de ser mantida porque não detecta erros de tipo no momento da compilação.

**Sintaxe baseada em classe**, em que as entidades e as operações são representadas por classes e métodos. Essa sintaxe produz um código mais fácil de ler e permite que as operações sejam invocadas de forma segura de tipo. A sintaxe baseada em classe é uma camada fina sobre a sintaxe baseada em função; portanto, ambas as variantes podem ser usadas de maneira intercambiável no mesmo aplicativo.

### <a name="example-function-based-syntax---c"></a>Exemplo: sintaxe baseada em função-C#

O código a seguir é um exemplo de uma entidade `Counter` simples implementada como uma função durável. Essa função define três operações, `add`, `reset`e `get`, cada uma operando em um estado de inteiro.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Para obter mais informações sobre a sintaxe baseada em função e como usá-la, consulte [sintaxe baseada em função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exemplo: sintaxe baseada em classe-C#

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

### <a name="example-javascript-entity"></a>Exemplo: entidade JavaScript

As entidades duráveis estão disponíveis em JavaScript, começando com a versão **1.3.0** do pacote do `durable-functions` NPM. O código a seguir é a entidade `Counter` implementada como uma função durável escrita em JavaScript.

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

## <a name="access-entities"></a>Acessar entidades

As entidades podem ser acessadas usando uma comunicação unidirecional ou bidirecional. A terminologia a seguir distingue as duas formas de comunicação: 

* **Chamar** uma entidade usa comunicação bidirecional (viagem de ida e volta). Você envia uma mensagem de operação para a entidade e aguarda a mensagem de resposta antes de continuar. A mensagem de resposta pode fornecer um valor de resultado ou um resultado de erro, como um erro de JavaScript ou uma exceção .NET. Esse resultado ou erro é então observado pelo chamador.
* **Sinalizar** uma entidade usa comunicação unidirecional (acionar e esquecer). Você envia uma mensagem de operação, mas não aguarda uma resposta. Embora a mensagem tenha garantia de ser entregue eventualmente, o remetente não sabe quando e não pode observar nenhum valor ou erro de resultado.

As entidades podem ser acessadas de dentro de funções de cliente, de dentro de funções de orquestrador, ou de dentro de funções de entidade. Nem todas as formas de comunicação têm suporte em todos os contextos:

* De dentro dos clientes, você pode sinalizar entidades e pode ler o estado da entidade.
* De dentro de orquestrações, você pode sinalizar entidades e pode chamar entidades.
* De dentro de entidades, você pode sinalizar entidades.

Os exemplos a seguir ilustram essas várias maneiras de acessar entidades.

> [!NOTE]
> Para simplificar, os exemplos a seguir mostram a sintaxe com rigidez de tipos para acessar entidades. Em geral, é recomendável que você [acesse entidades por meio de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque ele fornece mais verificação de tipo.

### <a name="example-client-signals-an-entity"></a>Exemplo: o cliente sinaliza uma entidade

Para acessar entidades de uma função comum do Azure, que também é conhecida como uma função de cliente, use a [Associação de saída do cliente de entidade](durable-functions-bindings.md#entity-client). O exemplo a seguir mostra uma função disparada por fila sinalizando uma entidade usando essa associação.

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

O *sinal* de termo significa que a invocação de API de entidade é unidirecional e assíncrona. Não é possível que uma função de cliente saiba quando a entidade processou a operação. Além disso, a função cliente não pode observar quaisquer valores de resultado ou exceções. 

### <a name="example-client-reads-an-entity-state"></a>Exemplo: o cliente lê um estado de entidade

As funções de cliente também podem consultar o estado de uma entidade, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

As consultas de estado de entidade são enviadas para o armazenamento de controle durável e retornam o estado persistido mais recentemente da entidade. Esse estado é sempre um estado "confirmado", ou seja, ele nunca é um estado intermediário temporário assumido no meio da execução de uma operação. No entanto, é possível que esse Estado seja obsoleto em comparação com o estado na memória da entidade. Somente orquestrações podem ler o estado na memória da entidade, conforme descrito na seção a seguir.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemplo: os sinais de orquestração e chamadas de uma entidade

As funções de orquestrador podem acessar entidades usando APIs na [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). O código de exemplo a seguir mostra uma função de orquestrador chamando e sinalizando uma entidade `Counter`.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Somente orquestrações são capazes de chamar entidades e obter uma resposta, que pode ser um valor de retorno ou uma exceção. As funções de cliente que usam a [Associação de cliente](durable-functions-bindings.md#entity-client) só podem sinalizar entidades.

> [!NOTE]
> Chamar uma entidade de uma função de orquestrador é semelhante a chamar uma [função de atividade](durable-functions-types-features-overview.md#activity-functions) de uma função de orquestrador. A principal diferença é que as funções de entidade são objetos duráveis com um endereço, que é a ID da entidade. As funções de entidade dão suporte à especificação de um nome de operação. As funções de atividade, por outro lado, são sem estado e não têm o conceito de operações.

### <a name="example-entity-signals-an-entity"></a>Exemplo: a entidade sinaliza uma entidade

Uma função de entidade pode enviar sinais para outras entidades, ou até mesmo, enquanto executa uma operação.
Por exemplo, podemos modificar o exemplo de entidade de `Counter` anterior para que ele envie um sinal de "marco atingido" para alguma entidade de monitor quando o contador atingir o valor 100.

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Coordenação de entidades

Pode haver ocasiões em que você precisa coordenar operações em várias entidades. Por exemplo, em um aplicativo bancário, você pode ter entidades que representem contas bancárias individuais. Ao transferir fundos de uma conta para outra, você deve garantir que a conta de origem tenha fundos suficientes. Você também deve garantir que as atualizações para as contas de origem e de destino sejam feitas de forma transacionalmente consistente.

### <a name="example-transfer-funds-c"></a>Exemplo: transferir fundos (C#)

O código de exemplo a seguir transfere fundos entre duas entidades de conta usando uma função de orquestrador. A coordenação de atualizações de entidade requer o uso do método `LockAsync` para criar uma _seção crítica_ na orquestração.

> [!NOTE]
> Para simplificar, este exemplo reutiliza a entidade `Counter` definida anteriormente. Em um aplicativo real, seria melhor definir uma entidade de `BankAccount` mais detalhada.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

No .NET, `LockAsync` retorna `IDisposable`, que termina a seção crítica quando descartada. Esse `IDisposable` resultado pode ser usado junto com um bloco de `using` para obter uma representação sintática da seção crítica.

No exemplo anterior, uma função de orquestrador transferiu fundos de uma entidade de origem para uma entidade de destino. O método `LockAsync` bloqueou as entidades de conta de origem e de destino. Esse bloqueio assegurau que nenhum outro cliente possa consultar ou modificar o estado de qualquer uma das contas até que a lógica de orquestração tenha saído da seção crítica no final da instrução de `using`. Esse comportamento impede a possibilidade de superrascunhar da conta de origem.

> [!NOTE] 
> Quando uma orquestração termina, normalmente ou com um erro, todas as seções críticas em andamento são encerradas implicitamente e todos os bloqueios são liberados.

### <a name="critical-section-behavior"></a>Comportamento da seção crítica

O método `LockAsync` cria uma seção crítica em uma orquestração. Essas seções críticas impedem que outras orquestrações façam alterações sobrepostas em um conjunto especificado de entidades. Internamente, a API de `LockAsync` envia operações de "bloqueio" para as entidades e retorna quando recebe uma mensagem de resposta de "bloqueio adquirido" de cada uma dessas mesmas entidades. Tanto o bloqueio quanto o desbloqueio são operações internas com suporte de todas as entidades.

Nenhuma operação de outros clientes é permitida em uma entidade enquanto ela está em um estado bloqueado. Esse comportamento garante que apenas uma instância de orquestração possa bloquear uma entidade por vez. Se um chamador tentar invocar uma operação em uma entidade enquanto ela estiver bloqueada por uma orquestração, essa operação será colocada em uma fila de operação pendente. Nenhuma operação pendente é processada até que a orquestração em retenção libere seu bloqueio.

> [!NOTE] 
> Esse comportamento é ligeiramente diferente dos primitivos de sincronização usados na maioria das linguagens de programação, como a C#instrução `lock` no. Por exemplo, no C#, a instrução `lock` deve ser usada por todos os threads para garantir a sincronização adequada entre vários threads. No entanto, as entidades não exigem que todos os chamadores bloqueiem uma entidade explicitamente. Se qualquer chamador bloquear uma entidade, todas as outras operações nessa entidade serão bloqueadas e enfileiradas por trás desse bloqueio.

Os bloqueios em entidades são duráveis e, portanto, persistem mesmo que o processo em execução seja reciclado. Os bloqueios são persistidos internamente como parte do estado durável de uma entidade.

Diferentemente das transações, as seções críticas não revertem automaticamente as alterações no caso de erros. Em vez disso, qualquer tratamento de erros, como reversão ou repetição, deve ser codificado explicitamente, por exemplo, capturando erros ou exceções. Essa opção de design é intencional. A reversão automática de todos os efeitos de uma orquestração é difícil ou impossível em geral, pois as orquestrações podem executar atividades e fazer chamadas para serviços externos que não podem ser revertidos. Além disso, as tentativas de reverter podem falhar e exigir tratamento de erro adicional.

### <a name="critical-section-rules"></a>Regras de seção crítica

Ao contrário dos primitivos de bloqueio de baixo nível na maioria das linguagens de programação, as seções críticas têm *garantia de não deadlock*. Para evitar deadlocks, aplicamos as seguintes restrições: 

* Seções críticas não podem ser aninhadas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente as entidades que foram bloqueadas.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas podem sinalizar somente as entidades que não foram bloqueadas.

Quaisquer violações dessas regras causam um erro de tempo de execução, como `LockingRulesViolationException` no .NET, que inclui uma mensagem que explica qual regra foi quebrada.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitos dos recursos de entidades duráveis são inspirados pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com os atores, poderá reconhecer muitos dos conceitos descritos neste artigo. As entidades duráveis são particularmente semelhantes aos [atores virtuais](https://research.microsoft.com/projects/orleans/), ou granulares, conforme popular pelo [projeto Orleans](http://dotnet.github.io/orleans/). Por exemplo:

* As entidades duráveis são endereçáveis por meio de uma ID de entidade.
* As operações de entidade durável são executadas em série, uma de cada vez, para evitar condições de corrida.
* As entidades duráveis são criadas implicitamente quando são chamadas ou sinalizadas.
* Quando não estiver executando operações, as entidades duráveis serão descarregadas silenciosamente da memória.

Há algumas diferenças importantes que valem a pena observar:

* As entidades duráveis priorizam a durabilidade em relação à latência e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* Entidades duráveis não têm tempos limite internos para mensagens. No Orleans, todas as mensagens expiram após uma hora configurável. O padrão é 30 segundos.
* As mensagens enviadas entre as entidades são entregues de forma confiável e em ordem. No Orleans, a entrega confiável ou ordenada tem suporte para conteúdo enviado por meio de fluxos, mas não é garantida para todas as mensagens entre as granulações.
* Os padrões de solicitação-resposta em entidades são limitados a orquestrações. De dentro de entidades, somente as mensagens unidirecionais (também conhecidas como sinalização) são permitidas, como no modelo de ator original e, ao contrário de granulares em Orleans. 
* As entidades duráveis não travam. No Orleans, os deadlocks podem ocorrer e não resolver até que o tempo limite das mensagens expire.
* Entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e oferecem suporte a mecanismos de bloqueio distribuídos. 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Leia o guia do desenvolvedor para entidades duráveis no .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre os hubs de tarefas](durable-functions-task-hubs.md)
