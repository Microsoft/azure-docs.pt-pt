---
title: Reentrância no Azure Service Fabric atores
description: Introdução à reentrância para Service Fabric Reliable Actors, uma maneira de evitar logicamente o bloqueio com base no contexto de chamada.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645536"
---
# <a name="reliable-actors-reentrancy"></a>Reentrada Reliable Actors
O tempo de execução de Reliable Actors, por padrão, permite a reentrância baseada em contexto de chamada lógica. Isso permite que os atores sejam reentrante se estiverem na mesma cadeia de contexto de chamada. Por exemplo, o ator A envia uma mensagem para o ator B, que envia uma mensagem para o ator C. Como parte do processamento de mensagens, se o ator C chamar o ator A, a mensagem será reentrante e, portanto, será permitida. Qualquer outra mensagem que faça parte de um contexto de chamada diferente será bloqueada no ator A até que o processamento seja concluído.

Há duas opções disponíveis para a reentrância do ator definida no `ActorReentrancyMode` enum:

* `LogicalCallContext` (comportamento padrão)
* `Disallowed`-desabilita a reentrância

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
A reentrância pode ser configurada nas configurações de um `ActorService`durante o registro. A configuração se aplica a todas as instâncias de ator criadas no serviço de ator.

O exemplo a seguir mostra um serviço de ator que define o modo de reentrância como `ActorReentrancyMode.Disallowed`. Nesse caso, se um ator enviar uma mensagem reentrante para outro ator, uma exceção do tipo `FabricException` será lançada.

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
* Saiba mais sobre a reentrância na documentação de [referência da API do ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
