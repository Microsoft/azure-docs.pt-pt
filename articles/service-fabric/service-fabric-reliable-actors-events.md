---
title: Eventos em atores de tecido de serviço azure baseados em ator
description: Conheça os eventos para Atores Fiáveis de Tecido de Serviço, uma forma eficaz de comunicar entre ator e cliente.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639671"
---
# <a name="actor-events"></a>Eventos de ator
Os eventos de ator fornecem uma forma de enviar notificações de melhor esforço do ator para os clientes. Os eventos de atores são projetados para a comunicação entre actore cliente e não devem ser usados para a comunicação entre actore ator.

Os seguintes fragmentos de código mostram como usar eventos de ator na sua aplicação.

Defina uma interface que descreve os eventos publicados pelo ator. Esta interface deve ser `IActorEvents` derivada da interface. Os argumentos dos métodos devem ser de contrato de [dados.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) Os métodos devem voltar a ser nulos, uma vez que as notificações de eventos são uma forma e um melhor esforço.

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

Sobre o cliente, crie um representante para o ator que publique o evento e subscreva os seus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Em caso de falhas, o ator pode falhar num processo ou nó diferente. O representante do ator gere as subscrições ativas e resubscreve-as automaticamente. Pode controlar o intervalo de `ActorProxyEventExtensions.SubscribeAsync<TEvent>` resubscrição através da API. Para cancelar a `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` subscrição, utilize a API.

No ator, publique os eventos à medida que acontecem. Se houver assinantes do evento, o tempo de execução dos Atores envia-lhes a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passos seguintes
* [Reentração do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Código da amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core Sample Code](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código da amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
