---
title: Desenvolver testes unitários para serviços estatais
description: Saiba mais sobre os testes unitários em Azure Service Fabric para serviços imponentes, e considerações especiais a ter em mente durante o desenvolvimento.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639841"
---
# <a name="create-unit-tests-for-stateful-services"></a>Criar testes unitários para Serviços Estatais
Serviço de teste de unidade Serviço Os serviços audagantes de supõem erros comuns que não seriam necessariamente apanhados por aplicação convencional ou testes de unidade específicos de domínio. Ao desenvolver testes unitários para serviços estatais, existem algumas considerações especiais que devem ser tidas em conta.

1. Cada réplica executa o código de aplicação, mas em contexto diferente. Se o serviço utilizar três réplicas, o código de serviço está a executar três nós em paralelo sob diferentes contextos/funções.
2. O Estado armazenado dentro do serviço de estado deve ser consistente entre todas as réplicas. O gerente do Estado e coleções fiáveis fornecerão esta consistência fora da caixa. No entanto, o estado de memória terá de ser gerido pelo código de aplicação.
3. Cada réplica mudará de funções em algum momento enquanto corre no cluster. Uma réplica secundária tornar-se-á uma primária, caso o nó que acolhe a primária fique indisponível ou sobrecarregado. Este é um comportamento natural para o Tecido de Serviço, pelo que os serviços devem planear a execução eventualmente sob um papel diferente.

Este artigo pressupõe que a Unidade de Testes de [Serviços De Serviço saem de serviço.](service-fabric-concepts-unit-testing.md)

## <a name="the-servicefabricmocks-library"></a>A biblioteca ServiceFabric.Mocks
A partir da versão 3.3.0, o [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornece uma API para ridicularizar tanto a orquestração das réplicas como a gestão do Estado. Isto será usado nos exemplos.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks não é propriedade ou mantido pela Microsoft. No entanto, esta é atualmente a biblioteca recomendada pela Microsoft para testar serviços estatais de teste de unidade.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurar a orquestração falsa e o estado
Como parte da parte organizada de um teste, será criado um conjunto de réplicas falsas e gestor de estado. O conjunto de réplicas será então o dono da criação de uma instância do serviço testado para cada réplica. Será também proprietária de eventos `OnChangeRole` de `RunAsync`ciclo de vida como e . O gestor do Estado simulado assegurará que quaisquer operações realizadas contra o gerente do Estado sejam executadas e mantidas como o atual gerente do Estado.

1. Crie um delegado de fábrica de serviço que instantaneamente o serviço que está a ser testado. Isto deve ser semelhante ou o mesmo que `Program.cs` o callback da fábrica de serviço normalmente encontrado para um serviço de Tecido de Serviço ou ator. Isto deve seguir a seguinte assinatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Criar um `MockReliableStateManager` exemplo de classe. Isto vai gozar com todas as interações com o gerente do estado.
3. Crie uma `MockStatefulServiceReplicaSet<TStatefulService>` `TStatefulService` instância de onde está o tipo de serviço que está a ser testado. Isto exigirá que o delegado criado em passo #1 e o gerente do Estado instantaneamente em #2
4. Adicione réplicas ao conjunto de réplicas. Especificar a função (como primário, ativo secundário, idlesecondary) e a identificação da réplica
   > Agarra-te às réplicas! Estes provavelmente serão utilizados durante o ato e afirmam partes de um teste de unidade.

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
Os pedidos de serviço podem ser executados numa réplica específica utilizando as propriedades de conveniência e as aparências.
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

## <a name="execute-a-service-move"></a>Executar um movimento de serviço
O conjunto de réplicas falsas expõe vários métodos de conveniência para desencadear diferentes tipos de movimentos de serviço.
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
O teste seguinte demonstra a criação de um conjunto de réplicas de três nósos e a verificação de que os dados estão disponíveis a partir de um secundário após uma mudança de função. Um problema típico que isto pode `InsertAsync` apanhar é se os dados adicionados durante `CommitAsync`foi guardado para algo na memória ou para uma recolha fiável sem correr . Em qualquer dos casos, o secundário estaria dessincronizado com as primárias. Isto levaria a respostas inconsistentes após movimentos de serviço.

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
Aprenda a testar a [comunicação serviço-a-serviço](service-fabric-testability-scenarios-service-communication.md) e [simular falhas usando o caos controlado](service-fabric-controlled-chaos.md).
