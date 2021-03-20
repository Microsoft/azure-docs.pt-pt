---
title: Guia do Desenvolvedor para Entidades Duráveis em .NET - Funções Azure
description: Como trabalhar com entidades duráveis em .NET com a extensão de Funções Duráveis para Funções Azure.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92740017"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guia do desenvolvedor para entidades duráveis em .NET

Neste artigo, descrevemos as interfaces disponíveis para o desenvolvimento de entidades duráveis com .NET em detalhe, incluindo exemplos e conselhos gerais. 

As funções de entidade fornecem aos desenvolvedores de aplicações sem servidor uma forma conveniente de organizar o estado de aplicação como uma coleção de entidades de grãos finos. Para mais detalhes sobre os conceitos subjacentes, consulte o artigo [Entidades Duráveis: Conceitos.](durable-functions-entities.md)

Atualmente oferecemos duas APIs para entidades dedefinição:

- A **sintaxe baseada na classe** representa as entidades e as operações como classes e métodos. Esta sintaxe produz código facilmente legível e permite que as operações sejam invocadas de forma verificada por tipo através de interfaces. 

- A **sintaxe baseada na função** é uma interface de nível inferior que representa as entidades como funções. Fornece um controlo preciso sobre a forma como as operações da entidade são expedidas e como o estado da entidade é gerido.  

