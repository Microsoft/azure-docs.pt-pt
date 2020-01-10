---
title: Simular falhas nos microserviços do Azure
description: Este artigo fala sobre as ações de possibilidade de teste encontradas em Microsoft Azure Service Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465581"
---
# <a name="testability-actions"></a>Ações de possibilidade de teste
Para simular uma infraestrutura não confiável, o Azure Service Fabric fornece a você, o desenvolvedor, maneiras de simular várias falhas do mundo real e transições de estado. Elas são expostas como ações de possibilidade de teste. As ações são as APIs de nível inferior que causam uma injeção de falha específica, transição de estado ou validação. Ao combinar essas ações, você pode escrever cenários de teste abrangentes para seus serviços.

Service Fabric fornece alguns cenários de teste comuns compostos por essas ações. É altamente recomendável que você utilize esses cenários internos, que são cuidadosamente escolhidos para testar transições de Estado comum e casos de falha. No entanto, as ações podem ser usadas para criar cenários de teste personalizados quando você quiser adicionar cobertura para cenários que ainda não são cobertos pelos cenários internos ou personalizados adaptados para seu aplicativo.

C#as implementações das ações são encontradas no assembly System. Fabric. dll. O módulo System Fabric PowerShell é encontrado no assembly Microsoft. Microsoft. Microsoft. PowerShell. dll. Como parte da instalação do tempo de execução, o módulo do PowerShell do infabric é instalado para permitir a facilidade de uso.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Ações de falha normais versus não de carência
As ações de possibilidade de teste são classificadas em dois buckets principais:

* Falhas de não-cortesia: essas falhas simulam falhas, como reinicializações de máquina e falhas de processo. Nesses casos de falhas, o contexto de execução do processo é interrompido abruptamente. Isso significa que nenhuma limpeza do estado pode ser executada antes de o aplicativo ser iniciado novamente.
* Falhas normais: essas falhas simulam ações normais, como movimentações e quedas de réplica disparadas pelo balanceamento de carga. Nesses casos, o serviço recebe uma notificação do fechamento e pode limpar o estado antes de sair.

Para obter uma melhor validação de qualidade, execute a carga de trabalho de serviço e de negócios enquanto induzir várias falhas normais e não de carência. Falhas inesperadas do exercício situações em que o processo de serviço é encerrado abruptamente no meio de algum fluxo de trabalho. Isso testará o caminho de recuperação depois que a réplica de serviço for restaurada por Service Fabric. Isso ajudará a testar a consistência dos dados e se o estado do serviço será mantido corretamente após falhas. O outro conjunto de falhas (as falhas normais) testa se o serviço reage corretamente às réplicas que estão sendo movidas por Service Fabric. Isso testa o tratamento de cancelamento no método RunAsync. O serviço precisa verificar se o token de cancelamento está sendo definido, salvar corretamente seu estado e sair do método RunAsync.

## <a name="testability-actions-list"></a>Lista de ações de possibilidade de teste
| Ação | Descrição | API gerenciada | Cmdlet do PowerShell | Falhas normais/não de carência |
| --- | --- | --- | --- | --- |
| CleanTestState |Remove todo o estado do teste do cluster em caso de um desligamento inadequado do driver de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| InvokeDataLoss |Induzi a perda de dados em uma partição de serviço. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Pertinente |
| InvokeQuorumLoss |Coloca uma determinada partição de serviço com estado na perda de quorum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Pertinente |
| MovePrimary |Move a réplica primária especificada de um serviço com estado para o nó de cluster especificado. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Pertinente |
| MoveSecondary |Move a réplica secundária atual de um serviço com estado para um nó de cluster diferente. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Pertinente |
| RemoveReplica |Simula uma falha de réplica removendo uma réplica de um cluster. Isso fechará a réplica e a passará para a função ' none ', removendo todo o seu estado do cluster. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Pertinente |
| RestartDeployedCodePackage |Simula uma falha de processo de pacote de códigos reiniciando um pacote de códigos implantado em um nó em um cluster. Isso anula o processo do pacote de códigos, que reiniciará todas as réplicas de serviço do usuário hospedadas nesse processo. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Anormais |
| RestartNode |Simula uma falha de nó de Cluster Service Fabric reiniciando um nó. |RestartNodeAsync |Restart-ServiceFabricNode |Anormais |
| RestartPartition |Simula um cenário de blecaute de Datacenter ou de indisponibilidade de cluster reiniciando algumas ou todas as réplicas de uma partição. |RestartPartitionAsync |Restart-ServiceFabricPartition |Pertinente |
| RestartReplica |Simula uma falha de réplica reiniciando uma réplica persistente em um cluster, fechando a réplica e, em seguida, reabrindo-a. |RestartReplicaAsync |Restart-ServiceFabricReplica |Pertinente |
| StartNode |Inicia um nó em um cluster que já está parado. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha de nó interrompendo um nó em um cluster. O nó ficará inoperante até que o StartNode seja chamado. |StopNodeAsync |Stop-ServiceFabricNode |Anormais |
| ValidateApplication |Valida a disponibilidade e a integridade de todos os serviços de Service Fabric em um aplicativo, geralmente depois de induzir alguma falha no sistema. |ValidateApplicationAsync |Test-ServiceFabricApplication |Não aplicável |
| ValidateService |Valida a disponibilidade e a integridade de um serviço de Service Fabric, geralmente depois de induzir alguma falha no sistema. |ValidateServiceAsync |Test-ServiceFabricService |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executando uma ação de possibilidade de teste usando o PowerShell
Este tutorial mostra como executar uma ação de possibilidade de teste usando o PowerShell. Você aprenderá a executar uma ação de possibilidade de teste em um cluster local (uma caixa) ou em um cluster do Azure. Microsoft. Fabric. PowerShell. dll--o módulo Service Fabric PowerShell – é instalado automaticamente quando você instala o MSI do Microsoft Service Fabric. O módulo é carregado automaticamente quando você abre um prompt do PowerShell.

