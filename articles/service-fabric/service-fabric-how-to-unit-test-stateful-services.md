---
title: Desenvolver a testes de unidade para serviços com estado no Azure Service Fabric | Documentos da Microsoft
description: Aprenda a programar testes de unidade para os serviços do Service Fabric com monitorização de estado.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: 65fccada665743cf293b4ee4bb8f786a4c01c58a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093910"
---
# <a name="create-unit-tests-for-stateful-services"></a>Criar testes de unidade para serviços com estado
Serviços do Service Fabric com monitorização de estado de teste de unidade revela erros comuns que seriam não necessariamente detetados por aplicativo convencional ou teste de unidade específica do domínio. Durante o desenvolvimento de testes de unidade para serviços com estado, existem algumas considerações especiais que devam ser mantidas em mente.

1. Cada réplica executa o código de aplicativo, mas no contexto diferente. Se o serviço utiliza três réplicas, o código do serviço está em execução em três nós em paralelo em função/contexto diferente.
2. Estado armazenado no serviço com estado deve ser consistente entre todas as réplicas. O Gestor de estado e as coleções fiáveis irão fornecer essa consistência-de-pronta. No entanto, o estado na memória terá de ser gerido pelo código da aplicação.
3. Cada réplica será alterado funções em algum momento durante a execução no cluster. Uma réplica secundária se tornará um site primário, no caso do nó que aloja o principal fica indisponível ou sobrecarregado. Este é o comportamento natural para o Service Fabric, por conseguinte, tem de planear serviços para, eventualmente, executado numa função diferente.

Este artigo pressupõe que [teste de unidade serviços com estado no Service Fabric](service-fabric-concepts-unit-testing.md) foi lido.

## <a name="the-servicefabricmocks-library"></a>A biblioteca de ServiceFabric.Mocks
A partir da versão 3.3.0, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornece uma API para a simulação de orquestração de réplicas e a gestão de estado. Isso será usado nos exemplos.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks não é pertencentes à empresa ou mantido pela Microsoft. No entanto, esta é atualmente a biblioteca para serviços com estado de teste de unidade de recomendação da Microsoft.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurar o estado e a orquestração de imitação
Como parte da parte do dispor de um teste, definir uma réplica de simulação e Gerenciador de estado será criado. O conjunto de réplicas, em seguida, serão proprietários a criação de uma instância do serviço testado para cada réplica. Ele também possui eventos de ciclo de vida em execução, como `OnChangeRole` e `RunAsync`. O Gestor de estado fictícia serão Certifique-se a todas as operações executadas no Gestor de estado são executadas e mantidas como faria com o Gestor de estado real.

1. Crie um delegado de fábrica de serviço que irá criar a instância de serviço que está sendo testado. Isso deve ser semelhante ou mesmo como o retorno de chamada do service factory normalmente encontrados na `Program.cs` para um serviço do Service Fabric ou o ator. Isso deve seguir a seguinte assinatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Criar uma instância do `MockReliableStateManager` classe. Isto irá simular todas as interações com o Gestor de estado.
3. Criar uma instância do `MockStatefulServiceReplicaSet<TStatefulService>` onde `TStatefulService` é o tipo de serviço que está sendo testado. Isso exigirá o delegado que criou no passo #1 e o Gestor de estado instanciado no #2
4. Adicione réplicas ao conjunto de réplicas. Especificar a função (por exemplo, o servidor primário, ActiveSecondary, IdleSecondary) e o ID da réplica
   > Manter a réplica IDs! Eles provavelmente serão utilizados durante o act e partes de um teste de unidade de declaração.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Executar pedidos de serviço
Pedidos de serviço podem ser executados numa réplica específica usando as propriedades de conveniência e pesquisas.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Executar uma mudança de serviço
O conjunto de réplicas fictícios expõe vários métodos de conveniência para acionar a diferentes tipos de movimentações de serviço.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Juntar tudo
O teste seguinte demonstra como configurar um conjunto de réplicas de três nós e verificar que os dados estão disponíveis a partir de uma secundária após uma alteração de função. Um problema típico, isso pode capturar é se os dados adicionados durante `InsertAsync` foi guardado para algo na memória ou para uma coleção fiável sem executar `CommitAsync`. Em ambos os casos, seria dessincronizado com o principal secundário. Isso poderia levar a respostas inconsistentes depois de serviço ser movida.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
Saiba como testar [comunicação de serviço para serviço](service-fabric-testability-scenarios-service-communication.md) e [simular falhas com chaos controlado](service-fabric-controlled-chaos.md).
