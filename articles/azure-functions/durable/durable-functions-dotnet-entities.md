---
title: Guia do desenvolvedor para entidades duráveis no .NET-Azure Functions
description: Como trabalhar com entidades duráveis no .NET com a extensão Durable Functions para Azure Functions.
services: functions
author: sebastianburckhardt
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 5738161e88c42f4d4033fab091d8e8c8d7162042
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301727"
---
# <a name="developers-guide-to-durable-entities-in-net-preview"></a>Guia do desenvolvedor para entidades duráveis no .NET (visualização)

Neste artigo, descrevemos as interfaces disponíveis para desenvolver entidades duráveis com o .NET em detalhes, incluindo exemplos e conselhos gerais. 

As funções de entidade fornecem aos desenvolvedores de aplicativos sem servidor uma maneira conveniente de organizar o estado do aplicativo como uma coleção de entidades refinadas. Para obter mais detalhes sobre os conceitos subjacentes, consulte o artigo [entidades duráveis: conceitos](durable-functions-entities.md) .

Atualmente, oferecemos duas APIs para definir entidades:

- A **sintaxe baseada em classe** representa entidades e operações como classes e métodos. Essa sintaxe produz um código facilmente legível e permite que as operações sejam invocadas de forma com uma verificação de tipo por meio de interfaces. 

- A **sintaxe baseada em função** é uma interface de nível inferior que representa entidades como funções. Ele fornece controle preciso sobre como as operações de entidade são expedidas e como o estado da entidade é gerenciado.  

