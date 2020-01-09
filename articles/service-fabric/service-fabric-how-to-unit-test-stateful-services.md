---
title: Desenvolver testes de unidade para serviços com estado
description: Saiba mais sobre os testes de unidade no Azure Service Fabric para serviços com estado e considerações especiais para ter em mente durante o desenvolvimento.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639841"
---
# <a name="create-unit-tests-for-stateful-services"></a>Criar testes de unidade para serviços com estado
O teste de unidade Service Fabric serviços com Estado descobre erros comuns que não seriam necessariamente detectados pelo aplicativo convencional ou pelo teste de unidade específico de domínio. Ao desenvolver testes de unidade para serviços com estado, há algumas considerações especiais que devem ser mantidas em mente.

1. Cada réplica executa o código do aplicativo, mas sob contexto diferente. Se o serviço usar três réplicas, o código de serviço será executado em três nós em paralelo sob contexto/função diferente.
2. O estado armazenado no serviço com estado deve ser consistente entre todas as réplicas. O Gerenciador de estado e as coleções confiáveis fornecerão essa consistência pronta para uso. No entanto, o estado na memória precisará ser gerenciado pelo código do aplicativo.
3. Cada réplica alterará as funções em algum momento durante a execução no cluster. Uma réplica secundária se tornará primária, caso o nó que hospeda o primário fique indisponível ou sobrecarregado. Esse é um comportamento natural para Service Fabric, portanto, os serviços devem planejar eventualmente em execução em uma função diferente.

Este artigo pressupõe que os [serviços com estado de teste de unidade no Service Fabric](service-fabric-concepts-unit-testing.md) foram lidos.

## <a name="the-servicefabricmocks-library"></a>A biblioteca do infabric. Mocks
A partir da versão 3.3.0, o [infabric. Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornece uma API para simular a orquestração das réplicas e o gerenciamento de estado. Isso será usado nos exemplos.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*O infabric. Mocks não é propriedade da Microsoft nem é mantida por ela. No entanto, essa é atualmente a biblioteca recomendada pela Microsoft para serviços com estado de teste de unidade.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurar a orquestração e o estado de simulação
Como parte da porção de organização de um teste, um conjunto de réplicas fictícios e um Gerenciador de estado serão criados. O conjunto de réplicas criará, então, uma instância do serviço testado para cada réplica. Ele também será responsável por executar eventos do ciclo de vida, como `OnChangeRole` e `RunAsync`. O Gerenciador de estado de simulação garantirá que todas as operações executadas em relação ao Gerenciador de estado sejam executadas e mantidas como o Gerenciador de estado real.

1. Crie um representante do Service Factory que criará uma instância do serviço que está sendo testado. Isso deve ser semelhante ou igual ao retorno de chamada do Service Factory normalmente encontrado em `Program.cs` para um serviço ou ator Service Fabric. Isso deve seguir a seguinte assinatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Crie uma instância da classe `MockReliableStateManager`. Isso simulará todas as interações com o Gerenciador de estado.
3. Crie uma instância do `MockStatefulServiceReplicaSet<TStatefulService>` em que `TStatefulService` é o tipo do serviço que está sendo testado. Isso exigirá o delegado criado na etapa #1 e o Gerenciador de estado instanciado em #2
4. Adicione réplicas ao conjunto de réplicas. Especifique a função (como Primary, ActiveSecondary, IdleSecondary) e a ID da réplica
   > Mantenha-se ligado às IDs de réplica! Eles provavelmente serão usados durante as partes Act e Assert de um teste de unidade.

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

## <a name="execute-service-requests"></a>Executar solicitações de serviço
As solicitações de serviço podem ser executadas em uma réplica específica usando as propriedades de conveniência e as pesquisas.
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

## <a name="execute-a-service-move"></a>Executar uma movimentação de serviço
O conjunto de réplicas fictícios expõe vários métodos de conveniência para disparar diferentes tipos de movimentação de serviço.
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
O teste a seguir demonstra como configurar um conjunto de réplicas de três nós e verificar se os dados estão disponíveis de um secundário após uma alteração de função. Um problema típico que isso pode detectar é se os dados adicionados durante a `InsertAsync` foram salvos em algo na memória ou em uma coleção confiável sem executar `CommitAsync`. Em ambos os casos, o secundário estaria fora de sincronia com o primário. Isso levaria a respostas inconsistentes após a movimentação do serviço.

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

## <a name="next-steps"></a>Passos seguintes
Saiba como testar a [comunicação](service-fabric-testability-scenarios-service-communication.md) entre serviços e [simular falhas usando o caos controlado](service-fabric-controlled-chaos.md).
