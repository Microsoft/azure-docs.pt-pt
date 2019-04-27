---
title: Notas dos Reliable Actors no ator a serialização do tipo | Documentos da Microsoft
description: Aborda os requisitos básicos para a definição de classes serializáveis que podem ser usados para definir interfaces e Estados dos Reliable Actors do Service Fabric
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725657"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Serialização do tipo de notas no Service Fabric Reliable Actors
Os argumentos de todos os métodos, tipos de resultado das tarefas devolvido por cada método numa interface de ator e objetos armazenados no Gestor de estado de um ator tem de ser [contrato de dados serializáveis](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Isto também se aplica aos argumentos dos métodos definidos na [interfaces de evento de ator](service-fabric-reliable-actors-events.md). (Métodos de interface de eventos de ator sempre retornam void.)

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

A interface é implementada por um ator que utiliza o Gestor de estado para armazenar um `VoicemailBox` objeto:

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

* O objeto é transmitido entre uma instância de ator e um chamador.
* O objeto é salvo no Gestor de estado em que é persistido no disco e replicado para outros nós.

A estrutura de Reliable Actor usa serialização DataContract. Por conseguinte, os objetos de dados personalizados e seus membros devem ser anotados com o **DataContract** e **DataMember** atributos, respectivamente.

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


## <a name="next-steps"></a>Passos Seguintes
* [Coleção de ciclo de vida e libertação da memória de ator](service-fabric-reliable-actors-lifecycle.md)
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo de ator e padrões de design orientado a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
