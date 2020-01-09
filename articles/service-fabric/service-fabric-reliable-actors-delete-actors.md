---
title: Excluir atores de Service Fabric do Azure
description: Saiba como excluir manualmente e completamente Reliable Actors e seu estado em um aplicativo de Service Fabric do Azure.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645621"
---
# <a name="delete-reliable-actors-and-their-state"></a>Excluir Reliable Actors e seu estado
A coleta de lixo de atores desativados limpa apenas o objeto de ator, mas não remove os dados armazenados no Gerenciador de estado de um ator. Quando um ator é reativado, seus dados são novamente disponibilizados para ele por meio do Gerenciador de estado. Nos casos em que os atores armazenam dados no Gerenciador de estado e são desativados, mas nunca reativados, pode ser necessário limpar seus dados.

O [serviço de ator](service-fabric-reliable-actors-platform.md) fornece uma função para excluir atores de um chamador remoto:

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

A exclusão de um ator tem os seguintes efeitos dependendo se o ator está ativo no momento:

* **Ator ativa**
  * O ator é removido da lista de atores ativos e é desativado.
  * Seu estado é excluído permanentemente.
* **Ator inativa**
  * Seu estado é excluído permanentemente.

Um ator não pode chamar Delete em si mesmo por meio de um de seus métodos de ator porque o ator não pode ser excluído durante a execução em um contexto de chamada de ator, no qual o tempo de execução obteve um bloqueio em volta da chamada de ator para impor o acesso de thread único.

Para obter mais informações sobre Reliable Actors, leia o seguinte:
* [Temporizadores e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