Segmentos do tutorial:

* [Executar uma ação em um cluster de uma caixa](#run-an-action-against-a-one-box-cluster)
* [Executar uma ação em um cluster do Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação em um cluster de uma caixa
Para executar uma ação de possibilidade de teste em um cluster local, primeiro conecte-se ao cluster e abra o prompt do PowerShell no modo de administrador. Vamos examinar a ação **Restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **Restart-ServiceFabricNode** está sendo executada em um nó chamado "Node1". O modo de conclusão especifica que ele não deve verificar se a ação Restart-node foi realmente bem-sucedida. A especificação do modo de conclusão como "Verify" fará com que ele verifique se a ação de reinicialização realmente teve êxito. Em vez de especificar diretamente o nó por seu nome, você pode especificá-lo por meio de uma chave de partição e o tipo de réplica, da seguinte maneira:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** deve ser usado para reiniciar um nó de Service Fabric em um cluster. Isso interromperá o processo Fabric. exe, que reiniciará todas as réplicas do serviço do sistema e do serviço de usuário hospedadas nesse nó. Usar essa API para testar seu serviço ajuda a descobrir bugs nos caminhos de recuperação de failover. Ele ajuda a simular falhas de nó no cluster.

A captura de tela a seguir mostra o comando **Restart-ServiceFabricNode** de teste em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída do primeiro **Get-ServiceFabricNode** (um cmdlet do módulo Service Fabric PowerShell) mostra que o cluster local tem cinco nós: node. 1 para node. 5. Depois que a ação de possibilidade de teste (cmdlet) **Restart-ServiceFabricNode** for executada no nó, chamado node. 4, vemos que o tempo de atividade do nó foi redefinido.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação em um cluster do Azure
A execução de uma ação de possibilidade de teste (usando o PowerShell) em um cluster do Azure é semelhante à execução da ação em um cluster local. A única diferença é que, antes de executar a ação, em vez de conectar-se ao cluster local, você precisa se conectar ao cluster do Azure primeiro.

## <a name="running-a-testability-action-using-c35"></a>Executando uma ação de possibilidade de teste usando C&#35;
Para executar uma ação de possibilidade de teste C#usando o, primeiro você precisa se conectar ao cluster usando o FabricClient. Em seguida, obtenha os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser usados para executar a mesma ação.
Observando a ação RestartServiceFabricNode, uma maneira de executá-la é usando as informações de nó (nome do nó e ID da instância do nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

* **Completemode** especifica que o modo não deve verificar se a ação de reinicialização realmente foi bem-sucedida. A especificação do modo de conclusão como "Verify" fará com que ele verifique se a ação de reinicialização realmente teve êxito.  
* **OperationTimeout** define a quantidade de tempo para a operação ser concluída antes que uma exceção TimeoutException seja lançada.
* **CancellationToken** permite que uma chamada pendente seja cancelada.

Em vez de especificar diretamente o nó por seu nome, você pode especificá-lo por meio de uma chave de partição e o tipo de réplica.

Para obter mais informações, consulte PartitionSelector e ReplicaSelector.

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
PartitionSelector é um auxiliar exposto na capacidade de teste e é usado para selecionar uma partição específica na qual executar qualquer uma das ações de possibilidade de teste. Ele pode ser usado para selecionar uma partição específica se a ID da partição for conhecida com antecedência. Ou você pode fornecer a chave de partição e a operação resolverá a ID da partição internamente. Você também tem a opção de selecionar uma partição aleatória.

Para usar esse auxiliar, crie o objeto PartitionSelector e selecione a partição usando um dos métodos Select *. Em seguida, passe o objeto PartitionSelector para a API que o exige. Se nenhuma opção for selecionada, o padrão será uma partição aleatória.

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
ReplicaSelector é um auxiliar exposto na capacidade de teste e é usado para ajudar a selecionar uma réplica na qual executar qualquer uma das ações de possibilidade de teste. Ele pode ser usado para selecionar uma réplica específica se a ID da réplica for conhecida com antecedência. Além disso, você tem a opção de selecionar uma réplica primária ou uma secundária aleatória. ReplicaSelector deriva de PartitionSelector, portanto, você precisa selecionar a réplica e a partição na qual deseja executar a operação de possibilidade de teste.

Para usar esse auxiliar, crie um objeto ReplicaSelector e defina como deseja selecionar a réplica e a partição. Em seguida, você pode passá-lo para a API que o exige. Se nenhuma opção for selecionada, o padrão será uma réplica aleatória e uma partição aleatória.

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
* [Cenários de possibilidade de teste](service-fabric-testability-scenarios.md)
* Como testar seu serviço
  * [Simular falhas durante cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)

