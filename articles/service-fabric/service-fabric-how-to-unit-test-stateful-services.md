---
title: Desenvolver testes unitários para serviços estatais
description: Saiba mais sobre os testes de unidade no Azure Service Fabric para serviços estatais e considerações especiais a ter em mente durante o desenvolvimento.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89011400"
---
# <a name="create-unit-tests-for-stateful-services"></a>Criar testes unitários para serviços estatais
Os serviços de teste de serviço de serviço de unidades desvendam erros comuns que não seriam necessariamente apanhados por aplicações convencionais ou testes de unidade específicos de domínio. Ao desenvolver testes unitários para serviços estatais, existem algumas considerações especiais que devem ser mentedas.

1. Cada réplica executa o código de aplicação, mas em contexto diferente. Se o serviço utilizar três réplicas, o código de serviço está a executar três nós em paralelo sob diferente contexto/função.
2. O Estado armazenado dentro do serviço imponente deve ser consistente entre todas as réplicas. O gerente do Estado e as coleções fiáveis fornecerão esta consistência fora da caixa. No entanto, o estado de memória terá de ser gerido pelo código de aplicação.
3. Cada réplica mudará de funções em algum momento enquanto executa no cluster. Uma réplica secundária tornar-se-á primária, no caso de o nó que hospeda o principal ficar indisponível ou sobrecarregado. Este é um comportamento natural para o Service Fabric, pelo que os serviços devem planear a execução eventualmente sob um papel diferente.

Este artigo pressupõe que [a Unidade que testa serviços estatais em Tecido de Serviço](service-fabric-concepts-unit-testing.md) foi lida.

## <a name="the-servicefabricmocks-library"></a>A biblioteca ServiceFabric.Mocks
A partir da versão 3.3.0, [o ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornece uma API para ridicularizar tanto a orquestração das réplicas como a gestão do Estado. Isto será usado nos exemplos.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks não é propriedade ou mantido pela Microsoft. No entanto, esta é atualmente a biblioteca recomendada pela Microsoft para testar serviços estatais de testes.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurar a orquestração e o estado de simulação
Como parte da parte de um teste, será criado um conjunto de réplicas simulada e gestor de estado. O conjunto de réplicas será então dono da criação de uma instância do serviço testado para cada réplica. Será também proprietária de eventos de ciclo de vida como `OnChangeRole` e `RunAsync` . O gerente do Estado vai garantir que quaisquer operações realizadas contra o gerente do Estado sejam executadas e mantidas como o verdadeiro gerente do Estado.

1. Crie um delegado de fábrica de serviço que instantaneamente o serviço que está a ser testado. Isto deve ser semelhante ou igual ao retorno da fábrica de serviço normalmente encontrado `Program.cs` para um serviço de Tecido de Serviço ou ator. Isto deve seguir a seguinte assinatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Criar um exemplo de `MockReliableStateManager` classe. Isto vai gozar com todas as interações com o gerente do Estado.
3. Criar um caso de `MockStatefulServiceReplicaSet<TStatefulService>` onde está o tipo de serviço que está a ser `TStatefulService` testado. Isto exigirá que o delegado criado em #1 e o gerente do Estado instantaneamente em #2
4. Adicione réplicas ao conjunto de réplicas. Especificar o papel (como Primário, Secundário Ativo, Inativo) e o ID da réplica
   > Atenção às réplicas de IDs! Estes serão provavelmente utilizados durante o ato e afirmarão partes de um teste de unidade.

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
Os pedidos de serviço podem ser executados numa réplica específica usando as propriedades de conveniência e as suas procuras.
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
O teste seguinte demonstra a criação de um conjunto de réplica de três nós e a verificação de que os dados estão disponíveis a partir de um secundário após uma mudança de função. Um problema típico que isto pode apanhar é se os dados adicionados durante a `InsertAsync` qual foram guardados para algo na memória ou para uma recolha fiável sem correr `CommitAsync` . Em qualquer dos casos, o secundário estaria dessincronizado com as primárias. Isto levaria a respostas inconsistentes após os movimentos de serviço.

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
Aprenda a testar a [comunicação de serviço-a-serviço](service-fabric-testability-scenarios-service-communication.md) e [simular falhas utilizando o caos controlado](service-fabric-controlled-chaos.md).