Este artigo se concentra principalmente na sintaxe baseada em classe, pois esperamos que seja mais adequado para a maioria dos aplicativos. No entanto, a [sintaxe baseada em função](#function-based-syntax) pode ser apropriada para aplicativos que desejam definir ou gerenciar suas próprias abstrações para estado e operações de entidade. Além disso, pode ser apropriado para implementar bibliotecas que exigem a genericidade que não tem suporte atualmente pela sintaxe baseada em classe. 

> [!NOTE]
> A sintaxe baseada em classe é apenas uma camada sobre a sintaxe baseada em função; portanto, ambas as variantes podem ser usadas de maneira intercambiável no mesmo aplicativo. 
 
## <a name="defining-entity-classes"></a>Definindo classes de entidade

O exemplo a seguir é uma implementação de uma entidade `Counter` que armazena um único valor do tipo inteiro e oferece quatro operações `Add`, `Reset`, `Get` e `Delete`.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

A função `Run` contém o texto clichê necessário para usar a sintaxe baseada em classe. Ele deve ser uma função *estática* do Azure. Ele é executado uma vez para cada mensagem de operação processada pela entidade. Quando `DispatchAsync<T>` é chamado e a entidade ainda não está na memória, ela constrói um objeto do tipo `T` e popula seus campos do último JSON persistente encontrado no armazenamento (se houver). Em seguida, ele invoca o método com o nome correspondente.

> [!NOTE]
> O estado de uma entidade baseada em classe é **criado implicitamente** antes que a entidade processe uma operação e pode ser **excluída explicitamente** em uma operação chamando `Entity.Current.DeleteState()`.

### <a name="class-requirements"></a>Requisitos de classe
 
As classes de entidade são POCOs (objetos antigos do CLR) que não exigem superclasses, interfaces ou atributos especiais. Porém

- A classe deve ser constructible (consulte a [construção da entidade](#entity-construction)).
- A classe deve ser serializável em JSON (consulte [serialização de entidade](#entity-serialization)).

Além disso, qualquer método destinado a ser invocado como uma operação deve atender a requisitos adicionais:

- Uma operação deve ter no máximo um argumento e não deve ter sobrecargas ou argumentos de tipo genérico.
- Uma operação destinada a ser chamada de uma orquestração usando uma interface deve retornar `Task` ou `Task<T>`.
- Os argumentos e os valores de retorno devem ser valores serializáveis ou objetos.

### <a name="what-can-operations-do"></a>O que as operações podem fazer?

Todas as operações de entidade podem ler e atualizar o estado da entidade e as alterações no estado são persistidas automaticamente no armazenamento. Além disso, as operações podem executar e/s externa ou outros cálculos, dentro dos limites gerais comuns a todos os Azure Functions.

As operações também têm acesso à funcionalidade fornecida pelo contexto `Entity.Current`:

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a ID da entidade atualmente em execução (inclui nome e chave).
* `SignalEntity`: envia uma mensagem unidirecional para uma entidade.
* `CreateNewOrchestration`: inicia uma nova orquestração.
* `DeleteState`: exclui o estado desta entidade.

Por exemplo, podemos modificar a entidade do contador para que ela inicie uma orquestração quando o contador atingir 100 e passe a ID da entidade como um argumento de entrada:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Acessando entidades diretamente

As entidades baseadas em classe podem ser acessadas diretamente, usando nomes de cadeia de caracteres explícitos para a entidade e suas operações. Fornecemos alguns exemplos abaixo; para obter uma explicação mais detalhada dos conceitos subjacentes (como sinais versus chamadas), consulte a discussão em [acessando entidades](durable-functions-entities.md#accessing-entities). 

> [!NOTE]
> Sempre que possível, é recomendável [acessar entidades por meio de interfaces](), pois ela fornece mais verificação de tipo.

### <a name="example-client-signals-entity"></a>Exemplo: entidade de sinais de cliente

A seguinte função http do Azure implementa uma operação de exclusão usando convenções REST. Ele envia um sinal de exclusão para a entidade do contador cuja chave é passada no caminho da URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Exemplo: o cliente lê o estado da entidade

A seguinte função http do Azure implementa uma operação GET usando convenções REST. Ele lê o estado atual da entidade do contador cuja chave é passada no caminho da URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> O objeto retornado por `ReadEntityStateAsync` é apenas uma cópia local, ou seja, um instantâneo do estado da entidade de algum momento anterior. Em particular, ele pode estar obsoleto e a modificação desse objeto não tem efeito sobre a entidade real. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exemplo: primeiro os sinais de orquestração e, em seguida, chama a entidade

A orquestração a seguir sinaliza uma entidade de contador para incrementar a ti e, em seguida, chama a mesma entidade para ler seu valor mais recente.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Acessando entidades por meio de interfaces

As interfaces podem ser usadas para acessar entidades por meio de objetos de proxy gerados. Essa abordagem garante que o nome e o tipo de argumento de uma operação correspondam ao que é implementado. É recomendável usar interfaces para acessar entidades sempre que possível.

Por exemplo, podemos modificar o exemplo de contador da seguinte maneira:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}
public class Counter : ICounter
{
    ...
}
```

Classes de entidade e interfaces de entidade são semelhantes às interfaces granulares e granulares populares por [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Para obter mais informações sobre semelhanças e diferenças entre entidades duráveis e Orleans, consulte [comparação com atores virtuais](durable-functions-entities.md#comparison-with-virtual-actors).

Além de fornecer verificação de tipo, as interfaces são úteis para uma separação melhor das preocupações dentro do aplicativo. Por exemplo, como uma entidade pode implementar várias interfaces, uma única entidade pode atender a várias funções. Além disso, como uma interface pode ser implementada por várias entidades, os padrões de comunicação gerais podem ser implementados como bibliotecas reutilizáveis.

### <a name="example-client-signals-entity-through-interface"></a>Exemplo: o cliente sinaliza a entidade por meio da interface

O código do cliente pode usar `SignalEntityAsync<TEntityInterface>` para enviar sinais a entidades que implementam `TEntityInterface`. Por exemplo:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

Neste exemplo, o parâmetro `proxy` é uma instância gerada dinamicamente de `ICounter`, que converte internamente a chamada para `Delete` em um sinal.

> [!NOTE]
> As APIs `SignalEntityAsync` podem ser usadas somente para operações unidirecionais. Mesmo que uma operação retorne `Task<T>`, o valor do parâmetro `T` sempre será nulo ou `default`, não o resultado real.
Por exemplo, não faz sentido sinalizar a operação `Get`, uma vez que nenhum valor é retornado. Em vez disso, os clientes podem usar o `ReadStateAsync` para acessar o estado do contador diretamente ou podem iniciar uma função de orquestrador que chama a operação `Get`. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exemplo: primeiro os sinais de orquestração e, em seguida, chama a entidade por meio do proxy

Para chamar ou sinalizar uma entidade de dentro de uma orquestração, `CreateEntityProxy` pode ser usado, juntamente com o tipo de interface, para gerar um proxy para a entidade. Esse proxy pode então ser usado para chamar ou sinalizar operações:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicitamente, todas as operações que retornam `void` são sinalizadas e todas as operações que retornam `Task` ou `Task<T>` são chamadas. É possível alterar esse comportamento padrão e as operações de sinal, mesmo que retornem a tarefa, usando o método `SignalEntity<IInterfaceType>` explicitamente.

### <a name="shorter-option-for-specifying-the-target"></a>Opção mais curta para especificar o destino

Ao chamar ou sinalizar uma entidade usando uma interface, o primeiro argumento deve especificar a entidade de destino. O destino pode ser especificado especificando a ID da entidade ou, nos casos em que há apenas uma classe que implementa a entidade, apenas a chave de entidade:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se apenas a chave de entidade for especificada e uma implementação exclusiva não puder ser encontrada em tempo de execução, `InvalidOperationException` será lançada. 

### <a name="restrictions-on-entity-interfaces"></a>Restrições em interfaces de entidade

Como de costume, todos os tipos de parâmetro e de retorno devem ser serializáveis em JSON. Caso contrário, as exceções de serialização são lançadas em tempo de execução.

Também impõem algumas regras adicionais:
* As interfaces de entidade devem definir apenas métodos.
* As interfaces de entidade não devem conter parâmetros genéricos.
* Os métodos de interface de entidade não devem ter mais de um parâmetro.
* Os métodos de interface de entidade devem retornar `void`, `Task` ou `Task<T>` 

Se qualquer uma dessas regras for violada, um `InvalidOperationException` será lançado em tempo de execução quando a interface for usada como um argumento de tipo para `SignalEntity` ou `CreateProxy`. A mensagem de exceção explica qual regra foi quebrada.

> [!NOTE]
> Métodos de interface que retornam `void` só podem ser sinalizados (unidirecional), não chamados (bidirecional). Métodos de interface que retornam `Task` ou `Task<T>` podem ser chamados ou sinalizados. Se chamado, eles retornam o resultado da operação ou geram novamente exceções lançadas pela operação. No entanto, quando sinalizado, eles não retornam o resultado real ou a exceção da operação, mas apenas o valor padrão.

## <a name="entity-serialization"></a>Serialização de entidade

Como o estado de uma entidade é permanentemente persistido, a classe de entidade deve ser serializável. O tempo de execução de Durable Functions usa a biblioteca [JSON.net](https://www.newtonsoft.com/json) para essa finalidade, que dá suporte a várias políticas e atributos para controlar o processo de serialização e desserialização. Os tipos de C# dados usados com mais frequência (incluindo matrizes e tipos de coleção) já são serializáveis e podem ser facilmente usados para definir o estado de entidades duráveis.

Por exemplo, Json.NET pode facilmente serializar e desserializar a seguinte classe:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Atributos de serialização

No exemplo acima, optamos por incluir vários atributos para tornar a serialização subjacente mais visível:
- Anotamos a classe com `[JsonObject(MemberSerialization.OptIn)]` para nos lembrar que a classe deve ser serializável e manter somente os membros explicitamente marcados como propriedades JSON.
-  Anotamos os campos a serem persistidos com `[JsonProperty("name")]` para nos lembrar que um campo faz parte do estado de entidade persistente e especificar o nome da propriedade a ser usada na representação JSON.

No entanto, esses atributos não são obrigatórios; outras convenções ou atributos são permitidos contanto que funcionem com Json.NET. Por exemplo, um pode usar atributos `[DataContract]` ou nenhum atributo:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Por padrão, o nome da classe *não* é armazenado como parte da representação JSON: ou seja, usamos `TypeNameHandling.None` como a configuração padrão. Esse comportamento padrão pode ser substituído usando atributos `JsonObject` ou `JsonProperty`.

### <a name="making-changes-to-class-definitions"></a>Fazendo alterações nas definições de classe

É necessário algum cuidado ao fazer alterações em uma definição de classe após a execução de um aplicativo, pois o objeto JSON armazenado pode não corresponder mais à nova definição de classe. Ainda assim, geralmente é possível lidar corretamente com a alteração dos formatos de dados, desde que um entenda o processo de desserialização usado pelo `JsonConvert.PopulateObject`.

Por exemplo, aqui estão alguns exemplos de alterações e seus efeitos:

1. Se uma nova propriedade for adicionada, que não está presente no JSON armazenado, ela assumirá seu valor padrão.
1. Se uma propriedade for removida, que está presente no JSON armazenado, o conteúdo anterior será perdido.
1. Se uma propriedade for renomeada, o efeito será como se estiver removendo a antiga e adicionando uma nova.
1. Se o tipo de uma propriedade for alterado para que ela não possa mais ser desserializada do JSON armazenado, uma exceção será lançada.
1. Se o tipo de uma propriedade for alterado, mas ainda puder ser desserializado do JSON armazenado, isso será feito.

Há muitas opções disponíveis para personalizar o comportamento de Json.NET. Por exemplo, para forçar uma exceção se o JSON armazenado contiver um campo que não está presente na classe, especifique o atributo `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. Também é possível escrever código personalizado para desserialização que possa ler JSON armazenado em formatos arbitrários.

## <a name="entity-construction"></a>Construção de entidade

Às vezes, queremos exercer mais controle sobre como os objetos de entidade são construídos. Agora, descrevemos várias opções para alterar o comportamento padrão ao construir objetos de entidade. 

### <a name="custom-initialization-on-first-access"></a>Inicialização personalizada no primeiro acesso

Ocasionalmente, precisamos executar alguma inicialização especial antes de distribuir uma operação para uma entidade que nunca foi acessada ou que foi excluída. Para especificar esse comportamento, é possível adicionar uma condicional antes do `DispatchAsync`:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Associações em classes de entidade

Diferentemente das funções regulares, os métodos de classe de entidade não têm acesso direto a associações de entrada e saída. Em vez disso, os dados de associação devem ser capturados na declaração da função de ponto de entrada e, em seguida, passados para o método `DispatchAsync<T>`. Todos os objetos passados para `DispatchAsync<T>` serão passados automaticamente para o construtor da classe de entidade como um argumento.

O exemplo a seguir mostra como uma referência a `CloudBlobContainer` da [Associação de entrada de blob](../functions-bindings-storage-blob.md#input) pode ser disponibilizada para uma entidade baseada em classe.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
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

### <a name="dependency-injection-in-entity-classes"></a>Injeção de dependência em classes de entidade

As classes de entidade dão suporte à [injeção de dependência Azure Functions](../functions-dotnet-dependency-injection.md). O exemplo a seguir demonstra como registrar um serviço `IHttpClientFactory` em uma entidade baseada em classe.

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
    [JsonIgnore]
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

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Para evitar problemas com a serialização, certifique-se de excluir os campos destinados a armazenar valores injetados da serialização.

## <a name="function-based-syntax"></a>Sintaxe baseada em função

Até agora, nos concentramos na sintaxe baseada em classe, pois esperamos que seja mais adequado para a maioria dos aplicativos. No entanto, a sintaxe baseada em função pode ser apropriada para aplicativos que desejam definir ou gerenciar suas próprias abstrações para estado e operações de entidade. Além disso, pode ser apropriado ao implementar bibliotecas que exigem a genericidade sem suporte atualmente pela sintaxe baseada em classe. 

Com a sintaxe baseada em função, a função de entidade manipula explicitamente a expedição da operação e gerencia explicitamente o estado da entidade. Por exemplo, o código a seguir mostra a entidade de *contador* implementada usando a sintaxe baseada em função.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>O objeto de contexto de entidade

A funcionalidade específica da entidade pode ser acessada por meio de um objeto de contexto do tipo `IDurableEntityContext`. Esse objeto de contexto está disponível como um parâmetro para a função de entidade e por meio da propriedade local assíncrona `Entity.Current`.

Os membros a seguir fornecem informações sobre a operação atual e nos permitem especificar um valor de retorno. 

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a ID da entidade atualmente em execução (inclui nome e chave).
* `OperationName`: o nome da operação atual.
* `GetInput<TInput>()`: Obtém a entrada para a operação atual.
* `Return(arg)`: retorna um valor para a orquestração que chamou a operação.

Os membros a seguir gerenciam o estado da entidade (criar, ler, atualizar, excluir). 

* `HasState`: se a entidade existe, ou seja, tem algum estado. 
* `GetState<TState>()`: Obtém o estado atual da entidade. Se ele ainda não existir, ele será criado.
* `SetState(arg)`: cria ou atualiza o estado da entidade.
* `DeleteState()`: exclui o estado da entidade, se ela existir. 

Se o estado retornado por `GetState` for um objeto, ele poderá ser modificado diretamente pelo código do aplicativo. Não é necessário chamar `SetState` novamente no final (mas também sem danos). Se `GetState<TState>` for chamado várias vezes, o mesmo tipo deverá ser usado.

Por fim, os membros a seguir são usados para sinalizar outras entidades ou iniciar novas orquestrações:

* `SignalEntity(EntityId, operation, input)`: envia uma mensagem unidirecional para uma entidade.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: inicia uma nova orquestração.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os conceitos de entidade](durable-functions-entities.md)
