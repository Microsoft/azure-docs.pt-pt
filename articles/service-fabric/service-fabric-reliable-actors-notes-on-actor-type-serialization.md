---
title: Reliable Actors observações sobre a serialização do tipo de ator
description: Discute os requisitos básicos para definir classes serializáveis que podem ser usadas para definir Service Fabric Estados e interfaces de Reliable Actors
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349311"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Observações sobre serialização de tipo de Reliable Actors Service Fabric
Os argumentos de todos os métodos, os tipos de resultado das tarefas retornadas por cada método em uma interface de ator e os objetos armazenados no Gerenciador de estado de um ator devem ser [serializáveis no contrato de dados](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Isso também se aplica aos argumentos dos métodos definidos nas [interfaces de evento do ator](service-fabric-reliable-actors-events.md). (Os métodos de interface de evento de ator sempre retornam void.)

## <a name="custom-data-types"></a>Tipos de dados personalizados
Neste exemplo, a seguinte interface de ator define um método que retorna um tipo de dados personalizado chamado `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

A interface é implementada por um ator que usa o Gerenciador de estado para armazenar um objeto de `VoicemailBox`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Neste exemplo, o objeto `VoicemailBox` é serializado quando:

* O objeto é transmitido entre uma instância de ator e um chamador.
* O objeto é salvo no Gerenciador de estado onde ele é persistido no disco e replicado para outros nós.

A estrutura de ator confiável usa a serialização DataContract. Portanto, os objetos de dados personalizados e seus membros devem ser anotados com os atributos **DataContract** e **DataMember** , respectivamente.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Passos seguintes
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Temporizadores e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo do ator e padrões de design orientados a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico de ator e monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md)
