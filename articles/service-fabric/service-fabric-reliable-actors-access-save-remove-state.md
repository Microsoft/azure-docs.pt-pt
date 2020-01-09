---
title: Gerenciar o estado de Service Fabric do Azure
description: Saiba mais sobre como acessar, salvar e remover o estado de um ator confiável do Azure Service Fabric e considerações ao criar um aplicativo.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645638"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Acessar, salvar e remover Reliable Actors estado
[Reliable Actors](service-fabric-reliable-actors-introduction.md) são objetos de thread único que podem encapsular a lógica e o estado e manter o estado de forma confiável. Cada instância de ator tem seu próprio [Gerenciador de estado](service-fabric-reliable-actors-state-management.md): uma estrutura de dados semelhante a um dicionário que armazena de forma confiável pares de chave/valor. O Gerenciador de estado é um wrapper em um provedor de estado. Você pode usá-lo para armazenar dados, independentemente de qual [configuração de persistência](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) é usada.

As chaves do Gerenciador de estado devem ser cadeias de caracteres. Os valores são genéricos e podem ser qualquer tipo, incluindo tipos personalizados. Os valores armazenados no Gerenciador de estado devem ser serializáveis de contrato de dados porque podem ser transmitidos pela rede para outros nós durante a replicação e podem ser gravados em disco, dependendo da configuração de persistência de estado de um ator.

O Gerenciador de estado expõe métodos comuns de dicionário para o gerenciamento de estado, semelhante àqueles encontrados no dicionário confiável.

Para obter informações, consulte [práticas recomendadas em gerenciamento de estado de ator](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Estado de acesso
O estado é acessado por meio do Gerenciador de estado por chave. Os métodos do Gerenciador de estado são todos assíncronos porque podem exigir e/s de disco quando os atores têm um estado persistente. No primeiro acesso, os objetos de estado são armazenados em cache na memória. Repita os objetos de acesso de operações de acesso diretamente da memória e retorne de forma síncrona sem incorrer em e/s de disco ou sobrecarga assíncrona de alternância de contexto. Um objeto de estado é removido do cache nos seguintes casos:

* Um método de ator gera uma exceção sem tratamento depois de recuperar um objeto do Gerenciador de estado.
* Um ator é reativado, seja após ser desativado ou após uma falha.
* O estado das páginas do provedor de estado para o disco. Esse comportamento depende da implementação do provedor de estado. O provedor de estado padrão para a configuração de `Persisted` tem esse comportamento.

Você pode recuperar o estado usando uma operação *Get* padrão que gera `KeyNotFoundException`(C#) ou `NoSuchElementException`(Java) se não existir uma entrada para a chave:

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

Você também pode recuperar o estado usando um método *TryGet* que não lança se uma entrada não existe para uma chave:

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
Os métodos de recuperação do Gerenciador de estado retornam uma referência a um objeto na memória local. A modificação desse objeto na memória local sozinha não faz com que ele seja salvo permanentemente. Quando um objeto é recuperado do Gerenciador de estado e modificado, ele deve ser reinserido no Gerenciador de estado para ser salvo permanentemente.

Você pode inserir o estado usando um *conjunto*incondicional, que é o equivalente à sintaxe de `dictionary["key"] = value`:

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

Você pode adicionar o estado usando um método *Add* . Esse método gera `InvalidOperationException`(C#) ou `IllegalStateException`(Java) quando tenta adicionar uma chave que já existe.

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

Você também pode adicionar o estado usando um método *TryAdd* . Esse método não gera quando tenta adicionar uma chave que já existe.

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

No final de um método de ator, o Gerenciador de estado salva automaticamente todos os valores que foram adicionados ou modificados por uma operação de inserção ou atualização. Um "Save" pode incluir a persistência para disco e replicação, dependendo das configurações usadas. Os valores que não foram modificados não são persistentes ou replicados. Se nenhum valor tiver sido modificado, a operação de salvamento não fará nada. Se o salvamento falhar, o estado modificado será descartado e o estado original será recarregado.

Você também pode salvar o estado manualmente chamando o método `SaveStateAsync` na base de ator:

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
Você pode remover o estado permanentemente do Gerenciador de estado de um ator chamando o método *Remove* . Esse método gera `KeyNotFoundException`(C#) ou `NoSuchElementException`(Java) quando tenta remover uma chave que não existe.

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

Você também pode remover o estado permanentemente usando o método *TryRemove* . Esse método não gera quando tenta remover uma chave que não existe.

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

O estado armazenado em Reliable Actors deve ser serializado antes de ser gravado em disco e replicado para alta disponibilidade. Saiba mais sobre a [serialização de tipo de ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre o [diagnóstico de ator e o monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md).
