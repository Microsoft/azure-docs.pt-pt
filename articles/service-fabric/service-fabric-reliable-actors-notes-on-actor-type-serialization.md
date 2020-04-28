---
title: Notas de atores fiáveis sobre serialização do tipo ator
description: Discute requisitos básicos para definir classes serializáveis que podem ser usadas para definir estados e interfaces de Atores fiáveis de tecido de serviço
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75349311"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Notas sobre fabricação de serviço função Função Função Atores fiáveis serialização
Os argumentos de todos os métodos, os tipos de resultados das tarefas devolvidas por cada método numa interface de ator, e os objetos armazenados no gerente do estado de um ator devem ser um contrato de [dados.](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) Isto aplica-se também aos argumentos dos métodos definidos nas interfaces de [eventos](service-fabric-reliable-actors-events.md)de atores. (Os métodos de interface de evento de ator sempre devolvem o vazio.)

## <a name="custom-data-types"></a>Tipos de dados personalizados
Neste exemplo, a seguinte interface de ator define um `VoicemailBox`método que devolve um tipo de dados personalizado chamado:

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

A interface é implementada por um ator que `VoicemailBox` usa o gerente do Estado para armazenar um objeto:

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

Neste exemplo, `VoicemailBox` o objeto é serializado quando:

* O objeto é transmitido entre uma instância de ator e um chamador.
* O objeto é guardado no gerente do Estado onde é persistido para discor e replicado para outros nódosos.

A estrutura do Ator Fiável utiliza a serialização dataContract. Por conseguinte, os objetos de dados personalizados e os seus membros devem ser anotados com os atributos **DataContract** e **DataMember,** respectivamente.

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
* [Ciclo de vida do ator e recolha de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Tempors de ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentração do ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo do ator e padrões de design orientados para objetos](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
