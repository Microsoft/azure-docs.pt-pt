---
title: Simular falhas em microserviços Azure
description: Este artigo fala sobre as ações de testability encontradas no Microsoft Azure Service Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.custom: devx-track-csharp
ms.openlocfilehash: 8b1d4ae42fa033c03bd82ae5cee5794d98c23c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022178"
---
# <a name="testability-actions"></a>Ações de testability
De forma a simular uma infraestrutura pouco fiável, a Azure Service Fabric fornece-lhe, o desenvolvedor, formas de simular várias falhas no mundo real e transições estatais. Estas são expostas como ações de testabilidade. As ações são as APIs de baixo nível que causam uma injeção específica de falhas, transição do estado ou validação. Ao combinar estas ações, pode escrever cenários de teste abrangentes para os seus serviços.

O Service Fabric fornece alguns cenários de teste comuns compostos por estas ações. Recomendamos vivamente que utilize estes cenários incorporados, que são cuidadosamente escolhidos para testar transições de estado comuns e casos de insucesso. No entanto, as ações podem ser usadas para criar cenários de teste personalizados quando pretende adicionar cobertura para cenários que ainda não estão cobertos pelos cenários incorporados ou que são personalizados para a sua aplicação.

C# As implementações das ações encontram-se na assembleia System.Fabric.dll. O módulo PowerShell do tecido do sistema encontra-se no conjunto Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação de tempo de execução, o módulo ServiceFabric PowerShell é instalado para permitir uma facilidade de utilização.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Graciosa vs. ações de falha ingraciful
As ações de testability são classificadas em dois grandes baldes:

* Falhas ingraças: Estas falhas simulam falhas como o recomeço da máquina e as falhas de processo. Nestes casos de falhas, o contexto de execução do processo para abruptamente. Isto significa que nenhuma limpeza do estado pode ser executada antes que a aplicação recomeça.
* Falhas graciosas: Estas falhas simulam ações graciosas como movimentos de réplica e gotas desencadeadas pelo equilíbrio de carga. Nesses casos, o serviço recebe uma notificação do fecho e pode limpar o estado antes de sair.

Para uma melhor validação de qualidade, gere o serviço e a carga de trabalho do negócio, ao mesmo tempo que induz várias falhas graciosas e imperiosas. Falhas imperiosas exercitam cenários em que o processo de serviço sai abruptamente no meio de algum fluxo de trabalho. Isto testa o caminho de recuperação assim que a réplica de serviço é restaurada pela Service Fabric. Isto ajudará a testar a consistência dos dados e se o estado de serviço é mantido corretamente após falhas. O outro conjunto de falhas (as graciosas falhas) testa que o serviço reage corretamente às réplicas que estão a ser movidas pela Service Fabric. Este teste de manuseamento do cancelamento no método RunAsync. O serviço precisa verificar se o token de cancelamento está sendo definido, salvar corretamente o seu estado, e sair do método RunAsync.

## <a name="testability-actions-list"></a>Lista de ações de testability
| Ação | Descrição | API gerida | Cmdlet PowerShell | Falhas graciosas/imperiosas |
| --- | --- | --- | --- | --- |
| Estado de Limpeza |Remove todo o estado de teste do cluster em caso de paragem do controlador de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| InvocarDataLoss |Induz a perda de dados numa divisória de serviço. |InvocarDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Gracioso |
| InvocarQuorumLoss |Coloca uma partição de serviço imponente em perda de quórum. |InvocarQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Gracioso |
| MovePrimary |Move a réplica primária especificada de um serviço stateful para o nó de cluster especificado. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Gracioso |
| MoveSecondary |Move a réplica secundária atual de um serviço stateful para um nó de cluster diferente. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Gracioso |
| Remover AReplica |Simula uma falha de réplica removendo uma réplica de um cluster. Isto vai fechar a réplica e vai transferi-la para o papel 'Nenhum', retirando todo o seu estado do cluster. |RemoverReplicaAsync |Remove-ServiceFabricReplica |Gracioso |
| ReiniciarDeployedCodePackage |Simula uma falha do processo do pacote de código reiniciando um pacote de código implantado num nó num cluster. Isto aborta o processo de pacote de código, que reiniciará todas as réplicas do serviço de utilizador hospedadas nesse processo. |ReiniciarDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Imperdoativo |
| ReinícioNode |Simula uma falha do nó do nó do conjunto de tecido de serviço reiniciando um nó. |RestartNodeAsync |Restart-ServiceFabricNode |Imperdoativo |
| Reinício DaPartição |Simula um apagão do datacenter ou um cenário de apagão de cluster reiniciando algumas ou todas as réplicas de uma partição. |RestartPartitionAsync |Restart-ServiceFabricPartition |Gracioso |
| RestartReplica |Simula uma falha de réplica reiniciando uma réplica persistiu num cluster, fechando a réplica e reabrindo-a. |RestartReplicaAsync |Restart-ServiceFabricReplica |Gracioso |
| InícioNode |Inicia um nó num aglomerado que já está parado. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha no nó parando um nó num aglomerado. O nó permanecerá no chão até que o StartNode seja chamado. |StopNodeAsync |Stop-ServiceFabricNode |Imperdoativo |
| ValidarApplicação |Valida a disponibilidade e a saúde de todos os serviços de Tecido de Serviço dentro de uma aplicação, normalmente depois de induzir alguma falha no sistema. |ValidarApplicationAsync |Test-ServiceFabricApplication |Não aplicável |
| Validar Serviço |Valida a disponibilidade e a saúde de um serviço de Tecido de Serviço, normalmente depois de induzir alguma falha no sistema. |Validar o ServiçoAsync |Test-ServiceFabricService |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executar uma ação de testability usando PowerShell
Este tutorial mostra-lhe como executar uma ação de testability usando PowerShell. Você aprenderá a executar uma ação de teste contra um cluster local (uma caixa) ou um cluster Azure. Microsoft.Fabric.Powershell.dll- o módulo PowerShell do tecido de serviço - é instalado automaticamente quando instala o Microsoft Service Fabric MSI. O módulo é carregado automaticamente quando abre uma solicitação PowerShell.

