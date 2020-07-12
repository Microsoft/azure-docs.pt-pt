---
title: Eliminar atores do Azure Service Fabric
description: Aprenda a eliminar manualmente e totalmente os atores fiáveis e o seu estado numa aplicação de Tecido de Serviço Azure.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: d977b6fea71d246610b0e6ef2d9e17d587f1e9ad
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253546"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminar o Reliable Actors e o estado
A recolha de lixo de atores desativados só limpa o objeto do ator, mas não remove dados que são armazenados no Diretor de Estado de um ator. Quando um ator é reativado, os seus dados são novamente disponibilizados através do Diretor de Estado. Nos casos em que os atores armazenam dados em Gerente de Estado e são desativados mas nunca reativados, pode ser necessário limpar os seus dados.

O [Serviço de Ator](service-fabric-reliable-actors-platform.md) proporciona uma função para eliminar os atores de um chamador remoto:

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

A eliminação de um ator tem os seguintes efeitos dependendo se o ator está ou não ativo:

* **Ator ativo**
  * O ator é removido da lista de atores ativos e está desativado.
  * O seu estado é apagado permanentemente.
* **Ator inativo**
  * O seu estado é apagado permanentemente.

Um ator não pode chamar a apagar-se de um dos seus métodos de ator porque o ator não pode ser apagado enquanto executa dentro de um contexto de chamada de ator, em que o tempo de execução obteve uma fechadura em torno da chamada do ator para impor o acesso de linha única.

Para obter mais informações sobre os Atores Fiáveis, leia o seguinte:
* [Temporizadores e lembretes do ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de atores](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do ator](/previous-versions/azure/dn971626(v=azure.100))
* [C# Código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
