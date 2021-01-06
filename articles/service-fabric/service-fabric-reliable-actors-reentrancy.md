---
title: Reentrância em Azure Service Fabric atores
description: Introdução à reentrância para Service Fabric Reliable Actors, uma forma de logicamente evitar o bloqueio com base no contexto de chamada.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 9578b676e46d21fe5d30de92ad59c852a8b05f70
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915883"
---
# <a name="reliable-actors-reentrancy"></a>Reentrada de atores fiáveis
O tempo de execução dos Atores Fidedigdos, por padrão, permite a reentrada lógica baseada no contexto de chamadas. Isto permite que os atores sejam reentrantes se estiverem na mesma cadeia de contexto de chamada. Por exemplo, o ator A envia uma mensagem ao Ator B, que envia uma mensagem ao Ator C. Como parte do processamento da mensagem, se o Ator C chamar o Ator A, a mensagem é reentrante, por isso será permitida. Quaisquer outras mensagens que façam parte de um contexto de chamada diferente serão bloqueadas no Ator A até que termine o processamento.

Existem duas opções disponíveis para a reentrada do ator definidas no `ActorReentrancyMode` enum:

* `LogicalCallContext` (comportamento predefinido)
* `Disallowed` - desativa a reentrada

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
A reentrada pode ser configurada em `ActorService` configurações durante o registo. A configuração aplica-se a todas as instâncias de ator criadas no serviço de ator.

O exemplo a seguir mostra um serviço de ator que define o modo de reentrada para `ActorReentrancyMode.Disallowed` . Neste caso, se um ator enviar uma mensagem de reentrante a outro ator, uma exceção do tipo `FabricException` será lançada.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a reentrância na documentação de referência da [API do Ator)](/dotnet/api/microsoft.servicefabric.actors?view=azure-dotnet)
