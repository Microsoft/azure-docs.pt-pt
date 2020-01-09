---
title: Eventos em atores Service Fabric do Azure baseados em ator
description: Saiba mais sobre eventos para Service Fabric Reliable Actors, uma maneira eficaz de se comunicar entre o ator e o cliente.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639671"
---
# <a name="actor-events"></a>Eventos de ator
Os eventos de ator fornecem uma maneira de enviar notificações de melhor esforço do ator para os clientes. Os eventos de ator são projetados para comunicação entre ator e cliente e não devem ser usados para comunicação de ator com ator.

Os trechos de código a seguir mostram como usar eventos de ator em seu aplicativo.

Defina uma interface que descreve os eventos publicados pelo ator. Essa interface deve ser derivada da interface `IActorEvents`. Os argumentos dos métodos devem ser [serializáveis de contrato de dados](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar void, pois as notificações de eventos são uma maneira e melhor esforço.

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
No lado do cliente, implemente o manipulador de eventos.

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

No cliente, crie um proxy para o ator que publica o evento e assine seus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

No caso de failovers, o ator pode fazer failover para um processo ou nó diferente. O proxy de ator gerencia as assinaturas ativas e as assina automaticamente. Você pode controlar o intervalo de nova assinatura por meio da API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Para cancelar a assinatura, use a API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

No ator, publique os eventos conforme eles acontecem. Se houver assinantes para o evento, o tempo de execução dos atores enviará a ele a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passos seguintes
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C#Código de exemplo do .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
