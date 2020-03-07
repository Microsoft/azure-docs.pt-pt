---
title: Guia do desenvolvedor para entidades duráveis em .NET - Funções Azure
description: Como trabalhar com entidades duráveis em .NET com a extensão funções duráveis para funções Azure.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357890"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guia do desenvolvedor para entidades duráveis em .NET

Neste artigo, descrevemos as interfaces disponíveis para o desenvolvimento de entidades duráveis com .NET em detalhe, incluindo exemplos e conselhos gerais. 

As funções da entidade fornecem aos desenvolvedores de aplicações sem servidor uma forma conveniente de organizar o estado de aplicação como uma coleção de entidades de grãos finos. Para mais detalhes sobre os conceitos subjacentes, consulte o artigo [Entidades Duráveis: Conceitos.](durable-functions-entities.md)

Atualmente oferecemos duas APIs para definir entidades:

- A **sintaxe baseada na classe** representa entidades e operações como classes e métodos. Esta sintaxe produz código facilmente legível e permite que as operações sejam invocadas de forma verificada por tipo através de interfaces. 

- A **sintaxe baseada na função** é uma interface de nível inferior que representa as entidades como funções. Fornece um controlo preciso sobre a forma como as operações da entidade são expedidas e como o estado da entidade é gerido.  

