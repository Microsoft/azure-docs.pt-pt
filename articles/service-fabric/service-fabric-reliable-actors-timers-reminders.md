---
title: Reliable Actors temporizadores e lembretes
description: Introdução a temporizadores e lembretes para Service Fabric Reliable Actors, incluindo diretrizes sobre quando usar cada um deles.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639620"
---
# <a name="actor-timers-and-reminders"></a>Temporizadores e lembretes de ator
Os atores podem agendar o trabalho periódico por conta própria registrando temporizadores ou lembretes. Este artigo mostra como usar temporizadores e lembretes e explica as diferenças entre eles.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores de ator fornecem um wrapper simples em relação a um temporizador .NET ou Java para garantir que os métodos de retorno de chamada respeitem as garantias de simultaneidade baseadas em ativação que o tempo de execução dos atores fornece.

Os atores podem usar os métodosC#`RegisterTimer`() ou `registerTimer`(Java) eC#`UnregisterTimer`() ou `unregisterTimer`(Java) em sua classe base para registrar e cancelar o registro de seus temporizadores. O exemplo a seguir mostra o uso de APIs de temporizador. As APIs são muito semelhantes ao temporizador do .NET ou ao temporizador Java. Neste exemplo, quando o temporizador for devido, o tempo de execução dos atores chamaráC#o método `MoveObject`() ou `moveObject`(Java). O método é garantido para respeitar a simultaneidade baseada em troca. Isso significa que nenhum outro método de ator ou retornos de chamada de timer/lembrete estarão em andamento até que esse retorno de chamada conclua a execução.

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

O próximo período do temporizador é iniciado depois que o retorno de chamada conclui a execução. Isso significa que o temporizador é interrompido enquanto o retorno de chamada está em execução e é iniciado quando o retorno de chamada é concluído.

O tempo de execução dos atores salva as alterações feitas no Gerenciador de estado do ator quando o retorno de chamada é concluído. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Todos os temporizadores são interrompidos quando o ator é desativado como parte da coleta de lixo. Nenhum retorno de chamada de temporizador é invocado depois disso. Além disso, o tempo de execução dos atores não retém nenhuma informação sobre os temporizadores que estavam em execução antes da desativação. Cabe ao ator registrar todos os temporizadores de que precisa quando ele é reativado no futuro. Para obter mais informações, consulte a seção sobre [coleta de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Lembretes de ator
Os lembretes são um mecanismo para disparar retornos de chamada persistentes em um ator em horários especificados. Sua funcionalidade é semelhante a temporizadores. Mas, ao contrário dos temporizadores, os lembretes são disparados em todas as circunstâncias até que o ator explicitamente cancele o registro deles ou o ator seja explicitamente excluído. Especificamente, os lembretes são disparados entre as desativações de ator e failovers porque o tempo de execução dos atores persiste informações sobre os lembretes do ator usando o provedor de estado do ator. Observe que a confiabilidade dos lembretes está vinculada às garantias de confiabilidade de estado fornecidas pelo provedor de estado do ator. Isso significa que para os atores cuja persistência de estado está definida como nenhum, os lembretes não serão acionados após um failover. 

Para registrar um lembrete, um ator chama o método `RegisterReminderAsync` fornecido na classe base, conforme mostrado no exemplo a seguir:

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

Neste exemplo, `"Pay cell phone bill"` é o nome do lembrete. Essa é uma cadeia de caracteres que o ator usa para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto associado ao lembrete. Ele será devolvido ao ator como um argumento para o retorno de chamada de lembrete, ou sejaC#, `IRemindable.ReceiveReminderAsync`() ou `Remindable.receiveReminderAsync`(Java).

Os atores que usam lembretes devem implementar a interface `IRemindable`, conforme mostrado no exemplo abaixo.

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

Quando um lembrete é disparado, o tempo de execução deC#Reliable Actors invocará o método `ReceiveReminderAsync`() ou `receiveReminderAsync`(Java) no ator. Um ator pode registrar vários lembretes e o método `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) é invocado quando qualquer um desses lembretes é disparado. O ator pode usar o nome do lembrete que é passado para o métodoC#`ReceiveReminderAsync`() ou `receiveReminderAsync`(Java) para descobrir qual lembrete foi disparado.

O tempo de execução dos atores salva o estado do ator quandoC#a chamada `ReceiveReminderAsync`() ou `receiveReminderAsync`(Java) é concluída. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Para cancelar o registro de um lembrete, um ator chamaC#o método `UnregisterReminderAsync`() ou `unregisterReminderAsync`(Java), conforme mostrado nos exemplos abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Como mostrado acima, o método `UnregisterReminderAsync`C#() ou `unregisterReminderAsync`(Java) aceita uma interface `IActorReminder`C#() ou `ActorReminder`(Java). A classe base de ator dá suporte aC#um método `GetReminder`() ou `getReminder`(Java) que pode ser usado para recuperarC#a interface `IActorReminder`() ou `ActorReminder`(Java) passando o nome do lembrete. Isso é conveniente porque o ator não precisa manter a interface `IActorReminder`(C#) ou `ActorReminder`(Java) retornada pela chamada do método `RegisterReminder`(C#) ou `registerReminder`(Java).

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre os eventos e a reentrância do ator confiável:
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
