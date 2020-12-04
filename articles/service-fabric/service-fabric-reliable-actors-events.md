---
title: Eventos em atores Azure Service Fabric
description: Conheça os eventos para Service Fabric Reliable Actors, uma forma eficaz de comunicar entre ator e cliente.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571373"
---
# <a name="actor-events"></a>Eventos de atores
Os eventos de ator fornecem uma forma de enviar notificações de melhor esforço do ator para os clientes. Os eventos atores são projetados para comunicação de actor-a-cliente e não devem ser usados para comunicação de actor-ator.

Os seguintes snippets de código mostram como usar eventos de atores na sua aplicação.

Defina uma interface que descreva os eventos publicados pelo ator. Esta interface deve ser derivada da `IActorEvents` interface. Os argumentos dos métodos devem ser [serializáveis em contratos de dados.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) Os métodos devem ser anulados, uma vez que as notificações de eventos são de uma forma e de melhor esforço.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Declare os eventos publicados pelo ator na interface do ator.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Do lado do cliente, implemente o manipulador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

No cliente, crie um representante para o ator que publica o evento e subscreva os seus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Em caso de falhas, o ator pode falhar num processo ou nó diferente. O proxy do ator gere as subscrições ativas e resscreve-as automaticamente. Pode controlar o intervalo de resignagem através da `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para cancelar a subscrição, use a `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Sobre o ator, publique os eventos como eles acontecem. Se houver subscritores do evento, o tempo de execução dos Atores envia-lhes a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passos seguintes
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do ator](/previous-versions/azure/dn971626(v=azure.100))
* [C# Código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Código de amostra de núcleo](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código de amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
