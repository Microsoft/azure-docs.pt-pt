---
title: Entidades duráveis-Azure Functions
description: Saiba o que são entidades duráveis e como usá-las na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 864a641968268c439c65996998cbb822746b96f9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838991"
---
# <a name="entity-functions-preview"></a>Funções de entidade (visualização)

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

> [!NOTE]
> Funções de entidade e funcionalidade relacionada só estão disponíveis no Durable Functions 2,0 e superior. As funções de entidade estão atualmente em visualização pública.

## <a name="entity-identity"></a>Identidade da entidade

As entidades (às vezes chamadas de *instâncias*de entidade) são acessadas por meio de um identificador exclusivo, a *ID da entidade*. Uma ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

* Um **nome de entidade**: um nome que identifica o tipo da entidade (por exemplo, "Counter").
* Uma **chave de entidade**: uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome (por exemplo, um GUID).

Por exemplo, uma função de entidade de *contador* pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusiva, `@Counter@Game1`como, `@Counter@Game2`e assim por diante. Todas as operações direcionadas a uma entidade específica exigem a especificação de uma ID de entidade como um parâmetro.

## <a name="programming-models"></a>Modelos de programação

As entidades duráveis dão suporte a dois modelos de programação diferentes. O primeiro modelo é um modelo "funcional" dinâmico em que a entidade é definida por uma única função. O segundo modelo é um modelo orientado a objeto em que a entidade é definida por uma classe e métodos. Esses modelos e os modelos de programação para interagir com entidades são descritos nas próximas seções.

### <a name="defining-entities"></a>Definindo entidades

Há dois modelos de programação opcionais para a criação de entidades duráveis. O código a seguir é um exemplo de uma entidade de *contador* simples implementada como uma função padrão. Essa função define três *operações*, `add` `reset`, e `get`, cada uma operando em um valor de estado inteiro, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Esse modelo funciona melhor para implementações de entidade simples ou implementações que têm um conjunto dinâmico de operações. No entanto, você também pode usar um modelo de programação baseado em classe que é útil para entidades que são estáticas, mas têm implementações mais complexas. O exemplo a seguir é uma implementação equivalente da `Counter` entidade usando classes e métodos.

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

> [!NOTE]
> O método de ponto de entrada de `[FunctionName]` função com o atributo `static` *deve* ser declarado ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

No modelo de programação baseado em classe, o `IDurableEntityContext` objeto está disponível `Entity.Current` na propriedade estática.

