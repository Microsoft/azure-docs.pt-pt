---
title: Funcionalidades - as funções do Azure de pré-visualização de funções duráveis
description: Saiba mais sobre as funcionalidades de pré-visualização para funções duráveis.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7101519aa4a87995dac3a7f11046eed84a2c09b6
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812766"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durável Functions 2.0 pré-visualização (funções do Azure)

*Funções duráveis* é uma extensão da [as funções do Azure](../functions-overview.md) e [WebJobs do Azure](../../app-service/web-sites-create-web-jobs.md) que permite que escreva funções com monitoração de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se não já estiver familiarizado com as funções durável, consulte a [documentação de visão geral](durable-functions-overview.md).

Funções duráveis 1.x é uma funcionalidade de disponibilidade geral (disponível em geral) das funções do Azure, mas também contém vários subfuncionalidades que estão atualmente em pré-visualização pública. Este artigo descreve as funcionalidades de pré-visualização recém-lançada e entra em detalhes sobre como funcionam e como pode começar a usá-los.

> [!NOTE]
> Estas funcionalidades de pré-visualização fazem parte de uma versão 2.0 de funções duráveis, o que é atualmente um **qualidade versão de pré-visualização** com várias alterações de última hora. O Azure funções durável cria o pacote de extensão pode ser encontrado em nuget.org com versões na forma de **2.0.0-betaX**. Essas compilações não são direcionadas para cargas de trabalho de produção e versões posteriores podem conter alterações de última hora adicionais.

## <a name="breaking-changes"></a>Alterações interruptivas

Várias alterações de última hora são introduzidas no 2.0 de funções durável. As aplicações existentes não são esperadas para ser compatível com 2.0 de funções durável sem alterações de código. Esta secção lista algumas das alterações:

### <a name="hostjson-schema"></a>Esquema de Host. JSON

O fragmento seguinte mostra o novo esquema para o Host. JSON. As principais alterações estar atento são as novas subsecções:

