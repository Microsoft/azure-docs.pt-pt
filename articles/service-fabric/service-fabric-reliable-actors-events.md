---
title: Eventos no baseada em atores actors do Azure Service Fabric | Documentos da Microsoft
description: Introdução a eventos do Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: fead1cf639a379fc288122e39629909786aa3f14
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57844805"
---
# <a name="actor-events"></a>Eventos de ator
Os eventos de ator fornecem uma forma de enviar notificações de melhor esforço de ator para os clientes. Eventos de ator foram concebidos para a comunicação de cliente a ator e não devem ser utilizados para comunicação de ator-ator.

Os fragmentos de código seguintes mostram como usar eventos de ator em seu aplicativo.

Defina uma interface que descreve eventos publicados pelo ator. Esta interface tem de ser derivada do `IActorEvents` interface. Os argumentos dos métodos tem de ser [contrato de dados serializáveis](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar void, como eventos, notificações são uma forma e de melhor esforço.

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
Declare os eventos publicados por ator na interface de ator.

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

No cliente, criar um proxy para o ator que publica o evento e subscrever os eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Em caso de ativações pós-falha, o ator pode efetuar a ativação pós-falha para um processo diferente ou um nó. O proxy de ator gerencia as subscrições ativas e automaticamente subscreve-los novamente. Pode controlar o intervalo de reavaliação de subscrição por meio do `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para anular a subscrição, utilize o `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

No ator, publicar os eventos à medida que acontecem. Se existirem subscritores ao evento, o tempo de execução de Atores envia a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passos Seguintes
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo de c#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de c# .NET Core exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
