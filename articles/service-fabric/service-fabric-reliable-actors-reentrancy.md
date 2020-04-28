---
title: Reentrancy em atores de tecido de serviço Azure
description: Introdução à reentração para atores fiáveis de tecido de serviço, uma forma logicamente de evitar o bloqueio com base no contexto de chamada.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645536"
---
# <a name="reliable-actors-reentrancy"></a>Reentração de Atores Fiáveis
O tempo de execução dos Atores Fiáveis, por defeito, permite a reentrabilidade lógica baseada no contexto. Isto permite que os atores sejam reinscritos se estiverem na mesma cadeia de contexto de chamada. Por exemplo, o Ator A envia uma mensagem ao ator B, que envia uma mensagem ao Ator C. Como parte do processamento de mensagens, se o ator C chamar o Ator A, a mensagem é recandidatada, por isso será permitida. Quaisquer outras mensagens que façam parte de um contexto de chamada diferente serão bloqueadas no Ator A até que termine o processamento.

Existem duas opções disponíveis para a `ActorReentrancyMode` reentrabilidade do ator definida no enum:

* `LogicalCallContext`(comportamento predefinido)
* `Disallowed`- desativa a reentrabilidade

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
A reentrabilidade pode ser `ActorService`configurada nas definições de uma definição durante o registo. A configuração aplica-se a todas as instâncias de ator criadas no serviço de ator.

O exemplo seguinte mostra um serviço de ator `ActorReentrancyMode.Disallowed`que define o modo de reentrabilidade para . Neste caso, se um ator enviar uma mensagem de reentrada para outro ator, uma exceção do tipo `FabricException` será lançada.

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
* Saiba mais sobre a reentrabilidade na documentação de referência da [API do ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