* `"storageProvider"` (e o `"azureStorage"` subsecção) para a configuração de armazenamento específicas
* `"tracking"` para o controlo e a configuração de registo
* `"notifications"` (e o `"eventGrid"` subsecção) para configuração de notificação do event grid

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Como durável Functions 2.0 continua a estabilizar, mais alterações irão ser apresentadas para o `durableTask` secção Host. JSON. Para obter mais informações sobre estas alterações, consulte [este problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Alterações de interface pública

Os vários objetos de "contexto" suportados pelas funções duráveis tinham as classes base abstratas que pretende utilizar no teste de unidade. Como parte do 2.0 de funções durável, estas classes base abstratas foram substituídas com interfaces. Código de função que usa os tipos concretos diretamente não são afetados.

A tabela seguinte representa as alterações principais:

| Antigo tipo | Novo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

## <a name="entity-functions"></a>Funções de entidade

Funções de entidade definem operações de leitura e atualizando pequenas partes do Estado, conhecido como *entidades duráveis*. Como funções do orchestrator, as funções de entidade são as funções com um tipo de Acionador especial *acionador de entidade*. Ao contrário das funções do orchestrator, as funções de entidade não tem quaisquer restrições de código específica. Funções de entidade também gerir o estado explicitamente em vez de implicitamente que representa o estado através do fluxo de controle.

### <a name="net-programing-models"></a>Modelos de programação do .NET

Existem dois modelos de programação opcionais para a criação de entidades duráveis. O código a seguir é um exemplo de um simples *contador* entidade implementada como uma função padrão. Esta função define três *operations*, `add`, `reset`, e `get`, cada do que operam num valor de estado de número inteiro, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
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

Este modelo mais adequado para implementações de entidade simples ou implementações que tenham um conjunto dinâmico de operações. No entanto, também é um modelo de programação baseado em classes que é útil para entidades que são estáticos, mas têm implementações mais complexas. O exemplo seguinte é uma implementação equivalente do `Counter` entidade com métodos e classes do .NET.

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

O modelo baseado em classes é semelhante ao modelo de programação popularizado por [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Nesse modelo, um tipo de entidade é definido como uma classe do .NET. Cada método da classe é uma operação que pode ser invocada por um cliente externo. Ao contrário de Orleans, no entanto, .NET interfaces são opcionais. Anterior *contador* exemplo não utilizou uma interface, mas ainda pode ser chamada por meio de outras funções ou por meio de chamadas de API de HTTP.

Entidade *instâncias* são acedidas através de um identificador exclusivo, o *ID de entidade*. Um ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

* Uma **nome da entidade**: um nome que identifica o tipo de entidade (por exemplo, "Contador").
* Uma **chave de entidade**: uma cadeia que identifica de forma exclusiva a entidade entre todas as outras entidades, o mesmo nome (por exemplo, um GUID).

Por exemplo, um *contador* a função de entidade pode ser utilizada para manter a classificação num jogo online. Cada instância do jogo tem um ID de entidade exclusivo, tal como `@Counter@Game1`, `@Counter@Game2`e assim por diante.

### <a name="comparison-with-virtual-actors"></a>Comparação com virtual atores

A estrutura de entidades durável é bastante influenciada pela [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se já estiver familiarizado com actors, em seguida, os conceitos por trás de entidades duráveis devem ser familiares para. Em particular, são semelhantes às entidades duráveis [atores virtual](https://research.microsoft.com/projects/orleans/) de várias maneiras:

* Entidades duráveis são endereçáveis através de um *ID de entidade*.
* Operações de entidade durável em série, executadas uma por vez, para evitar condições de corrida.
* Entidades duráveis são criadas automaticamente quando são chamados ou sinalizados.
* Ao não executar operações, duráveis entidades são silenciosamente baixadas da memória.

Existem algumas diferenças importantes, no entanto, que são a pena observar:

* Entidades duráveis priorizar *durabilidade* sobre *latência*e, portanto, pode não ser apropriado para aplicativos com requisitos de latência rigorosa.
* As mensagens enviadas entre entidades são entregues por ordem e confiável.
* Entidades duráveis podem ser utilizadas em conjunto com orquestrações duráveis e podem servir como bloqueios distribuídos, o que são descritos posteriormente neste artigo.
* Padrões de solicitação/resposta nas entidades estão limitados a orquestrações. Para a comunicação de entidade-para-entidade, são permitidas apenas as mensagens unidirecionais (também conhecido como "sinalização"), como no modelo de ator original. Esse comportamento evita deadlocks distribuídos.

### <a name="durable-entity-net-apis"></a>Entidade durável APIs do .NET

Suporte de entidade envolve várias APIs. Para um, existe uma nova API para a definição de funções de entidade, conforme mostrado acima, que especifica o que deve acontecer quando uma operação ser invocada numa entidade. Além disso, as APIs existentes para clientes e orquestrações foram atualizadas com a nova funcionalidade para interação com entidades.

#### <a name="implementing-entity-operations"></a>Implementar operações de entidade

A execução de uma operação numa entidade pode chamar esses membros do objeto de contexto (`IDurableEntityContext` no .NET):

* **OperationName**: obtém o nome da operação.
* **GetInput\<TInput >** : obtém a entrada para a operação.
* **GetState\<TState >** : obtém o estado atual da entidade.
* **SetState**: atualiza o estado da entidade.
* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **Self**: obtém o ID da entidade.
* **Devolver**: retorna um valor para o cliente ou uma orquestração que chama a operação.
* **IsNewlyConstructed**: devolve `true` se a entidade não existia antes da operação.
* **DestructOnExit**: elimina a entidade depois de concluir a operação.

As operações são menos restritas que orquestrações:

* Operações podem chamar a e/s externos, através de APIs síncronas ou assíncronas (Recomendamos que utilize apenas aqueles assíncronas).
* As operações podem ser determinística. Por exemplo, é seguro chamar `DateTime.UtcNow`, `Guid.NewGuid()` ou `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Acesso a entidades de clientes

Entidades duráveis podem ser invocadas a partir de funções comuns através da `orchestrationClient` enlace (`IDurableOrchestrationClient` no .NET). São suportados os seguintes métodos:

* **ReadEntityStateAsync\<T >** : lê o estado de uma entidade.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda para que elas sejam colocados em fila.
* **SignalEntityAsync\<T >** : igual ao `SignalEntityAsync` mas usa um objeto proxy gerado do tipo `T`.

Anterior `SignalEntityAsync` chamada requer a especificação do nome da operação de entidade como uma `string` e o payload da operação como um `object`. O código de exemplo seguinte é um exemplo desse padrão:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Também é possível gerar um objeto de proxy para acesso de tipo seguro. Para gerar um proxy de tipo seguro, o tipo de entidade tem de implementar uma interface. Por exemplo, suponha que o `Counter` entidade mencionada anteriormente implementado um `ICounter` interface, definida da seguinte forma:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Código de cliente, em seguida, poderia usar `SignalEntityAsync<T>` e especifique o `ICounter` interface como parâmetro de tipo para gerar um proxy de tipo seguro. Esse uso de proxies de tipo seguro é demonstrado no exemplo de código a seguir:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

No exemplo anterior, o `proxy` parâmetro é uma instância gerada dinamicamente de `ICounter`, que converte internamente a chamada para `Add` para o equivalente (sem tipos) a chamada para `SignalEntityAsync`.

> [!NOTE]
> É importante ter em atenção que o `ReadEntityStateAsync` e `SignalEntityAsync` métodos de `IDurableOrchestrationClient` priorizar o desempenho ao longo de consistência. `ReadEntityStateAsync` pode retornar um valor obsoleto, e `SignalEntityAsync` pode retornar antes da operação foi concluída.

#### <a name="accessing-entities-from-orchestrations"></a>Acesso a entidades de orquestrações

Orquestrações podem aceder a entidades usando o `IDurableOrchestrationContext` objeto. Podem escolher entre comunicação unidirecional (disparar e esquecer) e a comunicação bidirecional (solicitação e resposta). Os métodos correspondentes são:

* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **CallEntityAsync**: envia uma mensagem para uma entidade e aguarda uma resposta a indicar que a operação foi concluída.
* **CallEntityAsync\<T >** : envia uma mensagem para uma entidade e aguarda uma resposta que contém um resultado do tipo T.

Ao utilizar a comunicação bidirecional, todas as exceções lançadas durante a execução da operação também são transmitidas de volta para a orquestração de chamada e relançadas. Por outro lado, quando utilizar disparar e esquecer, exceções não são respeitadas.

Para obter acesso de tipo seguro, as funções de orquestração podem gerar proxies com base numa interface. O `CreateEntityProxy` método de extensão pode ser utilizado para esta finalidade:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration)]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

No exemplo anterior, uma entidade de "contador" supostamente existe que implementa o `IAsyncCounter` interface. A orquestração, em seguida, foi possível usar o `IAsyncCounter` definição para gerar um tipo de proxy para interagir de forma síncrona com a entidade de tipo.

### <a name="locking-entities-from-orchestrations"></a>Entidades de bloqueios de orquestrações

Orquestrações podem bloquear as entidades. Esta capacidade fornece uma forma simples de evitar corridas indesejadas usando *seções críticas*.

O objeto context fornece os seguintes métodos:

* **LockAsync**: adquire bloqueios numa ou mais entidades.
* **IsLocked**: retorna true se atualmente numa seção crítica, false caso contrário.

A seção crítica ends e todos os bloqueios são lançados, quando termina de orquestração. No .NET, `LockAsync` retorna um `IDisposable` que termina a seção crítica quando descartado, que pode ser utilizada em conjunto com um `using` cláusula para obter uma representação sintática de seção crítica.

Por exemplo, considere uma orquestração que precisa para testar se os dois jogadores estão disponíveis e, em seguida, atribuí-los a um jogo. Esta tarefa pode ser implementada usando uma seção crítica da seguinte forma:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Dentro da seção crítica, as duas entidades de player estejam bloqueadas, que significa que não está a executar quaisquer operações diferentes dos que são chamados a partir da seção crítica). Este comportamento impede corridas com operações em conflito, tais como os jogadores que está sendo atribuídos a outro jogos ou assinatura desativado.

Podemos impõe várias restrições das seções críticas como pode ser utilizado. Estas restrições servem para evitar deadlocks e reentrância.

* Não não possível aninhar seções críticas.
* Seções críticas não é possível criar suborchestrations.
* Seções críticas podem chamar apenas as entidades que têm de ser bloqueados.
* Seções críticas não é possível chamar a mesma entidade usando várias chamadas paralelas.
* Seções críticas podem sinalizar apenas as entidades que não têm a ser bloqueados.

## <a name="alternate-storage-providers"></a>Fornecedores de armazenamento alternativo

A estrutura de tarefa durável suporta vários fornecedores de armazenamento hoje em dia, incluindo [armazenamento do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [do Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), uma [emulador na memória](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e um experimentais [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) fornecedor. No entanto, até agora, a extensão de tarefas durável para as funções do Azure só suportado o fornecedor de armazenamento do Azure. A partir do 2.0 de funções durável, suporte para fornecedores de armazenamento alternativo está a ser adicionada, começando com o fornecedor de Redis.

> [!NOTE]
> 2\.0 de funções durável só suporta os fornecedores de 2.0 compatível com .NET Standard. No momento da escrita, o fornecedor do Azure Service Bus não suporta o .NET Standard 2.0 e, portanto, não está disponível como um fornecedor de armazenamento alternativo.

### <a name="emulator"></a>Emulador

O [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) fornecedor é um local da memória, o fornecedor de armazenamento não duradoura adequado a cenários de testes locais. Pode ser configurada utilizando os seguintes mínimos **Host. JSON** esquema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>(Experimental) de redis

O [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) fornecedor persistir todos os Estados de orquestração para um cluster de Redis configurado.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

O `connectionStringName` tem de referenciar o nome de uma variável de ambiente ou definição de aplicação. Essa variável de ambiente ou definição de aplicação deve conter um valor de cadeia de ligação de Redis na forma de *porta do servidor:* . Por exemplo, `localhost:6379` para ligar a um cluster de Redis local.

> [!NOTE]
> O fornecedor de Redis é atualmente experimental e só suporta aplicações de função em execução num único nó. Não é garantido que o fornecedor de Redis nunca estarão disponível em geral, e pode ser removido numa versão futura.
