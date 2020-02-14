---
title: Guia do desenvolvedor para entidades duráveis no .NET-Azure Functions
description: Como trabalhar com entidades duráveis no .NET com a extensão Durable Functions para Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198501"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guia do desenvolvedor para entidades duráveis no .NET

Neste artigo, descrevemos as interfaces disponíveis para desenvolver entidades duráveis com o .NET em detalhes, incluindo exemplos e conselhos gerais. 

As funções de entidade fornecem aos desenvolvedores de aplicativos sem servidor uma maneira conveniente de organizar o estado do aplicativo como uma coleção de entidades refinadas. Para mais detalhes sobre os conceitos subjacentes, consulte o artigo [Entidades Duráveis: Conceitos.](durable-functions-entities.md)

Atualmente, oferecemos duas APIs para definir entidades:

- A **sintaxe baseada na classe** representa entidades e operações como classes e métodos. Essa sintaxe produz um código facilmente legível e permite que as operações sejam invocadas de forma com uma verificação de tipo por meio de interfaces. 

- A **sintaxe baseada na função** é uma interface de nível inferior que representa as entidades como funções. Ele fornece controle preciso sobre como as operações de entidade são expedidas e como o estado da entidade é gerenciado.  

Este artigo se concentra principalmente na sintaxe baseada em classe, pois esperamos que seja mais adequado para a maioria dos aplicativos. No entanto, a [sintaxe baseada na função](#function-based-syntax) pode ser adequada para aplicações que desejem definir ou gerir as suas próprias abstrações para estado e operações de entidades. Além disso, pode ser apropriado para implementar bibliotecas que exigem a genericidade que não tem suporte atualmente pela sintaxe baseada em classe. 

> [!NOTE]
> A sintaxe baseada em classe é apenas uma camada sobre a sintaxe baseada em função; portanto, ambas as variantes podem ser usadas de maneira intercambiável no mesmo aplicativo. 
 
## <a name="defining-entity-classes"></a>Definindo classes de entidade

O exemplo seguinte é a implementação de uma entidade `Counter` que armazena um único valor de tipo inteiro, e oferece quatro operações `Add`, `Reset`, `Get`e `Delete`.

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

A função `Run` contém a placa de caldeira necessária para a utilização da sintaxe baseada na classe. Deve ser uma função *azure estática.* Ele é executado uma vez para cada mensagem de operação processada pela entidade. Quando `DispatchAsync<T>` é chamado e a entidade já não está na memória, constrói um objeto de tipo `T` e povoa os seus campos a partir do último JSON persistido encontrado no armazenamento (se houver). Em seguida, ele invoca o método com o nome correspondente.

> [!NOTE]
> O estado de uma entidade baseada na classe é **criado implicitamente** antes de a entidade processar uma operação, e pode ser **eliminado explicitamente** numa operação, chamando `Entity.Current.DeleteState()`.

### <a name="class-requirements"></a>Requisitos de classe
 
As classes de entidade são POCOs (objetos antigos do CLR) que não exigem superclasses, interfaces ou atributos especiais. Todavia:

- A classe deve ser construível (ver [construção da Entidade).](#entity-construction)
- A classe deve ser jSON-serializável (ver [Entity serialization](#entity-serialization)).

Além disso, qualquer método destinado a ser invocado como uma operação deve atender a requisitos adicionais:

- Uma operação deve ter no máximo um argumento e não deve ter sobrecargas ou argumentos de tipo genérico.
- Uma operação destinada a ser chamada de uma orquestração utilizando uma interface deve devolver `Task` ou `Task<T>`.
- Os argumentos e os valores de retorno devem ser valores serializáveis ou objetos.

### <a name="what-can-operations-do"></a>O que as operações podem fazer?

Todas as operações de entidade podem ler e atualizar o estado da entidade e as alterações no estado são persistidas automaticamente no armazenamento. Além disso, as operações podem executar e/s externa ou outros cálculos, dentro dos limites gerais comuns a todos os Azure Functions.

As operações têm também acesso à funcionalidade fornecida pelo contexto `Entity.Current`:

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a identificação da entidade atualmente executante (inclui nome e chave).
* `SignalEntity`: envia uma mensagem de ida a uma entidade.
* `CreateNewOrchestration`: inicia uma nova orquestração.
* `DeleteState`: elimina o estado desta entidade.

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

As entidades baseadas em classe podem ser acessadas diretamente, usando nomes de cadeia de caracteres explícitos para a entidade e suas operações. Fornecemos alguns exemplos abaixo; para uma explicação mais profunda dos conceitos subjacentes (tais como sinais vs. chamadas) consulte a discussão nas [entidades](durable-functions-entities.md#access-entities)access . 

> [!NOTE]
> Sempre que possível, recomendamos o acesso a entidades através de [interfaces,](#accessing-entities-through-interfaces)porque fornece mais verificação de tipo.

### <a name="example-client-signals-entity"></a>Exemplo: entidade de sinais de cliente

A seguinte função http do Azure implementa uma operação de exclusão usando convenções REST. Ele envia um sinal de exclusão para a entidade do contador cuja chave é passada no caminho da URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
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
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> O objeto devolvido por `ReadEntityStateAsync` é apenas uma cópia local, isto é, uma foto do estado da entidade de algum ponto anterior no tempo. Em particular, ele pode estar obsoleto e a modificação desse objeto não tem efeito sobre a entidade real. 

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

As classes de entidades e as interfaces de entidades são semelhantes às interfaces de grãos e cereais popularizadas por [Orleans.](https://www.microsoft.com/research/project/orleans-virtual-actors/) Para obter mais informações sobre semelhanças e diferenças entre Entidades Duráveis e Orleães, consulte [A Comparação com os atores virtuais.](durable-functions-entities.md#comparison-with-virtual-actors)

Além de fornecer verificação de tipo, as interfaces são úteis para uma separação melhor das preocupações dentro do aplicativo. Por exemplo, como uma entidade pode implementar várias interfaces, uma única entidade pode atender a várias funções. Além disso, como uma interface pode ser implementada por várias entidades, os padrões de comunicação gerais podem ser implementados como bibliotecas reutilizáveis.

### <a name="example-client-signals-entity-through-interface"></a>Exemplo: o cliente sinaliza a entidade por meio da interface

O código do cliente pode usar `SignalEntityAsync<TEntityInterface>` para enviar sinais a entidades que implementam `TEntityInterface`. Por exemplo:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

Neste exemplo, o parâmetro `proxy` é um caso de `ICounter`gerado dinamicamente, que traduz internamente a chamada para `Delete` num sinal.

> [!NOTE]
> As APIs `SignalEntityAsync` só podem ser utilizadas para operações de ida e só. Mesmo que uma operação der volta `Task<T>`, o valor do parâmetro `T` será sempre nulo ou `default`, e não o resultado real.
Por exemplo, não faz sentido sinalizar a operação `Get`, uma vez que não é devolvido qualquer valor. Em vez disso, os clientes podem usar `ReadStateAsync` para aceder diretamente ao estado de contraestado, ou podem iniciar uma função orquestradora que chama a operação `Get`. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exemplo: primeiro os sinais de orquestração e, em seguida, chama a entidade por meio do proxy

Para ligar ou sinalizar uma entidade de dentro de uma orquestração, `CreateEntityProxy` pode ser usado, juntamente com o tipo de interface, para gerar um proxy para a entidade. Esse proxy pode então ser usado para chamar ou sinalizar operações:

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

Implicitamente, todas as operações que devolvam `void` são sinalizadas, e quaisquer operações que devolvam `Task` ou `Task<T>` são chamadas. Pode-se alterar este comportamento predefinido e as operações de sinalização mesmo que devolvam a Tarefa, utilizando o método `SignalEntity<IInterfaceType>` explicitamente.

### <a name="shorter-option-for-specifying-the-target"></a>Opção mais curta para especificar o destino

Ao chamar ou sinalizar uma entidade usando uma interface, o primeiro argumento deve especificar a entidade de destino. O destino pode ser especificado especificando a ID da entidade ou, nos casos em que há apenas uma classe que implementa a entidade, apenas a chave de entidade:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se apenas a chave da entidade for especificada e não for encontrada uma implementação única no tempo de execução, `InvalidOperationException` é lançado. 

### <a name="restrictions-on-entity-interfaces"></a>Restrições em interfaces de entidade

Como de costume, todos os tipos de parâmetro e de retorno devem ser serializáveis em JSON. Caso contrário, as exceções de serialização são lançadas em tempo de execução.

Também impõem algumas regras adicionais:
* As interfaces de entidade devem definir apenas métodos.
* As interfaces de entidade não devem conter parâmetros genéricos.
* Os métodos de interface de entidade não devem ter mais de um parâmetro.
* Os métodos de interface da entidade devem devolver `void`, `Task`ou `Task<T>` 

Se alguma destas regras for violada, um `InvalidOperationException` é lançado em tempo de execução quando a interface é usada como um argumento tipo para `SignalEntity` ou `CreateProxy`. A mensagem de exceção explica qual regra foi quebrada.

> [!NOTE]
> Os métodos de interface que regressam `void` só podem ser sinalizados (de ida), não chamados (bidirecionais). Os métodos de interface que retornam `Task` ou `Task<T>` podem ser chamados ou sinalizados. Se chamado, eles retornam o resultado da operação ou geram novamente exceções lançadas pela operação. No entanto, quando sinalizado, eles não retornam o resultado real ou a exceção da operação, mas apenas o valor padrão.

## <a name="entity-serialization"></a>Serialização de entidade

Como o estado de uma entidade é permanentemente persistido, a classe de entidade deve ser serializável. O tempo de funcionamento das Funções Duráveis utiliza a biblioteca [Json.NET](https://www.newtonsoft.com/json) para este fim, que suporta uma série de políticas e atributos para controlar o processo de serialização e desserialização. Os tipos de C# dados usados com mais frequência (incluindo matrizes e tipos de coleção) já são serializáveis e podem ser facilmente usados para definir o estado de entidades duráveis.

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
- Anotamos a classe com `[JsonObject(MemberSerialization.OptIn)]` para nos lembrar que a classe deve ser ilizável, e persistir apenas membros que são explicitamente marcados como propriedades JSON.
-  Anotamos os campos a persistir com `[JsonProperty("name")]` para nos lembrar que um campo faz parte do estado de entidade persistida, e especificar o nome de propriedade a ser usado na representação json.

No entanto, esses atributos não são obrigatórios; outras convenções ou atributos são permitidos contanto que funcionem com Json.NET. Por exemplo, pode-se usar `[DataContract]` atributos, ou nenhum atributo:

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

Por predefinição, o nome da classe *não* é armazenado como parte da representação da JSON: ou seja, usamos `TypeNameHandling.None` como definição padrão. Este comportamento predefinido pode ser ultrapassado usando `JsonObject` ou `JsonProperty` atributos.

### <a name="making-changes-to-class-definitions"></a>Fazendo alterações nas definições de classe

É necessário algum cuidado ao fazer alterações em uma definição de classe após a execução de um aplicativo, pois o objeto JSON armazenado pode não corresponder mais à nova definição de classe. Ainda assim, é frequentemente possível lidar corretamente com a alteração dos formatos de dados, desde que se compreenda o processo de desserialização utilizado por `JsonConvert.PopulateObject`.

Por exemplo, aqui estão alguns exemplos de alterações e seus efeitos:

1. Se uma nova propriedade for adicionada, que não está presente no JSON armazenado, ela assumirá seu valor padrão.
1. Se uma propriedade for removida, que está presente no JSON armazenado, o conteúdo anterior será perdido.
1. Se uma propriedade for renomeada, o efeito será como se estiver removendo a antiga e adicionando uma nova.
1. Se o tipo de uma propriedade for alterado para que ela não possa mais ser desserializada do JSON armazenado, uma exceção será lançada.
1. Se o tipo de uma propriedade for alterado, mas ainda puder ser desserializado do JSON armazenado, isso será feito.

Há muitas opções disponíveis para personalizar o comportamento de Json.NET. Por exemplo, para forçar uma exceção se o JSON armazenado contiver um campo que não esteja presente na classe, especifique o atributo `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. Também é possível escrever código personalizado para desserialização que possa ler JSON armazenado em formatos arbitrários.

## <a name="entity-construction"></a>Construção de entidade

Às vezes, queremos exercer mais controle sobre como os objetos de entidade são construídos. Agora, descrevemos várias opções para alterar o comportamento padrão ao construir objetos de entidade. 

### <a name="custom-initialization-on-first-access"></a>Inicialização personalizada no primeiro acesso

Ocasionalmente, precisamos executar alguma inicialização especial antes de distribuir uma operação para uma entidade que nunca foi acessada ou que foi excluída. Para especificar este comportamento, pode-se adicionar um condicional antes do `DispatchAsync`:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Associações em classes de entidade

Diferentemente das funções regulares, os métodos de classe de entidade não têm acesso direto a associações de entrada e saída. Em vez disso, os dados vinculativos devem ser capturados na declaração de função do ponto de entrada e depois passados ao método `DispatchAsync<T>`. Quaisquer objetos passados para `DispatchAsync<T>` serão automaticamente passados para o construtor de classe entidade como argumento.

O exemplo seguinte mostra como uma referência `CloudBlobContainer` da ligação de [entrada blob](../functions-bindings-storage-blob-input.md) pode ser disponibilizada a uma entidade baseada em classe.

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

Para obter mais informações sobre encadernações em Funções Azure, consulte a documentação de [Gatilhos e Encadernações de Funções Azure.](../functions-triggers-bindings.md)

### <a name="dependency-injection-in-entity-classes"></a>Injeção de dependência em classes de entidade

As classes de entidades suportam a Injeção de Dependência de [Funções Azure.](../functions-dotnet-dependency-injection.md) O exemplo que se segue demonstra como registar um serviço `IHttpClientFactory` numa entidade baseada em classes.

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

> [!NOTE]
> Ao contrário do que acontece quando se utiliza a injeção de construtores em funções regulares .NET Azure, o método do ponto de entrada das funções para as entidades de base de classe *deve* ser declarado `static`. Declarar um ponto de entrada de função não estática pode causar conflitos entre o inicializador de objeto de Azure Functions normal e o inicializador de objeto de entidades duráveis.

## <a name="function-based-syntax"></a>Sintaxe baseada em função

Até agora, nos concentramos na sintaxe baseada em classe, pois esperamos que seja mais adequado para a maioria dos aplicativos. No entanto, a sintaxe baseada em função pode ser apropriada para aplicativos que desejam definir ou gerenciar suas próprias abstrações para estado e operações de entidade. Além disso, pode ser apropriado ao implementar bibliotecas que exigem a genericidade sem suporte atualmente pela sintaxe baseada em classe. 

Com a sintaxe baseada em função, a função de entidade manipula explicitamente a expedição da operação e gerencia explicitamente o estado da entidade. Por exemplo, o código seguinte mostra a entidade *Contador* implementada utilizando a sintaxe baseada na função.  

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

A funcionalidade específica da entidade pode ser acedida através de um objeto de contexto de tipo `IDurableEntityContext`. Este objeto de contexto está disponível como parâmetro para a função da entidade, e através da propriedade async-local `Entity.Current`.

Os membros a seguir fornecem informações sobre a operação atual e nos permitem especificar um valor de retorno. 

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a identificação da entidade atualmente executante (inclui nome e chave).
* `OperationName`: o nome da operação atual.
* `GetInput<TInput>()`: obtém a entrada para a operação em curso.
* `Return(arg)`: devolve um valor à orquestração que chamou de operação.

Os membros a seguir gerenciam o estado da entidade (criar, ler, atualizar, excluir). 

* `HasState`: se a entidade existe, ou seja, tem algum Estado. 
* `GetState<TState>()`: obtém o estado atual da entidade. Se ele ainda não existir, ele será criado.
* `SetState(arg)`: cria ou atualiza o estado da entidade.
* `DeleteState()`: elimina o estado da entidade, se existir. 

Se o estado devolvido por `GetState` for um objeto, pode ser modificado diretamente pelo código de aplicação. Não há necessidade de voltar a chamar `SetState` no final (mas também não há mal). Se `GetState<TState>` for chamado várias vezes, o mesmo tipo deve ser utilizado.

Por fim, os membros a seguir são usados para sinalizar outras entidades ou iniciar novas orquestrações:

* `SignalEntity(EntityId, operation, input)`: envia uma mensagem de ida a uma entidade.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: inicia uma nova orquestração.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça conceitos de entidades](durable-functions-entities.md)