Segmentos tutoriais:

* [Executar uma ação contra um aglomerado de uma caixa](#run-an-action-against-a-one-box-cluster)
* [Executar uma ação contra um aglomerado de Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação contra um aglomerado de uma caixa
Para executar uma ação de teste contra um cluster local, ligue primeiro ao cluster e abra o pedido powerShell no modo de administrador. Vejamos a ação **Restart-ServiceFabricNode.**

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **Restart-ServiceFabricNode** está a ser executada num nó chamado "Node1". O modo de conclusão especifica que não deve verificar se a ação do nó de reinício foi efetivamente bem sucedida. Especificar o modo de conclusão como "Verificar" fará com que verifique se a ação de reinício foi realmente bem sucedida. Em vez de especificar diretamente o nó pelo seu nome, pode especi-lo através de uma chave de partição e do tipo de réplica, da seguinte forma:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reiniciar-ServiçoSFbricNode** deve ser utilizado para reiniciar um nó de tecido de serviço num cluster. Isto irá parar o processo Fabric.exe, que irá reiniciar todas as réplicas do serviço de sistema e do serviço de utilizador hospedados nesse nó. A utilização desta API para testar o seu serviço ajuda a descobrir bugs ao longo dos caminhos de recuperação de falhas. Ajuda a simular falhas de nó no cluster.

A imagem que se segue mostra o comando de testability **Restart-ServiceFabricNode** em ação.

![Screenshot de executar o comando Restart-ServiceFabricNode em PowerShell.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída do primeiro **Get-ServiceFabricNode** (um cmdlet do módulo PowerShell do tecido de serviço) mostra que o cluster local tem cinco nós: Nó.1 a Nó.5. Após a ação de teste (cmdlet) **Restart-ServiceFabricNode** é executado no nó, denominado Nó.4, vemos que o tempo de uptime do nó foi reiniciado.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação contra um aglomerado de Azure
Executar uma ação de testability (utilizando o PowerShell) contra um cluster Azure é semelhante a executar a ação contra um cluster local. A única diferença é que antes de poder executar a ação, em vez de se ligar ao cluster local, tem de se ligar primeiro ao cluster Azure.

## <a name="running-a-testability-action-using-c35"></a>Executar uma ação de testability utilizando C&#35;
Para executar uma ação de testability utilizando C#, primeiro tem de se ligar ao cluster utilizando o FabricClient. Em seguida, obtenha os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser usados para executar a mesma ação.
Olhando para a ação RestartServiceFabricNode, uma forma de executá-la é utilizando a informação do nó (nome de nó e iD do nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

* **CompleteMode** especifica que o modo não deve verificar se a ação de reinício realmente conseguiu. Especificar o modo de conclusão como "Verificar" fará com que verifique se a ação de reinício foi realmente bem sucedida.  
* **OperationTimeout** define o tempo para a operação terminar antes de uma exceção TimeoutException ser lançada.
* **CancelamentoToken** permite que uma chamada pendente seja cancelada.

Em vez de especificar diretamente o nó pelo seu nome, pode especi-lo através de uma chave de partição e do tipo de réplica.

Para mais informações, consulte PartitionSelector e ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector é um ajudante exposto na capacidade de teste e é usado para selecionar uma divisória específica para executar qualquer uma das ações de testability. Pode ser usado para selecionar uma partição específica se o ID de partição for conhecido previamente. Ou, pode fornecer a chave de partição e a operação resolverá o ID de partição internamente. Também tem a opção de selecionar uma divisória aleatória.

Para utilizar este ajudante, crie o objeto partitionSelector e selecione a partição utilizando um dos métodos Select*. Em seguida, passe no objeto do PartitionSelector para a API que o exija. Se não for selecionada nenhuma opção, é padrão para uma divisória aleatória.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector é um ajudante exposto na capacidade de teste e é usado para ajudar a selecionar uma réplica sobre a qual realizar qualquer uma das ações de testability. Pode ser usado para selecionar uma réplica específica se o ID da réplica for conhecido previamente. Além disso, tem a opção de selecionar uma réplica primária ou uma secundária aleatória. ReplicaSElector deriva do PartitionSelector, por isso é necessário selecionar tanto a réplica como a partição em que deseja realizar a operação de testabilidade.

Para utilizar este ajudante, crie um objeto ReplicaSelector e desfaseje a forma como pretende selecionar a réplica e a partição. Em seguida, pode passá-lo para a API que o requer. Se nenhuma opção for selecionada, é padrão para uma réplica aleatória e partição aleatória.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Passos seguintes
* [Cenários de testabilidade](service-fabric-testability-scenarios.md)
* Como testar o seu serviço
  * [Simular falhas durante as cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação de serviço-a-serviço](service-fabric-testability-scenarios-service-communication.md)

