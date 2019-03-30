---
title: Simular falhas em microsserviços do Azure | Documentos da Microsoft
description: Este artigo fala sobre as ações de capacidade de teste encontradas no Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: motanv
manager: chackdan
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 37a794387f3a2f02124805705d380ad9f1fc1270
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662861"
---
# <a name="testability-actions"></a>Ações do Testability
Para simular uma infraestrutura confiável, o Azure Service Fabric fornece, o desenvolvedor, com formas de simular várias falhas de mundo real e transições de estado. Eles são expostos como ações de capacidade de teste. As ações são as APIs de baixo nível que fazem com que uma injeção de falha específico, a transição de estado ou a validação. Ao combinar essas ações, pode escrever os cenários de teste abrangente para os seus serviços.

O Service Fabric fornece que alguns cenários comuns de teste é composto por estas ações. Recomendamos vivamente que utilize estes cenários incorporados, que são cuidadosamente escolhidos para testar as transições de estado comuns e casos de falha. No entanto, as ações podem ser utilizadas para criar cenários de teste personalizada quando deseja adicionar cobertura para cenários que não são abrangidos pelos cenários incorporados ainda ou que são personalizados adaptados para a sua aplicação.

C#implementações das ações são encontradas no System.Fabric.dll assembly. O módulo do PowerShell de recursos de infraestrutura do sistema é encontrado na assemblagem Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação de tempo de execução, o módulo do ServiceFabric PowerShell é instalado para permitir a facilidade de utilização.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Anulações normais do vs. ações de falha inesperado
Ações de capacidade de teste são classificadas em dois grupos principais:

* Falhas inesperadas: Estas falhas simular falhas com reinícios da máquina e processam falhas. Em tais casos de falhas, o contexto de execução do processo será interrompido abruptamente. Isso significa que nenhum limpeza do Estado pode ser executado antes do aplicativo é iniciado novamente.
* Falhas de anulações normais: Estas falhas simular ações anulações normais como movimentações de réplica e remoções acionadas pelo balanceamento de carga. Nesses casos, o serviço recebe uma notificação de fechamento e pode limpar o estado antes de sair.

Para a validação de melhor qualidade, execute a carga de trabalho empresarial e serviço ao induzindo várias falhas amigável e inesperadas. Falhas inesperadas exercitem cenários em que o processo de serviço sai abruptamente no meio de um fluxo de trabalho. Esta ação testa o caminho de recuperação depois da réplica de serviço é restaurada ao Service Fabric. Isto ajudará a testar a consistência dos dados e se o estado do serviço é mantido corretamente após falhas. O outro conjunto de teste de falhas (as falhas de anulações normais) que o serviço corretamente reage a réplicas que está a ser movidas pelo Service Fabric. Esta ação testa o processamento de cancelamento no método RunAsync. O serviço precisa verificar o token de cancelamento que está a ser definido, corretamente salvar seu estado e o método RunAsync de saída.

## <a name="testability-actions-list"></a>Lista de ações de capacidade de teste
| Ação | Descrição | API gerida | Cmdlet do PowerShell | Falhas de anulações normais/inesperado |
| --- | --- | --- | --- | --- |
| CleanTestState |Remove todos os Estados de teste do cluster em caso de um encerramento incorreto do controlador de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| InvokeDataLoss |Induces perda de dados numa partição de serviço. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Anulações normais |
| InvokeQuorumLoss |Coloca uma partição de serviço com estado determinado a perda de quórum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Anulações normais |
| MovePrimary |Move a réplica primária especificada de um serviço com monitorização de estado para o nó de cluster especificado. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Anulações normais |
| MoveSecondary |Move a réplica secundária atual de um serviço com monitorização de estado para um nó de cluster diferente. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Anulações normais |
| RemoveReplica |Simula uma falha de réplica ao remover uma réplica de um cluster. Isto irá fechar a réplica e irão transitar para a função 'None', remover todo seu estado do cluster. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Anulações normais |
| RestartDeployedCodePackage |Simula uma falha de processo do pacote de código ao reiniciar um pacote de código implementado num nó num cluster. Isso anulará o processo de pacote de código, que irá reiniciar todas as réplicas de serviço de utilizador alojadas nesse processo. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Inesperado |
| RestartNode |Simula uma falha de nó de cluster do Service Fabric reiniciando um nó. |RestartNodeAsync |Restart-ServiceFabricNode |Inesperado |
| RestartPartition |Simula um cenário de indisponibilidade de indisponibilidade ou cluster datacenter reiniciando algumas ou todas as réplicas de uma partição. |RestartPartitionAsync |Restart-ServiceFabricPartition |Anulações normais |
| RestartReplica |Simula uma falha de réplica ao reiniciar uma réplica persistente num cluster, a réplica a fechar e, em seguida, abri-lo. |RestartReplicaAsync |Restart-ServiceFabricReplica |Anulações normais |
| StartNode |Inicia um nó num cluster que já está parado. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha de nó, parando um nó num cluster. O nó permanecerá inativo até que seja chamado StartNode. |StopNodeAsync |Stop-ServiceFabricNode |Inesperado |
| ValidateApplication |Valida a disponibilidade e o estado de funcionamento de todos os serviços do Service Fabric dentro de um aplicativo, normalmente, depois de induzir algumas falhas no sistema. |ValidateApplicationAsync |Test-ServiceFabricApplication |Não aplicável |
| ValidateService |Valida a disponibilidade e o estado de funcionamento de um serviço do Service Fabric, normalmente, depois de induzir algumas falhas no sistema. |ValidateServiceAsync |Test-ServiceFabricService |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executar uma ação de capacidade de teste com o PowerShell
Este tutorial mostra como executar uma ação de capacidade de teste com o PowerShell. Aprenderá como executar uma ação de capacidade de teste em relação a um cluster (one box) local ou num cluster do Azure. Microsoft.Fabric.Powershell.dll – o módulo do PowerShell do Service Fabric é instalado automaticamente ao instalar o MSI de recursos de infraestrutura do serviço de Microsoft. O módulo é carregado automaticamente ao abrir uma linha de comandos do PowerShell.

