---
title: Temporizadores e lembretes de atores confiáveis
description: Introdução aos temporizadores e lembretes para Service Fabric Reliable Actors, incluindo orientações sobre quando usar cada um.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791799"
---
# <a name="actor-timers-and-reminders"></a>Temporizadores e lembretes do ator
Os atores podem agendar trabalhos periódicos sobre si mesmos, registando ou temporizadores ou lembretes. Este artigo mostra como usar temporizadores e lembretes e explica as diferenças entre eles.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores do ator fornecem um invólucro simples em torno de um temporizador .NET ou Java para garantir que os métodos de retorno respeitam a concordância baseada na curva garante que o tempo de execução dos Atores fornece.

Os atores podem usar os `RegisterTimer` métodos (C#) ou `registerTimer` (Java) e `UnregisterTimer` (C#) ou `unregisterTimer` (Java) na sua classe base para registar e não registar os seus temporizadores. O exemplo abaixo mostra a utilização de APIs temporizadores. As APIs são muito semelhantes ao temporizador .NET ou ao temporizador Java. Neste exemplo, quando o temporizador é devido, o tempo de execução dos Atores chamará o `MoveObject` método (C#) ou `moveObject` (Java). O método é garantido para respeitar a concordância baseada na viragem. Isto significa que nenhum outro método de ator ou chamadas de temporizador/lembrete estará em andamento até que esta chamada complete a execução.

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

O próximo período do temporizador começa após a chamada completar a execução. Isto implica que o temporizador é interrompido enquanto a chamada está a ser executada e é iniciada quando a chamada termina.

O tempo de execução dos Atores salva as alterações feitas ao Diretor de Estado do ator quando a chamada terminar. Se ocorrer um erro na salvação do estado, esse objeto ator será desativado e será ativado um novo caso.

Ao contrário dos [lembretes,](#actor-reminders)os temporizadores não podem ser atualizados. Se `RegisterTimer` for chamado novamente, um novo temporizador será registado.

Todos os temporizadores são parados quando o ator é desativado como parte da recolha de lixo. Não há chamadas de temporizador depois disso. Além disso, o tempo de execução dos Atores não retém nenhuma informação sobre os temporizadores que estavam a funcionar antes da desativação. Cabe ao ator registar os temporizadores de que necessita quando for reativado no futuro. Para mais informações, consulte a secção sobre [a recolha de lixo do ator.](service-fabric-reliable-actors-lifecycle.md)

## <a name="actor-reminders"></a>Lembretes do ator
Os lembretes são um mecanismo para desencadear chamadas persistentes num ator em horas específicas. A sua funcionalidade é semelhante aos temporizadores. Mas, ao contrário dos temporizadores, os lembretes são desencadeados em todas as circunstâncias até que o ator os desista explicitamente ou o ator seja explicitamente apagado. Especificamente, os lembretes são desencadeados através de desativações e falhas de atores porque o tempo de execução dos Atores persiste em informações sobre os lembretes do ator usando o provedor estatal do ator. Também ao contrário dos temporizadores, os lembretes existentes podem ser atualizados chamando o método de registo ( `RegisterReminderAsync` ) novamente usando o mesmo *nome de lembrete*.

> [!NOTE]
> A fiabilidade dos lembretes está ligada às garantias de fiabilidade do Estado fornecidas pelo prestador estatal de atores. Isto significa que, para os atores cuja persistência do Estado está definida para *Nenhum,* os lembretes não dispararão após uma falha.

Para registar um lembrete, um ator chama o [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) método fornecido na classe base, como mostra o seguinte exemplo:

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

Neste exemplo, `"Pay cell phone bill"` é o nome de lembrete. Esta é uma corda que o ator usa para identificar um lembrete exclusivo. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto que está associado ao lembrete. Será passado de volta para o ator como argumento para o callback lembrete, ou seja, `IRemindable.ReceiveReminderAsync` (C#) ou `Remindable.receiveReminderAsync` (Java).

Os atores que usam lembretes devem implementar a `IRemindable` interface, como mostra o exemplo abaixo.

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

Quando um lembrete é desencadeado, o tempo de execução dos Atores Fidedignas invocará o  `ReceiveReminderAsync` método (C#) ou `receiveReminderAsync` (Java) no Ator. Um ator pode registar vários lembretes, e o `ReceiveReminderAsync` método (C#) ou `receiveReminderAsync` (Java) é invocado quando qualquer um desses lembretes é desencadeado. O ator pode usar o nome de lembrete que é passado para o `ReceiveReminderAsync` método (C#) ou `receiveReminderAsync` (Java) para descobrir que lembrete foi desencadeado.

O tempo de execução dos Atores salva o estado do ator quando a `ReceiveReminderAsync` chamada (C#) ou `receiveReminderAsync` (Java) termina. Se ocorrer um erro na salvação do estado, esse objeto ator será desativado e será ativado um novo caso.

Para desregistar um lembrete, um ator chama o `UnregisterReminderAsync` método (C#) ou `unregisterReminderAsync` (Java), como mostram os exemplos abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Como mostrado acima, o `UnregisterReminderAsync` método (C#) ou `unregisterReminderAsync` (Java) aceita uma `IActorReminder` interface (C#) ou `ActorReminder` (Java). A classe base do ator suporta um `GetReminder` método (C#) ou `getReminder` (Java) que pode ser usado para recuperar a `IActorReminder` interface (C#) ou `ActorReminder` (Java) passando no nome do lembrete. Isto é conveniente porque o ator não precisa de persistir na `IActorReminder` interface (C#) ou `ActorReminder` (Java) que foi devolvida da `RegisterReminder` chamada (C#) ou `registerReminder` (Java).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre eventos de atores fiáveis e reentrada:
* [Eventos de atores](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
