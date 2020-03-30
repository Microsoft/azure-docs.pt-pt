---
title: Tempors e lembretes de atores fiáveis
description: Introdução a tempors e lembretes para atores fiáveis de tecido de serviço, incluindo orientação sobre quando usar cada um.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639620"
---
# <a name="actor-timers-and-reminders"></a>Tempors de ator e lembretes
Os atores podem agendar trabalhos periódicos sobre si mesmos, registando os temporizadores ou lembretes. Este artigo mostra como usar tempors e lembretes e explica as diferenças entre eles.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores de ator fornecem um invólucro simples em torno de um temporizador .NET ou Java para garantir que os métodos de chamada respeitam a conmoeda baseada em turn-based garante que o tempo de execução dos Atores fornece.

Os atores `RegisterTimer`podem utilizar `registerTimer`os métodos `UnregisterTimer`(C#) `unregisterTimer`ou (Java) e (C#) ou (Java) na sua classe base para registar e desregistar os seus temporizadores. O exemplo abaixo mostra a utilização de APIs temporizadores. As APIs são muito semelhantes ao temporizador .NET ou java. Neste exemplo, quando o temporizador é devido, `MoveObject`o tempo de `moveObject`execução dos Atores chamará o método (C#) ou (Java). O método é garantido para respeitar a moeda baseada em turnos. Isto significa que nenhum outro método de ator ou chamadas de temporizador/lembrete estará em andamento até que este callback complete a execução.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

O próximo período do temporizador começa após a chamada completar a execução. Isto implica que o temporizador é parado enquanto o callback está a ser executado e é iniciado quando a chamada termina.

O tempo de execução dos Atores poupa as alterações feitas ao Diretor de Estado do ator quando a chamada terminar. Se ocorrer um erro na salvação do estado, esse objeto ator será desativado e uma nova instância será ativada.

Todos os tempoizadores são parados quando o ator é desativado como parte da recolha de lixo. Nenhuma chamada temporizador é invocada depois disso. Além disso, o tempo de execução dos Atores não retém qualquer informação sobre os tempos que estavam a decorrer antes da desativação. Cabe ao ator registar quaisquer temporizadores de que necessite quando for reativado no futuro. Para mais informações, consulte a secção sobre a recolha de lixo do [ator.](service-fabric-reliable-actors-lifecycle.md)

## <a name="actor-reminders"></a>Lembretes de ator
Lembretes são um mecanismo para desencadear chamadas persistentes num ator em momentos determinados. A sua funcionalidade é semelhante aos temporizadores. Mas, ao contrário dos temporizadores, os lembretes são desencadeados em todas as circunstâncias até que o ator os desregisse explicitamente ou o ator seja explicitamente apagado. Especificamente, os lembretes são desencadeados através de desativações e falhas de ator porque o tempo de execução dos Atores persiste informações sobre os lembretes do ator usando o provedor do estado do ator. Por favor, note que a fiabilidade dos lembretes está ligada às garantias de fiabilidade do Estado fornecidas pelo provedor do Estado ator. Isto significa que para os atores cuja persistência estatal está definida para Nenhum, os lembretes não dispararão após uma falha. 

Para registar um lembrete, `RegisterReminderAsync` um ator chama o método fornecido na classe base, como mostra o seguinte exemplo:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Neste exemplo, `"Pay cell phone bill"` é o nome do lembrete. Esta é uma corda que o ator usa para identificar de forma única um lembrete. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto que está associado ao lembrete. Será passado de volta para o ator como argumento para o `IRemindable.ReceiveReminderAsync`callback lembrete, ou seja, (C#) ou `Remindable.receiveReminderAsync`(Java).

Os atores que usam `IRemindable` lembretes devem implementar a interface, como mostra o exemplo abaixo.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Quando um lembrete é desencadeado, o tempo `ReceiveReminderAsync`de execução `receiveReminderAsync`dos Atores Fiáveis invocará o método (C#) ou (Java) no Ator. Um ator pode registar vários `ReceiveReminderAsync`lembretes, `receiveReminderAsync`e o método (C#) ou (Java) é invocado quando qualquer um desses lembretes é desencadeado. O ator pode usar o nome de `ReceiveReminderAsync`lembrete que `receiveReminderAsync`é passado para o método (C#) ou (Java) para descobrir que lembrete foi desencadeado.

O tempo de execução dos Atores `ReceiveReminderAsync`salva o `receiveReminderAsync`estado do ator quando a chamada (C#) ou (Java) terminar. Se ocorrer um erro na salvação do estado, esse objeto ator será desativado e uma nova instância será ativada.

Para não registar um lembrete, `UnregisterReminderAsync`um ator `unregisterReminderAsync`chama o método (C#) ou (Java), como mostram os exemplos abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Como mostrado acima, `UnregisterReminderAsync`o método `unregisterReminderAsync`(C#) ou `IActorReminder`(Java) aceita `ActorReminder`uma interface (C#) ou (Java). A classe base do `GetReminder`ator suporta `getReminder`um método (C#) ou `IActorReminder`(Java) que `ActorReminder`pode ser usado para recuperar a interface (C#) ou (Java) passando o nome de lembrete. Isto é conveniente porque o ator `IActorReminder`não precisa de `ActorReminder`persistir na interface (C#) ou (Java) que foi devolvida da `RegisterReminder`chamada do método (C#) ou `registerReminder`(Java).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre eventos e reentrabilidade de Ator Fiável:
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentração do ator](service-fabric-reliable-actors-reentrancy.md)