Tutoriais segmentos:

* [Executar uma ação em relação a um cluster de one box](#run-an-action-against-a-one-box-cluster)
* [Executar uma ação em relação a um cluster do Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação em relação a um cluster de one box
Para executar uma ação de capacidade de teste em relação a um cluster local, ligue ao cluster e abra a linha de comandos do PowerShell no modo de administrador. Vamos examinar os **reinício-ServiceFabricNode** ação.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **reinício-ServiceFabricNode** está em execução num nó com o nome "Node1". O modo de conclusão Especifica que ele deve não verificar se a ação de reinício do nó, na verdade, foi bem-sucedida. Especificar o modo de conclusão como "Verificar" fará com que ele verificar se a ação de reinício, na verdade, foi bem-sucedida. Em vez de especificar diretamente o nó pelo respetivo nome, pode especificá-lo por meio de uma chave de partição e o tipo de réplica, da seguinte forma:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reinício-ServiceFabricNode** deve ser usado para reiniciar um nó do Service Fabric num cluster. Isto irá parar o processo de Fabric.exe, que reiniciará todos os serviço e usuário serviço as réplicas de sistema alojadas nesse nó. Usando essa API para testar o seu serviço ajuda a descobrir bugs juntamente com os caminhos de recuperação de ativação pós-falha. Ele ajuda a simular falhas de nó do cluster.

A captura de ecrã a seguir mostra a **reinício-ServiceFabricNode** comando de capacidade de teste em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída da primeira **Get-ServiceFabricNode** (um cmdlet do módulo PowerShell do Service Fabric) mostra que o cluster local com cinco nós: NODE.1 para Node.5. Após a ação de capacidade de teste (cmdlet) **reinício-ServiceFabricNode** é executado no nó, com o nome Node.4, Vemos que o tempo de atividade do nó foi reposto.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação em relação a um cluster do Azure
Executar uma ação de capacidade de teste (ao utilizar o PowerShell) em relação a um cluster do Azure é semelhante ao executar a ação em relação a um cluster local. A única diferença é que antes de poder executar a ação, em vez de ligar ao local cluster, terá de ligar ao cluster do Azure pela primeira vez.

## <a name="running-a-testability-action-using-c35"></a>Executar uma ação de capacidade de teste com C&#35;
Para executar uma ação de capacidade de teste com o C#, primeiro tem de ligar ao cluster com FabricClient. Em seguida, obter os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser utilizados para executar a mesma ação.
Olhando para a ação de RestartServiceFabricNode, é uma forma de executá-lo usando as informações de nó (nome do nó e o ID de instância de nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

* **CompleteMode** Especifica que o modo de não deve verificar se a ação de reinício, na verdade, foi bem-sucedida. Especificar o modo de conclusão como "Verificar" fará com que ele verificar se a ação de reinício, na verdade, foi bem-sucedida.  
* **OperationTimeout** define o período de tempo para a operação ser concluídas antes de uma TimeoutException exceção é lançada.
* **CancellationToken** permite que uma chamada pendente para ser cancelada.

Em vez de especificar diretamente o nó pelo respetivo nome, pode especificá-lo por meio de uma chave de partição e o tipo de réplica.

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
PartitionSelector é um auxiliar exposto na capacidade de teste e é utilizado para selecionar uma partição específica para efetuar qualquer uma das ações de capacidade de teste. Ele pode ser usado para selecionar uma partição específica, se o ID de partição é conhecido antecipadamente. Em alternativa, pode fornecer a chave de partição e a operação irá resolver o ID de partição internamente. Tem também a opção de selecionar uma partição aleatória.

Para utilizar esse auxiliar, criar o objeto de PartitionSelector e selecione a partição com um dos métodos de Select *. Em seguida, passe no objeto PartitionSelector para a API que precise dela. Se nenhuma opção for selecionada, é assumida como predefinição a uma partição aleatória.

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
ReplicaSelector é um auxiliar exposto na capacidade de teste e é utilizado para ajudar a selecionar uma réplica no qual pretende efetuar qualquer uma das ações de capacidade de teste. Ele pode ser usado para selecionar uma réplica específica se o ID de réplica é conhecido antecipadamente. Além disso, terá a opção de selecionar uma réplica primária ou uma secundária aleatória. ReplicaSelector deriva de PartitionSelector, por isso terá de selecionar a réplica e a partição no qual pretende efetuar a operação de capacidade de teste.

Para utilizar esse auxiliar, crie um objeto de ReplicaSelector e defina a forma como pretende selecionar a réplica e a partição. Pode, em seguida, passá-lo a API que precise dela. Se nenhuma opção for selecionada, é assumida como predefinição para uma réplica aleatória e a partição aleatória.

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

## <a name="next-steps"></a>Passos Seguintes
* [Cenários do Testability](service-fabric-testability-scenarios.md)
* Como testar o seu serviço
  * [Simular falhas durante cargas de trabalho do serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação de serviço a serviço](service-fabric-testability-scenarios-service-communication.md)

