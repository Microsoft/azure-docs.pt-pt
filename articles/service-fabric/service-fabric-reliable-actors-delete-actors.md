---
title: Eliminar atores do Azure Service Fabric | Documentos da Microsoft
description: Saiba como eliminar manualmente os Reliable Actors do Service Fabric e o respetivo estado.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: ab6f8740ec1955078c6acbd9cb362a25a89e4bda
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887396"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminar Reliable Actors e o respetivo estado
Coleta de lixo de atores desativados limpa apenas o objeto de ator, mas não remove os dados armazenados no Gestor de estado de um ator. Quando um ator é reativado, seus dados é novamente disponibilizados ao mesmo através do Gestor de estado. Nos casos onde atores armazenar dados no Gestor de estado e estiverem desativados, mas nunca reativados, poderá ser necessário limpar os seus dados.

O [serviço de Ator](service-fabric-reliable-actors-platform.md) fornece uma função para a eliminar atores de um chamador remoto:

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

A eliminação de um ator tem os seguintes efeitos dependendo se pretende ou não o ator está ativo no momento:

* **Ator do Active Directory**
  * Ator é removido da lista de atores do Active Directory e é desativado.
  * O estado é eliminado permanentemente.
* **Ator inativo**
  * O estado é eliminado permanentemente.

Não é possível chamar um ator eliminar em si mesmo a partir de um de seus métodos de ator, uma vez que o ator não pode ser eliminado durante a execução dentro de um contexto de chamada de ator, em que o tempo de execução foi obtido um bloqueio em torno da chamada de ator para impor o acesso de thread único.

Para obter mais informações sobre Reliable Actors, leia o seguinte:
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo de c#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