O modelo baseado em classe é semelhante ao modelo de programação popular pelo [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Nesse modelo, um tipo de entidade é definido como uma classe .NET. Cada método da classe é uma operação que pode ser invocada por um cliente externo. Ao contrário de Orleans, no entanto, as interfaces .NET são opcionais. O exemplo de *contador* anterior não usou uma interface, mas ela ainda pode ser invocada por meio de outras funções ou por meio de chamadas de API http.

> [!NOTE]
> As funções de gatilho de entidade estão disponíveis no Durable Functions 2,0 e acima. Atualmente, as funções de gatilho de entidade estão disponíveis somente para aplicativos de funções do .NET.

### <a name="accessing-entities-from-clients"></a>Acessando entidades de clientes

Entidades duráveis podem ser invocadas ou consultadas a partir de funções comuns – também conhecidas como *funções de cliente* , usando a associação de saída do [cliente de entidade](durable-functions-bindings.md#entity-client). O exemplo a seguir mostra uma função disparada por fila *sinalizando* uma entidade usando essa associação.

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

> [!NOTE]
> As funções do .NET oferecem suporte a métodos sem rigidez de tipos e fortemente tipados para entidades de sinalização. Consulte a documentação de referência de [Associação de cliente de entidade](durable-functions-bindings.md#entity-client-usage) para obter detalhes.

O *sinal* de termo significa que a invocação de API de entidade é unidirecional e assíncrona. Não é possível que uma *função de cliente* saiba quando a entidade processou a operação, nem é possível que uma função de entidade retorne um valor a uma função de cliente. O sistema de mensagens unidirecional baseado em fila era uma opção de design intencional para entidades duráveis para priorizar a durabilidade do desempenho. Essa opção de design é uma das compensações de entidades duráveis em comparação com outras tecnologias semelhantes. No momento, apenas orquestrações são capazes de lidar com valores de retorno de entidades, conforme descrito na próxima seção.

As funções de cliente também podem consultar o estado das entidades, conforme mostrado no exemplo a seguir:

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

As consultas de estado de entidade são enviadas para o armazenamento de controle durável e retornam o estado *persistido* mais recentemente da entidade. É possível que o estado retornado seja obsoleto em comparação com o estado na memória da entidade. Somente orquestrações podem ler o estado na memória da entidade, conforme descrito na seção a seguir.

### <a name="accessing-entities-from-orchestrations"></a>Acessando entidades de orquestrações

As funções de orquestrador podem acessar entidades usando APIs na [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). As funções de orquestrador podem escolher entre comunicação unidirecional (acionar e esquecer, também conhecida como *sinalização*) e comunicação bidirecional (solicitação e resposta, também conhecida como *chamada*). O código de exemplo a seguir mostra uma função de orquestrador *chamando* e *sinalizando* uma entidade de *contador* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value - will await a response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value - will not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Somente orquestrações são capazes de chamar entidades e obter uma resposta, que pode ser um valor de retorno ou uma exceção. As funções de cliente que usam a [Associação de cliente](durable-functions-bindings.md#entity-client) só podem *sinalizar* entidades.

> [!NOTE]
> Chamar uma entidade de uma função orchestrtor é semelhante a chamar uma [função de atividade](durable-functions-types-features-overview.md#activity-functions) de uma função de orquestrador. A principal diferença é que as funções de entidade são objetos duráveis com um endereço (a *ID da entidade*) e dão suporte à especificação de um nome de operação. As funções de atividade, por outro lado, são sem estado e não têm o conceito de operações.

### <a name="dependency-injection-in-entity-classes-net"></a>Injeção de dependência em classes de entidade (.NET)

As classes de entidade dão suporte à [injeção de dependência Azure Functions](../functions-dotnet-dependency-injection.md). O exemplo a seguir demonstra como registrar um `IHttpClientFactory` serviço em uma entidade baseada em classe.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

O trecho a seguir demonstra como incorporar o serviço injetado à sua classe de entidade.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Ao contrário de quando usar a injeção de Construtor no .NET Azure Functions regular, o método de ponto de entrada de funções para `static`entidades baseadas em classe deve ser declarado. Declarar um ponto de entrada de função não estática pode causar conflitos entre o inicializador de objeto de Azure Functions normal e o inicializador de objeto de entidades duráveis.

### <a name="bindings-in-entity-classes-net"></a>Associações em classes de entidade (.NET)

Diferentemente das funções regulares, os métodos de classe de entidade não têm acesso direto a associações de entrada e saída. Em vez disso, os dados de associação devem ser capturados na declaração da função de ponto de `DispatchAsync<T>` entrada e passados para o método. Todos os objetos passados `DispatchAsync<T>` para serão passados automaticamente para o construtor da classe de entidade como um argumento.

O exemplo a seguir mostra como `CloudBlobContainer` uma referência da [Associação de entrada de blob](../functions-bindings-storage-blob.md#input) pode ser disponibilizada para uma entidade baseada em classe.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Para obter mais informações sobre associações no Azure Functions, consulte a documentação [Azure Functions gatilhos e associações](../functions-triggers-bindings.md) .

## <a name="entity-coordination"></a>Coordenação de entidades

Pode haver ocasiões em que você precisa coordenar operações em várias entidades. Por exemplo, em um aplicativo bancário, você pode ter entidades representando contas bancárias individuais. Ao transferir fundos de uma conta para outra, você deve garantir que a conta de _origem_ tenha fundos suficientes e que as atualizações para as contas de _origem_ e de _destino_ sejam feitas de forma transacionalmente consistente.

### <a name="transfer-funds-example-in-c"></a>Exemplo de transferência de fundos emC#

O código de exemplo a seguir transfere fundos entre duas entidades de _conta_ usando uma função de orquestrador. A coordenação de atualizações de entidade requer `LockAsync` o uso do método para criar uma _seção crítica_ na orquestração:

> [!NOTE]
> Para simplificar, este exemplo reutiliza a `Counter` entidade definida anteriormente. No entanto, em um aplicativo real, seria melhor definir uma entidade mais detalhada `BankAccount` .

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

No .net, `LockAsync` retorna um `IDisposable` que termina a seção crítica quando descartada. Esse `IDisposable` resultado pode ser usado junto com um `using` bloco para obter uma representação sintática da seção crítica.

No exemplo anterior, uma função de orquestrador transferiu fundos de uma entidade de _origem_ para uma entidade de _destino_ . O `LockAsync` método bloqueou as entidades de conta de _origem_ e de _destino_ . Esse bloqueio assegurau que nenhum outro cliente possa consultar ou modificar o estado de qualquer uma das contas até que a lógica de orquestração tenha saído da _seção crítica_ no `using` final da instrução. Isso efetivamente evitou a possibilidade de superrascunhar da conta de _origem_ .

### <a name="critical-section-behavior"></a>Comportamento da seção crítica

O `LockAsync` método cria uma _seção crítica_ em uma orquestração. Essas _seções críticas_ impedem que outras orquestrações façam alterações sobrepostas em um conjunto especificado de entidades. Internamente, a `LockAsync` API envia operações de "bloqueio" para as entidades e retorna quando recebe uma mensagem de resposta de "bloqueio adquirido" de cada uma dessas mesmas entidades. Tanto o *bloqueio* quanto o *desbloqueio* são operações internas com suporte de todas as entidades.

Nenhuma operação de outros clientes é permitida em uma entidade enquanto ela está em um estado bloqueado. Esse comportamento garante que apenas uma instância de orquestração possa bloquear uma entidade por vez. Se um chamador tentar invocar uma operação em uma entidade enquanto ela estiver bloqueada por uma orquestração, essa operação será colocada em uma *fila de operação pendente*. Nenhuma operação pendente será processada até que a orquestração em retenção libere seu bloqueio.

> [!NOTE] 
> Isso é um pouco diferente dos primitivos de sincronização usados na maioria das linguagens de `lock` programação, C#como a instrução em. Por exemplo, em C#, a `lock` instrução deve ser usada por todos os threads para garantir o sincronização apropriado em vários threads. No entanto, as entidades não exigem que todos os chamadores _bloqueiem_ uma entidade explicitamente. Se qualquer chamador bloquear uma entidade, todas as outras operações nessa entidade serão bloqueadas e enfileiradas por trás desse bloqueio.

Os bloqueios em entidades são duráveis, de modo que persistirão mesmo se o processo de execução for reciclado. Os bloqueios são persistidos internamente como parte do estado durável de uma entidade.

### <a name="critical-section-restrictions"></a>Restrições de seção crítica

Impõem várias restrições sobre como seções críticas podem ser usadas. Essas restrições servem para evitar deadlocks e reentrância.

* Não é possível aninhar seções críticas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente as entidades que foram bloqueadas.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas só podem sinalizar entidades que não tenham sido bloqueadas.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitos dos recursos de entidades duráveis são inspirados pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com os atores, poderá reconhecer muitos dos conceitos descritos neste artigo. Em particular, as entidades duráveis são semelhantes aos [atores virtuais](https://research.microsoft.com/projects/orleans/) de várias maneiras:

* As entidades duráveis são endereçáveis por meio de uma *ID de entidade*.
* As operações de entidade durável são executadas em série, uma de cada vez, para evitar condições de corrida.
* As entidades duráveis são criadas automaticamente quando são chamadas ou sinalizadas.
* Quando não estiver executando operações, as entidades duráveis serão descarregadas silenciosamente da memória.

No entanto, há algumas diferenças importantes que valem a pena observar:

* As entidades duráveis priorizam a *durabilidade* em relação à *latência*e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* As mensagens enviadas entre as entidades são entregues de forma confiável e em ordem.
* Entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e oferecem suporte a mecanismos de bloqueio distribuídos.
* Os padrões de solicitação/resposta em entidades são limitados a orquestrações. Para a comunicação de *cliente para entidade* e *entidade para entidade* , somente mensagens unidirecionais (também conhecidas como "sinalização") são permitidas, como no modelo de ator original. Esse comportamento impede deadlocks distribuídos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os hubs de tarefas](durable-functions-task-hubs.md)
