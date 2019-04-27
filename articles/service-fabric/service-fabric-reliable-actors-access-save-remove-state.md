---
title: Gerir o estado do Azure Service Fabric | Documentos da Microsoft
description: Saiba como aceder, guardar e remover o estado de Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725402"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Aceder, guardar e remover o estado de Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) são objetos de thread único de mensagens em fila que podem encapsular a lógica e o estado e manter o estado de forma fiável. Todas as instâncias de ator tem a sua própria [Gestor de estado](service-fabric-reliable-actors-state-management.md): pares chave/valor uma estrutura de dicionário de dados que armazena de forma fiável. O Gestor de estado é um wrapper em torno de um fornecedor de estado. Pode usá-lo para armazenar dados, independentemente de qual [definição de persistência](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) é utilizado.

Chaves de Gestor de estado tem de ser cadeias de caracteres. Valores são genéricos e pode ser qualquer tipo, incluindo tipos personalizados. Valores armazenados no Gestor de estado tem de ser serializável de contrato de dados porque eles podem ser transmitidos pela rede a outros nós durante a replicação e podem ser gravados em disco, consoante a definição de persistência do Estado de um ator.

O Gestor de estado expõe métodos de dicionário comuns para gerir o estado, semelhante àquelas encontradas no dicionário fiável.

Para obter informações, consulte [melhores práticas no gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Estado de acesso
Estado é acedido através do Gestor de estado por chave. Os métodos de Gestor de estado são assíncronos porque possam ser necessárias e/s de disco quando atores tem mantido o estado. No primeiro acesso, os objetos de estado são colocadas em cache na memória. Repita os objetos de acesso de operações de acesso diretamente da memória e retornar de forma síncrona sem incorrer em e/s de disco ou assíncrona de alternância de contexto sobrecarga. Um objeto de estado é removido do cache nos seguintes casos:

* Um método de ator lança uma exceção não processada depois que ele recupera um objeto do Gestor de estado.
* Um ator é reativado, após a ser desativada ou após falha.
* O fornecedor de estado de páginas de estado para o disco. Este comportamento depende a implementação do Provedor de estado. O fornecedor de estado predefinido para o `Persisted` definição tem esse comportamento.

Pode obter o estado ao utilizar uma norma *Obtenha* operação que lança `KeyNotFoundException`(C#) ou `NoSuchElementException`(Java), se não existir uma entrada para a chave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Também pode obter o estado utilizando um *TryGet* método que não inicia se não existir uma entrada para uma chave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Guardar Estado
Os métodos de recuperação do Gestor de estado devolvem uma referência a um objeto na memória local. Modificar este objeto na memória local sozinho não causa ser guardadas de maneira duradoura. Quando um objeto é obtido a partir do Gestor de estado e modificado, tem de ser reinserido no Gerenciador de estado para serem salvos de maneira duradoura.

Pode inserir o estado, utilizando um incondicional *definir*, que é o equivalente a `dictionary["key"] = value` sintaxe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Pode adicionar o estado, utilizando uma *adicionar* método. Esse método lança `InvalidOperationException`(C#) ou `IllegalStateException`(Java), quando tentar adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Também pode adicionar o estado ao utilizar um *TryAdd* método. Este método não gerar quando tentar adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

No final de um método de ator, o Gerenciador de estado guarda automaticamente quaisquer valores que foram adicionados ou modificados por uma operação de inserção ou atualização. Um "Guardar" pode incluir a persistência para o disco e a replicação, consoante as definições utilizadas. Os valores que não foram modificados não são persistentes ou replicados. Se não existem valores tiverem sido modificados, o salvamento operação não faz nada. Se a guardar falhar, o estado modificado é descartado e o estado original é recarregado.

Também pode guardar estado manualmente, chamando o `SaveStateAsync` método na base de ator:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Remover Estado
Pode remover estado permanentemente do Gestor de estado de um ator ao chamar o *remover* método. Esse método lança `KeyNotFoundException`(C#) ou `NoSuchElementException`(Java) ao tentar remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Pode também remover estado permanentemente com o *TryRemove* método. Este método não gerar ao tentar remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Estado que é armazenado no Reliable Actors deve ser serializado antes de sua gravada em disco e replicados para elevada disponibilidade. Saiba mais sobre [serialização do tipo de Ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre [monitorização de desempenho e diagnóstico de Ator](service-fabric-reliable-actors-diagnostics.md).
