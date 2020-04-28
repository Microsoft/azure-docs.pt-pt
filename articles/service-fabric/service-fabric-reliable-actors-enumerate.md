---
title: Atores enumerados no Tecido de Serviço Azure
description: Saiba mais sobre a enumeração de Atores Fiáveis e seus metadados numa aplicação Azure Service Fabric usando exemplos.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645604"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Atores fiáveis de tecido de serviço enumerado
O serviço Reliable Actors permite que um cliente enumere metadados sobre os atores que o serviço está a hospedar. Como o serviço do ator é um serviço de estado dividido, a enumeração é realizada por partição. Como cada partição pode conter muitos atores, a enumeração é devolvida como um conjunto de resultados paged. As páginas estão em loop até que todas as páginas sejam lidas. O exemplo que se segue mostra como criar uma lista de todos os atores ativos numa das divisórias de um serviço de ator:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Passos seguintes
* [Gestão do Estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e recolha de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de amostra .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código da amostra java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
