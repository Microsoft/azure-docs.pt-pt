---
title: Gerir o estado do tecido de serviço Azure
description: Saiba como aceder, economizar e remover o estado para um Azure Service Fabric Reliable Ator, e considerações ao desenhar uma aplicação.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: e97fadb374fc45110f5071aab48da0a57eef4284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012794"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Acesso, salvamento e remoção do estado de Atores Fiáveis
[Os Atores fiáveis](service-fabric-reliable-actors-introduction.md) são objetos de linha única que podem encapsular a lógica e o estado e manter o estado de forma fiável. Cada instância de ator tem o seu próprio [gestor estatal](service-fabric-reliable-actors-state-management.md): uma estrutura de dados semelhante a um dicionário que armazena de forma fiável os pares chave/valor. O gerente do Estado é um invólucro em torno de um fornecedor estatal. Pode usá-lo para armazenar dados independentemente da definição de [persistência.](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)

As chaves do gerente do estado devem ser cordas. Os valores são genéricos e podem ser qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gestor do estado devem ser serializáveis por poderem ser transmitidos através da rede a outros nós durante a replicação e podem ser escritos em disco, dependendo da definição de persistência do estado de um ator.

O gestor do estado expõe métodos de dicionário comuns para gerir o estado, semelhantes aos encontrados no Dicionário Fiável.

Para obter informações, consulte [as melhores práticas na gestão do estado do ator.](service-fabric-reliable-actors-state-management.md#best-practices)

## <a name="access-state"></a>Estado de acesso
O Estado é acedido através do gerente do estado por chave. Os métodos de gerente do Estado são todos assíncronos porque podem exigir e-mails de disco quando os atores têm permanecido estado. Após o primeiro acesso, os objetos do estado estão em cache na memória. Repita as operações de acesso aceder a objetos diretamente da memória e regressar sincronizadamente sem incorrer em disco I/O ou sobrecarga de comutação de contexto assíncrona. Um objeto de estado é removido da cache nos seguintes casos:

* Um método de ator lança uma exceção sem manipulação depois de recuperar um objeto do gerente do estado.
* Um ator é reativado, seja depois de ter sido desativado ou após o fracasso.
* As páginas do fornecedor do Estado dizem para o disco. Este comportamento depende da implementação do fornecedor estatal. O fornecedor estatal predefinido para a `Persisted` definição tem este comportamento.

Pode recuperar o estado utilizando uma operação standard *Get* que lança `KeyNotFoundException` (C#) ou `NoSuchElementException` (Java) se não existir uma entrada para a chave:

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

Também pode recuperar o estado utilizando um método *TryGet* que não atira se uma entrada não existir para uma chave:

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

## <a name="save-state"></a>Salvar estado
Os métodos de recuperação do gerente do estado devolvem uma referência a um objeto na memória local. Modificar este objeto apenas na memória local não faz com que seja guardado durão. Quando um objeto é recuperado do gerente do Estado e modificado, deve ser reinserido no gestor do Estado para ser salvo duravelmente.

Pode inserir o estado utilizando um *conjunto*incondicional, que é o equivalente à `dictionary["key"] = value` sintaxe:

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

Pode adicionar estado utilizando um método *Add.* Este método lança `InvalidOperationException` (C#) ou `IllegalStateException` (Java) quando tenta adicionar uma chave que já existe.

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

Também pode adicionar estado utilizando um método *TryAdd.* Este método não atira quando tenta adicionar uma chave que já existe.

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

No final de um método de ator, o gestor do estado guarda automaticamente quaisquer valores que tenham sido adicionados ou modificados por uma operação de inserção ou atualização. Um "save" pode incluir a persistência no disco e a replicação, dependendo das definições utilizadas. Os valores que não foram modificados não são persistidos ou replicados. Se não tiverem sido modificados valores, a operação de salvamento não faz nada. Se a poupança falhar, o estado modificado é descartado e o estado original é recarregado.

Também pode guardar o estado manualmente, chamando o `SaveStateAsync` método na base do ator:

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

## <a name="remove-state"></a>Remover estado
Pode remover o estado permanentemente do gestor estatal de um ator, chamando o método *Remover.* Este método lança `KeyNotFoundException` (C#) ou `NoSuchElementException` (Java) quando tenta remover uma chave que não existe.

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

Também pode remover o estado permanentemente utilizando o método *TryRemove.* Este método não atira quando tenta remover uma chave que não existe.

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

## <a name="next-steps"></a>Passos seguintes

O estado que está armazenado em Reliable Actors deve ser serializado antes de ser escrito em disco e replicado para alta disponibilidade. Saiba mais sobre [a serialização do tipo ator.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Em seguida, saiba mais sobre [diagnósticos de ator e monitorização de desempenho.](service-fabric-reliable-actors-diagnostics.md)