Este artigo centra-se principalmente na sintaxe baseada na classe, pois esperamos que seja mais adequado para a maioria das aplicações. No entanto, a [sintaxe baseada na função](#function-based-syntax) pode ser adequada para aplicações que desejem definir ou gerir as suas próprias abstrações para estado e operações de entidades. Além disso, pode ser adequado para a implementação de bibliotecas que exijam genericidade não atualmente apoiada pela sintaxe baseada na classe. 

> [!NOTE]
> A sintaxe baseada na classe é apenas uma camada em cima da sintaxe baseada na função, por isso ambas as variantes podem ser usadas alternadamente na mesma aplicação. 
 
## <a name="defining-entity-classes"></a>Definição de classes de entidades

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

A função `Run` contém a placa de caldeira necessária para a utilização da sintaxe baseada na classe. Deve ser uma função *azure estática.* Executa uma vez por cada mensagem de operação que é processada pela entidade. Quando `DispatchAsync<T>` é chamado e a entidade já não está na memória, constrói um objeto de tipo `T` e povoa os seus campos a partir do último JSON persistido encontrado no armazenamento (se houver). Em seguida, invoca o método com o nome correspondente.

> [!NOTE]
> O estado de uma entidade baseada na classe é **criado implicitamente** antes de a entidade processar uma operação, e pode ser **eliminado explicitamente** numa operação, chamando `Entity.Current.DeleteState()`.

### <a name="class-requirements"></a>Requisitos de classe
 
As classes de entidades são POCOs (objetos CLR simples e antigos) que não requerem superclasses especiais, interfaces ou atributos. Todavia:

- A classe deve ser construível (ver [construção da Entidade).](#entity-construction)
- A classe deve ser jSON-serializável (ver [Entity serialization](#entity-serialization)).

Além disso, qualquer método que se destine a ser invocado como operação deve satisfazer requisitos adicionais:

- Uma operação deve ter, no máximo, um argumento e não deve ter sobrecargas ou argumentos genéricos.
- Uma operação destinada a ser chamada de uma orquestração utilizando uma interface deve devolver `Task` ou `Task<T>`.
- Os argumentos e os valores de retorno devem ser valores ou objetos serilizáveis.

### <a name="what-can-operations-do"></a>O que as operações podem fazer?

Todas as operações da entidade podem ler e atualizar o estado da entidade, e as alterações ao Estado são automaticamente perspercadas para armazenamento. Além disso, as operações podem realizar i/O externos ou outros cálculos, dentro dos limites gerais comuns a todas as Funções Azure.

As operações têm também acesso à funcionalidade fornecida pelo contexto `Entity.Current`:

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a identificação da entidade atualmente executante (inclui nome e chave).
* `SignalEntity`: envia uma mensagem de ida a uma entidade.
* `CreateNewOrchestration`: inicia uma nova orquestração.
* `DeleteState`: elimina o estado desta entidade.

Por exemplo, podemos modificar a entidade contrária para que inicie uma orquestração quando o contador atinge 100 e passa o ID da entidade como um argumento de entrada:

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

## <a name="accessing-entities-directly"></a>Aceder diretamente a entidades

As entidades baseadas em classe podem ser acedidas diretamente, utilizando nomes de cordas explícitos para a entidade e suas operações. Fornecemos alguns exemplos abaixo; para uma explicação mais profunda dos conceitos subjacentes (tais como sinais vs. chamadas) consulte a discussão nas [entidades](durable-functions-entities.md#access-entities)access . 

> [!NOTE]
> Sempre que possível, recomendamos o acesso a entidades através de [interfaces,](#accessing-entities-through-interfaces)porque fornece mais verificação de tipo.

### <a name="example-client-signals-entity"></a>Exemplo: entidade de sinais de cliente

A seguinte Função Azure Http implementa uma operação DELETE utilizando convenções REST. Envia um sinal de eliminação para a entidade contadora cuja chave é passada no caminho do URL.

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

### <a name="example-client-reads-entity-state"></a>Exemplo: cliente lê estado de entidade

A seguinte Função Azure Http implementa uma operação GET utilizando convenções REST. Lê o estado atual da contraentidade cuja chave é passada no caminho do URL.

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
> O objeto devolvido por `ReadEntityStateAsync` é apenas uma cópia local, isto é, uma foto do estado da entidade de algum ponto anterior no tempo. Em particular, pode ser estagnado, e modificar este objeto não tem qualquer efeito sobre a entidade real. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exemplo: orquestração primeiro sinaliza, em seguida, chama entidade

A orquestração seguinte sinaliza uma contra entidade para incrementá-la, e depois chama a mesma entidade para ler o seu valor mais recente.

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

## <a name="accessing-entities-through-interfaces"></a>Aceder a entidades através de interfaces

As interfaces podem ser usadas para aceder a entidades através de objetos proxy gerados. Esta abordagem garante que o nome e o tipo de argumento de uma operação correspondam ao que é implementado. Recomendamos a utilização de interfaces para aceder a entidades sempre que possível.

Por exemplo, podemos modificar o contraexemplo da seguinte forma:

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

Além de fornecer verificação de tipos, as interfaces são úteis para uma melhor separação de preocupações dentro da aplicação. Por exemplo, uma vez que uma entidade pode implementar múltiplas interfaces, uma única entidade pode servir várias funções. Além disso, uma vez que uma interface pode ser implementada por várias entidades, os padrões gerais de comunicação podem ser implementados como bibliotecas reutilizáveis.

### <a name="example-client-signals-entity-through-interface"></a>Exemplo: entidade de sinais de cliente através da interface

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

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exemplo: orquestração primeiro sinaliza, em seguida, chama entidade através de procuração

Para ligar ou sinalizar uma entidade de dentro de uma orquestração, `CreateEntityProxy` pode ser usado, juntamente com o tipo de interface, para gerar um proxy para a entidade. Este representante pode então ser utilizado para operações de chamada ou de sinalização:

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

### <a name="shorter-option-for-specifying-the-target"></a>Opção mais curta para especificar o alvo

Ao ligar ou sinalizar uma entidade utilizando uma interface, o primeiro argumento deve especificar a entidade-alvo. O alvo pode ser especificado especificando o ID da entidade, ou, nos casos em que há apenas uma classe que implementa a entidade, apenas a chave da entidade:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se apenas a chave da entidade for especificada e não for encontrada uma implementação única no tempo de execução, `InvalidOperationException` é lançado. 

### <a name="restrictions-on-entity-interfaces"></a>Restrições às interfaces de entidades

Como de costume, todos os tipos de parâmetros e de retorno devem ser jSON-serializáveis. Caso contrário, as exceções à serialização são lançadas em tempo de execução.

Também aplicamos algumas regras adicionais:
* As interfaces de entidades só devem definir métodos.
* As interfaces de entidades não devem conter parâmetros genéricos.
* Os métodos de interface da entidade não devem ter mais de um parâmetro.
* Os métodos de interface da entidade devem devolver `void`, `Task`ou `Task<T>` 

Se alguma destas regras for violada, um `InvalidOperationException` é lançado em tempo de execução quando a interface é usada como um argumento tipo para `SignalEntity` ou `CreateProxy`. A mensagem de exceção explica qual a regra que foi quebrada.

> [!NOTE]
> Os métodos de interface que regressam `void` só podem ser sinalizados (de ida), não chamados (bidirecionais). Os métodos de interface que retornam `Task` ou `Task<T>` podem ser chamados ou sinalizados. Se forem chamados, devolvem o resultado da operação ou relançam exceções lançadas pela operação. No entanto, quando sinalizados, não devolvem o resultado real ou a exceção da operação, mas apenas o valor predefinido.

## <a name="entity-serialization"></a>Serialização de entidades

Uma vez que o estado de uma entidade é persistente, a classe da entidade deve ser ser ilegível. O tempo de funcionamento das Funções Duráveis utiliza a biblioteca [Json.NET](https://www.newtonsoft.com/json) para este fim, que suporta uma série de políticas e atributos para controlar o processo de serialização e desserialização. Os tipos C# de dados mais utilizados (incluindo matrizes e tipos de recolha) já são facilmente utilizados para definir o estado das entidades duráveis.

Por exemplo, Json.NET podem facilmente serializar e desserializar a seguinte classe:

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

### <a name="serialization-attributes"></a>Atributos de Serialização

No exemplo acima, escolhemos incluir vários atributos para tornar a serialização subjacente mais visível:
- Anotamos a classe com `[JsonObject(MemberSerialization.OptIn)]` para nos lembrar que a classe deve ser ilizável, e persistir apenas membros que são explicitamente marcados como propriedades JSON.
-  Anotamos os campos a persistir com `[JsonProperty("name")]` para nos lembrar que um campo faz parte do estado de entidade persistida, e especificar o nome de propriedade a ser usado na representação json.

No entanto, estes atributos não são necessários; outras convenções ou atributos são permitidos desde que trabalhem com Json.NET. Por exemplo, pode-se usar `[DataContract]` atributos, ou nenhum atributo:

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

### <a name="making-changes-to-class-definitions"></a>Fazer alterações nas definições de classes

Alguns cuidados são necessários ao fazer alterações à definição de classe após a execução de uma aplicação, porque o objeto JSON armazenado pode deixar de corresponder à nova definição de classe. Ainda assim, é frequentemente possível lidar corretamente com a alteração dos formatos de dados, desde que se compreenda o processo de desserialização utilizado por `JsonConvert.PopulateObject`.

Por exemplo, aqui estão alguns exemplos de alterações e o seu efeito:

1. Se for adicionado um novo imóvel, que não está presente na JSON armazenada, assume o seu valor predefinido.
1. Se uma propriedade for removida, que está presente no JSON armazenado, o conteúdo anterior perde-se.
1. Se uma propriedade for renomeada, o efeito é como se removesse o antigo e adicionasse um novo.
1. Se o tipo de uma propriedade for alterada para que não possa mais ser desserializada a partir do JSON armazenado, uma exceção é lançada.
1. Se o tipo de propriedade for alterado, mas ainda pode ser desserializado a partir do JSON armazenado, fá-lo-á.

Existem muitas opções disponíveis para personalizar o comportamento de Json.NET. Por exemplo, para forçar uma exceção se o JSON armazenado contiver um campo que não esteja presente na classe, especifique o atributo `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. Também é possível escrever código personalizado para desserialização que pode ler JSON armazenado em formatos arbitrários.

## <a name="entity-construction"></a>Construção de entidades

Às vezes queremos exercer mais controlo sobre a forma como os objetos de entidade são construídos. Agora descrevemos várias opções para alterar o comportamento padrão ao construir objetos de entidade. 

### <a name="custom-initialization-on-first-access"></a>Inicialização personalizada no primeiro acesso

Ocasionalmente, precisamos de realizar uma inicialização especial antes de enviar uma operação para uma entidade que nunca foi acedida, ou que tenha sido apagada. Para especificar este comportamento, pode-se adicionar um condicional antes do `DispatchAsync`:

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

### <a name="bindings-in-entity-classes"></a>Encadernações em classes de entidades

Ao contrário das funções regulares, os métodos de classe de entidades não têm acesso direto a encadernações de entrada e saída. Em vez disso, os dados vinculativos devem ser capturados na declaração de função do ponto de entrada e depois passados ao método `DispatchAsync<T>`. Quaisquer objetos passados para `DispatchAsync<T>` serão automaticamente passados para o construtor de classe entidade como argumento.

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

### <a name="dependency-injection-in-entity-classes"></a>Injeção de dependência em classes de entidades

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

O seguinte corte demonstra como incorporar o serviço injetado na sua classe de entidade.

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
> Ao contrário do que acontece quando se utiliza a injeção de construtores em funções regulares .NET Azure, o método do ponto de entrada das funções para as entidades de base de classe *deve* ser declarado `static`. Declarar um ponto de entrada de função não estático pode causar conflitos entre o ininicializador de objetos funções azuis normais e o inicializador de objetos de entidades duráveis.

## <a name="function-based-syntax"></a>Sintaxe baseada em funções

Até agora, concentrámo-nos na sintaxe baseada na classe, pois esperamos que seja mais adequada para a maioria das aplicações. No entanto, a sintaxe baseada na função pode ser adequada para aplicações que desejem definir ou gerir as suas próprias abstrações para estado e operações de entidades. Além disso, pode ser apropriado na implementação de bibliotecas que exijam genéricos que não são atualmente suportados pela sintaxe baseada na classe. 

Com a sintaxe baseada na função, a Função Entidade lida explicitamente com o despacho de operação, e gere explicitamente o estado da entidade. Por exemplo, o código seguinte mostra a entidade *Contador* implementada utilizando a sintaxe baseada na função.  

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

### <a name="the-entity-context-object"></a>O objeto de contexto da entidade

A funcionalidade específica da entidade pode ser acedida através de um objeto de contexto de tipo `IDurableEntityContext`. Este objeto de contexto está disponível como parâmetro para a função da entidade, e através da propriedade async-local `Entity.Current`.

Os seguintes membros fornecem informações sobre a operação atual, e permitem-nos especificar um valor de devolução. 

* `EntityName`: o nome da entidade atualmente em execução.
* `EntityKey`: a chave da entidade atualmente em execução.
* `EntityId`: a identificação da entidade atualmente executante (inclui nome e chave).
* `OperationName`: o nome da operação atual.
* `GetInput<TInput>()`: obtém a entrada para a operação em curso.
* `Return(arg)`: devolve um valor à orquestração que chamou de operação.

Os seguintes membros gerem o estado da entidade (criar, ler, atualizar, eliminar). 

* `HasState`: se a entidade existe, ou seja, tem algum Estado. 
* `GetState<TState>()`: obtém o estado atual da entidade. Se já não existe, é criado.
* `SetState(arg)`: cria ou atualiza o estado da entidade.
* `DeleteState()`: elimina o estado da entidade, se existir. 

Se o estado devolvido por `GetState` for um objeto, pode ser modificado diretamente pelo código de aplicação. Não há necessidade de voltar a chamar `SetState` no final (mas também não há mal). Se `GetState<TState>` for chamado várias vezes, o mesmo tipo deve ser utilizado.

Finalmente, os seguintes membros são usados para sinalizar outras entidades, ou iniciar novas orquestrações:

* `SignalEntity(EntityId, operation, input)`: envia uma mensagem de ida a uma entidade.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: inicia uma nova orquestração.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça conceitos de entidades](durable-functions-entities.md)
