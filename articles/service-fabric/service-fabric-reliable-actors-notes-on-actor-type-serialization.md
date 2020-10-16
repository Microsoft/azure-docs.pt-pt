---
title: Notas de atores fiáveis sobre a serialização do tipo de ator
description: Discute os requisitos básicos para a definição de classes serializáveis que podem ser usadas para definir estados e interfaces de tecido de serviços fiáveis
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 33a15b3c6f570d3b4d36a205f91bce7b042f3bb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016585"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Notas sobre tecido de serviço Fiáveis Atores tipo serialização
Os argumentos de todos os métodos, tipos de resultados das tarefas devolvidas por cada método numa interface de ator, e objetos armazenados no gestor estatal de um ator devem ser [serializáveis por contratos de dados](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Isto aplica-se também aos argumentos dos métodos definidos nas [interfaces de eventos de ator.](service-fabric-reliable-actors-events.md) (Os métodos de interface de evento de ator voltam sempre vazios.)

## <a name="custom-data-types"></a>Tipos de dados personalizados
Neste exemplo, a interface do ator que se segue define um método que devolve um tipo de dados personalizado chamado `VoicemailBox` :

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

A interface é implementada por um ator que usa o gerente do estado para armazenar um `VoicemailBox` objeto:

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

Neste exemplo, o `VoicemailBox` objeto é serializado quando:

* O objeto é transmitido entre uma instância do ator e um chamador.
* O objeto é guardado no gestor do estado onde é persistido no disco e replicado para outros nós.

A estrutura do Ator Fiável utiliza a serialização dataContract. Por isso, os objetos de dados personalizados e os seus membros devem ser anotados com os **atributos DataContract** e **DataMember,** respectivamente.

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
* [Ciclo de vida do ator e coleção de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Temporizadores e lembretes do ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de atores](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo do ator e padrões de design orientados a objetos](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
