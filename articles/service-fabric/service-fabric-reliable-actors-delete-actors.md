---
title: Eliminar atores de tecido de serviço Azure
description: Aprenda a eliminar manualmente e totalmente os Atores Fiáveis e o seu estado numa aplicação Azure Service Fabric.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645621"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminar o Reliable Actors e o estado
A recolha de lixo de atores desativados só limpa o objeto do ator, mas não remove dados que são armazenados no State Manager de um ator. Quando um ator é reativado, os seus dados são novamente disponibilizados através do State Manager. Nos casos em que os atores armazenam dados em State Manager e são desativados mas nunca reativados, pode ser necessário limpar os seus dados.

O [Serviço de Ator](service-fabric-reliable-actors-platform.md) fornece uma função para apagar atores de um chamador remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

A desposição de um ator tem os seguintes efeitos dependendo se o ator está ou não ativo:

* **Ator Ativo**
  * O ator é removido da lista de atores ativos e é desativado.
  * O seu estado é apagado permanentemente.
* **Ator Inativo**
  * O seu estado é apagado permanentemente.

Um ator não pode chamar a si mesmo de um dos seus métodos de ator porque o ator não pode ser apagado enquanto executa dentro de um contexto de chamada de ator, em que o tempo de execução obteve um bloqueio em torno do ator chamada para impor o acesso de um fio único.

Para mais informações sobre atores fiáveis, leia o seguinte:
* [Tempors de ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentração do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Código da amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código da amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