Este artigo centra-se principalmente na sintaxe baseada na classe, pois esperamos que seja mais adequado para a maioria das aplicações. No entanto, a [sintaxe baseada em funções](#function-based-syntax) pode ser adequada para aplicações que desejem definir ou gerir as suas próprias abstrações para o estado e operações da entidade. Além disso, pode ser apropriado para a implementação de bibliotecas que exijam genéricos não suportados atualmente pela sintaxe baseada na classe. 

> [!NOTE]
> A sintaxe baseada na classe é apenas uma camada em cima da sintaxe baseada na função, para que ambas as variantes possam ser usadas intercambiavelmente na mesma aplicação. 
 
## <a name="defining-entity-classes"></a>Definição de classes de entidades

Segue-se a implementação de uma `Counter` entidade que armazena um único valor de tipo inteiro, e oferece quatro operações, `Add` e `Reset` `Get` `Delete` .

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

A `Run` função contém a placa de caldeira necessária para a utilização da sintaxe baseada na classe. Deve ser uma função *estática* de Azure. Executa uma vez por cada mensagem de operação que é processada pela entidade. Quando `DispatchAsync<T>` é chamado e a entidade já não está na memória, constrói um objeto de tipo e `T` povoa os seus campos a partir do último JSON persistido encontrado no armazenamento (se houver). Em seguida, invoca o método com o nome correspondente.

> [!NOTE]
> O estado de uma entidade baseada na classe é **criado implicitamente** antes de a entidade processar uma operação, e pode ser **eliminado explicitamente** numa operação através da chamada `Entity.Current.DeleteState()` .

### <a name="class-requirements"></a>Requisitos de classe
 
As classes de entidades são POCOs (objetos CLR simples e antigos) que não requerem superclasses, interfaces ou atributos especiais. No entanto:

- A classe deve ser construtiva (ver [construção de entidade).](#entity-construction)
- A classe deve ser json-serializável (ver [serialização da entidade).](#entity-serialization)

Além disso, qualquer método que se destine a ser invocado como operação deve satisfazer requisitos adicionais:

- Uma operação deve ter, no máximo, um argumento, e não deve ter sobrecargas ou argumentos genéricos.
- Uma operação destinada a ser chamada de orquestração utilizando uma interface deve regressar `Task` ou `Task<T>` .
- Os argumentos e os valores de devolução devem ser valores ou objetos serializáveis.

### <a name="what-can-operations-do"></a>O que podem fazer as operações?

Todas as operações das entidades podem ler e atualizar o estado da entidade, e as alterações ao estado são automaticamente persistiu no armazenamento. Além disso, as operações podem realizar E/S externos ou outros cálculos, dentro dos limites gerais comuns a todas as Funções Azure.

As operações também têm acesso à funcionalidade fornecida pelo `Entity.Current` contexto:

* `EntityName`: o nome da entidade atualmente executante.
* `EntityKey`: a chave da entidade atualmente executando.
* `EntityId`: o ID da entidade atualmente executante (inclui nome e chave).
* `SignalEntity`: envia uma mensagem de ida para uma entidade.
* `CreateNewOrchestration`: inicia uma nova orquestração.
* `DeleteState`: elimina o estado desta entidade.

Por exemplo, podemos modificar a entidade de contador para que inicie uma orquestração quando o contador atinge 100 e passa o ID da entidade como argumento de entrada:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Aceder diretamente às entidades

As entidades baseadas na classe podem ser acedidas diretamente, utilizando nomes de cordas explícitos para a entidade e suas operações. Fornecemos alguns exemplos abaixo; para uma explicação mais profunda dos conceitos subjacentes (tais como sinais vs. chamadas) consulte a discussão nas [entidades access](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Sempre que possível, recomendamos [o Acesso às entidades através de interfaces,](#accessing-entities-through-interfaces)pois fornece mais verificação de tipo.

### <a name="example-client-signals-entity"></a>Exemplo: cliente sinaliza entidade

A função Azure Http Function implementa uma operação DELETE utilizando convenções REST. Envia um sinal de exclusão para a entidade de contador cuja chave é passada no caminho URL.

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

A função Azure Http é a seguinte uma operação GET utilizando convenções REST. Lê-se o estado atual da entidade de contador cuja chave é passada no caminho do URL.

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
> O objeto devolvido `ReadEntityStateAsync` é apenas uma cópia local, isto é, uma imagem do estado da entidade de algum ponto anterior no tempo. Em particular, pode ser velho, e modificar este objeto não tem qualquer efeito sobre a entidade real. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exemplo: a orquestração primeiro sinaliza, depois chama entidade

A orquestração que se segue sinaliza uma entidade contra-contrária para a incrementar, e depois chama a mesma entidade para ler o seu valor mais recente.

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

As interfaces podem ser usadas para aceder a entidades através de objetos de procuração gerados. Esta abordagem garante que o nome e o tipo de argumento de uma operação correspondem ao que é implementado. Recomendamos a utilização de interfaces para aceder a entidades sempre que possível.

Por exemplo, podemos modificar o exemplo do contador da seguinte forma:

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

As classes de entidades e interfaces de entidades são semelhantes às interfaces de grãos e cereais popularizadas por [Orleans.](https://www.microsoft.com/research/project/orleans-virtual-actors/) Para obter mais informações sobre semelhanças e diferenças entre Entidades Duradouras e Orleães, consulte [Comparação com atores virtuais.](durable-functions-entities.md#comparison-with-virtual-actors)

Além de fornecer verificação de tipo, as interfaces são úteis para uma melhor separação de preocupações dentro da aplicação. Por exemplo, uma vez que uma entidade pode implementar várias interfaces, uma única entidade pode servir várias funções. Além disso, uma vez que uma interface pode ser implementada por várias entidades, os padrões gerais de comunicação podem ser implementados como bibliotecas reutilizáveis.

### <a name="example-client-signals-entity-through-interface"></a>Exemplo: cliente sinaliza entidade através da interface

O código do cliente pode ser utilizado `SignalEntityAsync<TEntityInterface>` para enviar sinais a entidades que implementem. `TEntityInterface` Por exemplo:

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

Neste exemplo, o `proxy` parâmetro é um exemplo gerado dinamicamente de , que traduz `ICounter` internamente a chamada para um `Delete` sinal.

> [!NOTE]
> As `SignalEntityAsync` APIs só podem ser utilizadas para operações unidirecionais. Mesmo que uma operação retorne, `Task<T>` o valor do parâmetro será sempre `T` nulo ou não o resultado `default` real.
Por exemplo, não faz sentido sinalizar a `Get` operação, uma vez que nenhum valor é devolvido. Em vez disso, os clientes podem usar `ReadStateAsync` o acesso direto ao estado do contador, ou podem iniciar uma função de orquestrador que chama a `Get` operação. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exemplo: a orquestração primeiro sinaliza, depois chama a entidade através de procuração

Para chamar ou sinalizar uma entidade de dentro de uma orquestração, `CreateEntityProxy` pode ser usado, juntamente com o tipo de interface, para gerar um representante para a entidade. Este representante pode então ser utilizado para chamadas ou operações de sinal:

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

Implicitamente, quaisquer operações que regressem `void` são sinalizadas, e quaisquer operações que regressem `Task` ou sejam `Task<T>` chamadas. Pode-se alterar este comportamento predefinido e sinalizar operações mesmo que retornem a Tarefa, utilizando explicitamente o `SignalEntity<IInterfaceType>` método.

### <a name="shorter-option-for-specifying-the-target"></a>Opção mais curta para especificar o alvo

Ao ligar ou sinalizar uma entidade utilizando uma interface, o primeiro argumento deve especificar a entidade-alvo. O alvo pode ser especificado especificando o ID da entidade, ou, nos casos em que há apenas uma classe que implementa a entidade, apenas a chave da entidade:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se apenas a chave da entidade for especificada e uma implementação única não puder ser encontrada no tempo de execução, `InvalidOperationException` é lançada. 

### <a name="restrictions-on-entity-interfaces"></a>Restrições às interfaces das entidades

Como sempre, todos os tipos de parâmetros e devolução devem ser json-serializáveis. Caso contrário, as exceções à serialização são lançadas em tempo de execução.

Também aplicamos algumas regras adicionais:
* As interfaces de entidades só devem definir métodos.
* As interfaces de entidades não devem conter parâmetros genéricos.
* Os métodos de interface da entidade não devem ter mais do que um parâmetro.
* Os métodos de interface da entidade devem `void` `Task` regressar, ou `Task<T>` 

Se alguma destas regras for violada, uma `InvalidOperationException` é lançada no tempo de execução quando a interface é usada como um argumento tipo para ou `SignalEntity` `CreateProxy` . A mensagem de exceção explica qual a regra que foi quebrada.

> [!NOTE]
> Os métodos de retorno `void` da interface só podem ser sinalizados (de ida), não chamados (de duas vias). Os métodos de interface regressam `Task` ou `Task<T>` podem ser chamados ou sinalizados. Se forem chamados, devolvem o resultado da operação ou voltam a lançar exceções lançadas pela operação. No entanto, quando sinalizados, não devolvem o resultado real ou a exceção da operação, mas apenas o valor padrão.

## <a name="entity-serialization"></a>Serialização da entidade

Uma vez que o estado de uma entidade é duramente persistente, a classe de entidade deve ser serializável. O tempo de execução das Funções Duradouras utiliza a biblioteca [Json.NET](https://www.newtonsoft.com/json) para o efeito, que suporta uma série de políticas e atributos para controlar o processo de serialização e deserialização. Os tipos de dados C# mais utilizados (incluindo matrizes e tipos de recolha) já são serializáveis, e podem ser facilmente utilizados para definir o estado das entidades duráveis.

Por exemplo, Json.NET podem facilmente serializar e deserizar a seguinte classe:

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

No exemplo acima, escolhemos incluir vários atributos para tornar a serialização subjacente mais visível:
- Anotamos a classe com `[JsonObject(MemberSerialization.OptIn)]` que nos lembramos que a classe deve ser serializável, e persistir apenas membros que são explicitamente marcados como propriedades JSON.
-  Anotamos os campos a persistir `[JsonProperty("name")]` para nos lembrar que um campo faz parte do estado da entidade persistiu, e especificar o nome da propriedade a ser usado na representação do JSON.

No entanto, estes atributos não são necessários; outras convenções ou atributos são permitidos desde que trabalhem com Json.NET. Por exemplo, pode-se usar `[DataContract]` atributos, ou nenhum atributo em tudo:

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

Por predefinição, o nome da classe *não* é armazenado como parte da representação JSON: isto é, usamos `TypeNameHandling.None` como definição padrão. Este comportamento predefinido pode ser ultrapassado usando `JsonObject` ou `JsonProperty` atributos.

### <a name="making-changes-to-class-definitions"></a>Alterações nas definições de classe

São necessários alguns cuidados ao fazer alterações a uma definição de classe após a execução de uma aplicação, porque o objeto JSON armazenado pode deixar de corresponder à nova definição de classe. Ainda assim, é frequentemente possível lidar corretamente com a alteração de formatos de dados, desde que se compreenda o processo de desseetoria utilizado por `JsonConvert.PopulateObject` .

Por exemplo, aqui estão alguns exemplos de mudanças e o seu efeito:

1. Se for adicionado um novo imóvel, que não está presente no JSON armazenado, assume o seu valor padrão.
1. Se um imóvel for removido, que está presente no JSON armazenado, o conteúdo anterior perde-se.
1. Se uma propriedade for renomeada, o efeito é como se removesse a antiga e adicionasse uma nova.
1. Se o tipo de propriedade for alterado para que não possa mais ser desseerializado do JSON armazenado, uma exceção é lançada.
1. Se o tipo de propriedade for alterado, mas ainda pode ser desseeralizado a partir do JSON armazenado, fá-lo-á.

Existem muitas opções disponíveis para personalizar o comportamento de Json.NET. Por exemplo, para forçar uma exceção se o JSON armazenado contiver um campo que não esteja presente na classe, especifique o atributo `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` . Também é possível escrever código personalizado para deserialização que pode ler JSON armazenado em formatos arbitrários.

## <a name="entity-construction"></a>Construção de entidades

Às vezes queremos exercer mais controlo sobre a forma como os objetos de entidade são construídos. Agora descrevemos várias opções para alterar o comportamento padrão ao construir objetos de entidade. 

### <a name="custom-initialization-on-first-access"></a>Inicialização personalizada no primeiro acesso

Ocasionalmente precisamos de realizar alguma inicialização especial antes de enviar uma operação para uma entidade que nunca foi acedida, ou que foi eliminada. Para especificar este comportamento, pode-se adicionar um condicional antes do `DispatchAsync` seguinte:

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

### <a name="bindings-in-entity-classes"></a>Encadernações nas classes de entidades

Ao contrário das funções regulares, os métodos de classe de entidade não têm acesso direto às ligações de entrada e saída. Em vez disso, os dados vinculativos devem ser capturados na declaração de função do ponto de entrada e depois transmitidos ao `DispatchAsync<T>` método. Quaisquer objetos passados `DispatchAsync<T>` serão automaticamente passados para o construtor de classes de entidades como argumento.

O exemplo a seguir mostra como uma `CloudBlobContainer` referência da ligação de [entrada de bolhas](../functions-bindings-storage-blob-input.md) pode ser disponibilizada a uma entidade baseada na classe.

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

Para obter mais informações sobre encadernações em Funções Azure, consulte a documentação [Azure Functions Triggers and Bindings.](../functions-triggers-bindings.md)

### <a name="dependency-injection-in-entity-classes"></a>Injeção de dependência nas classes de entidades

As classes de entidades suportam [a injeção de dependência de funções Azure.](../functions-dotnet-dependency-injection.md) O exemplo a seguir demonstra como registar um `IHttpClientFactory` serviço numa entidade baseada em classes.

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

    public HttpEntity(IHttpClientFactory factory)
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
> Para evitar problemas com a serialização, certifique-se de excluir campos destinados a armazenar valores injetados da serialização.

> [!NOTE]
> Ao contrário da utilização da injeção de construtor em funções regulares .NET Azure, o método de ponto de entrada de funções para entidades de base de classe *deve* ser declarado `static` . Declarar um ponto de entrada de função não estática pode causar conflitos entre o inicializador normal de objetos Azure Functions e o inicializador de objetos de entidades duráveis.

## <a name="function-based-syntax"></a>Sintaxe baseada em funções

Até agora, concentrámo-nos na sintaxe baseada na classe, pois esperamos que seja mais adequada para a maioria das aplicações. No entanto, a sintaxe baseada na função pode ser apropriada para aplicações que desejem definir ou gerir as suas próprias abstrações para o estado e operações da entidade. Além disso, pode ser apropriado ao implementar bibliotecas que exijam genéricos não suportados atualmente pela sintaxe baseada na classe. 

Com a sintaxe baseada na função, a Função de Entidade lida explicitamente com o despacho de operação e gere explicitamente o estado da entidade. Por exemplo, o seguinte código mostra a entidade *Counter* implementada utilizando a sintaxe baseada na função.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>O objeto de contexto da entidade

A funcionalidade específica da entidade pode ser acedida através de um objeto de contexto do tipo `IDurableEntityContext` . Este objeto de contexto está disponível como parâmetro para a função da entidade, e através da propriedade async-local `Entity.Current` .

Os seguintes membros fornecem informações sobre a operação atual, e permitem-nos especificar um valor de retorno. 

* `EntityName`: o nome da entidade atualmente executante.
* `EntityKey`: a chave da entidade atualmente executando.
* `EntityId`: o ID da entidade atualmente executante (inclui nome e chave).
* `OperationName`: o nome da operação em curso.
* `GetInput<TInput>()`: obtém a entrada para a operação atual.
* `Return(arg)`: devolve um valor à orquestração que chamou a operação.

Os seguintes membros gerem o estado da entidade (criar, ler, atualizar, excluir). 

* `HasState`: se a entidade existe, isto é, tem algum estado. 
* `GetState<TState>()`: obtém o estado atual da entidade. Se já não existe, é criada.
* `SetState(arg)`: cria ou atualiza o estado da entidade.
* `DeleteState()`: elimina o estado da entidade, se existir. 

Se o estado devolvido `GetState` for um objeto, pode ser modificado diretamente pelo código de aplicação. Não há necessidade de voltar a ligar `SetState` no final (mas também nenhum mal). Se `GetState<TState>` for chamado várias vezes, deve ser utilizado o mesmo tipo.

Finalmente, os seguintes membros são usados para sinalizar outras entidades, ou iniciar novas orquestrações:

* `SignalEntity(EntityId, operation, input)`: envia uma mensagem de ida para uma entidade.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: inicia uma nova orquestração.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os conceitos de entidade](durable-functions-entities.md)
