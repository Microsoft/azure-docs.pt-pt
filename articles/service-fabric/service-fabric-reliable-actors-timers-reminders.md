---
title: Reliable Actors temporizadores e lembretes | Documentos da Microsoft
description: Introdução ao lembretes e temporizadores dos Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667434"
---
# <a name="actor-timers-and-reminders"></a>Lembretes e temporizadores de ator
Actors podem agendar trabalho periódico nos próprios registrando temporizadores e lembretes. Este artigo mostra como usar temporizadores e lembretes e explica as diferenças entre eles.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores de ator fornecem um wrapper simple em torno de um temporizador de .NET ou Java para se certificar de que os métodos de retorno de chamada respeitam a simultaneidade turnos garante que fornece o tempo de execução de Atores.

Actors podem utilizar o `RegisterTimer`(C#) ou `registerTimer`(Java) e `UnregisterTimer`(C#) ou `unregisterTimer`métodos (Java) em sua classe base para se registrar e cancelar o registro de suas temporizadores. O exemplo abaixo mostra a utilização de APIs do temporizador. As APIs são muito semelhantes para o temporizador de .NET ou o temporizador de Java. Neste exemplo, quando o temporizador é concluído, o tempo de execução de Atores chamará o `MoveObject`(C#) ou `moveObject`método (Java). O método é garantido que respeite a simultaneidade turnos. Isso significa que não existem outros métodos de ator ou retornos de chamada do timer/lembrete será em curso até que esse retorno de chamada seja concluída a execução.

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

O período seguinte do temporizador inicia após o retorno de chamada de conclusão da execução. Isso implica que o temporizador é parado, enquanto o retorno de chamada está em execução e é iniciado quando o retorno de chamada é concluída.

O tempo de execução de Atores guarda as alterações feitas ao Gestor de estado do ator após a conclusão de retorno de chamada. Se ocorrer um erro ao guardar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Todos os temporizadores são paradas quando o ator é desativado como parte da coleta de lixo. Nenhum retorno de chamada do timer é chamados depois disso. Além disso, o tempo de execução de Atores não mantém qualquer informação sobre os timers que estavam em execução antes da desativação. É até o ator para registar a existirem outros timers que ela precisa quando ele for reativado no futuro. Para obter mais informações, consulte a secção sobre [coleta de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Lembretes de ator
Lembretes são um mecanismo para acionar chamadas de retorno persistentes num ator em determinados horários. Sua funcionalidade é semelhante a temporizadores. Mas, ao contrário de temporizadores, lembretes são acionados em todas as circunstâncias até que o ator explicitamente anula o registo-los ou eliminar explicitamente o ator. Especificamente, os lembretes são acionados em desativações de ator e ativações pós-falha porque o tempo de execução de Atores persistir informações sobre lembretes de ator com o fornecedor de estado do ator. Tenha em atenção que a confiabilidade de lembretes está ligada às garantias de confiabilidade de estado fornecidas pelo fornecedor de estado de ator. Isso significa que para atores cuja persistência de estado é definida como None, os lembretes não serão disparado após uma ativação pós-falha. 

Para registar um lembrete, um ator chama o `RegisterReminderAsync` método fornecido na classe base, conforme mostrado no exemplo a seguir:

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

Neste exemplo, `"Pay cell phone bill"` é o nome de lembrete. Esta é uma cadeia de caracteres que o ator utiliza para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto que estão associado com o lembrete. Ela será transmitida para o ator como um argumento para o retorno de chamada de lembrete, ou seja `IRemindable.ReceiveReminderAsync`(C#) ou `Remindable.receiveReminderAsync`(Java).

Atores que utilizam lembretes tem de implementar o `IRemindable` interface, conforme mostrado no exemplo abaixo.

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

Quando for acionado um lembrete, o tempo de execução do Reliable Actors invocará o `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`método de (Java) no Ator. Um ator pode registar várias lembretes e o `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) método é invocado quando qualquer um desses lembretes é acionado. O ator pode utilizar o nome de lembrete que é passado para o `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`método de (Java) para descobrir qual lembrete foi acionada.

Os Atores guarda de tempo de execução do ator de estado quando o `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`conclusão da chamada (Java). Se ocorrer um erro ao guardar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Para anular o registo de um lembrete, um ator chama o `UnregisterReminderAsync`(C#) ou `unregisterReminderAsync`método (Java), conforme mostrado nos exemplos abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Conforme mostrado acima, o `UnregisterReminderAsync`(C#) ou `unregisterReminderAsync`(Java) método aceita um `IActorReminder`(C#) ou `ActorReminder`interface (Java). O suporte de classe base do ator uma `GetReminder`(C#) ou `getReminder`método de (Java) que pode ser usado para recuperar o `IActorReminder`(C#) ou `ActorReminder`interface (Java) ao transmitir o nome de lembrete. Isso é conveniente porque o ator não precisa de manter os `IActorReminder`(C#) ou `ActorReminder`interface (Java) que foi devolvido do `RegisterReminder`(C#) ou `registerReminder`chamada de método (Java).

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre eventos de Reliable Actor e reentrância:
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
