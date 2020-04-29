---
title: Simular falhas nos microserviços do Azure
description: Este artigo fala sobre as ações de testability encontradas no Microsoft Azure Service Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282045"
---
# <a name="testability-actions"></a>Ações de testabilidade
Para simular uma infraestrutura pouco fiável, o Azure Service Fabric fornece-lhe, o desenvolvedor, formas de simular várias falhas no mundo real e transições estatais. Estes são expostos como ações de testabilidade. As ações são as APIs de baixo nível que causam uma injeção específica de falhas, transição do Estado ou validação. Ao combinar estas ações, pode escrever cenários de teste abrangentes para os seus serviços.

O Service Fabric fornece alguns cenários comuns de teste compostos por estas ações. Recomendamos vivamente que utilize estes cenários incorporados, que são cuidadosamente escolhidos para testar transições de estado comum e casos de falha. No entanto, as ações podem ser usadas para criar cenários de teste personalizados quando você quer adicionar cobertura para cenários que ainda não estão cobertos pelos cenários incorporados ou que são personalizados para a sua aplicação.

As implementações c# das ações são encontradas na montagem System.Fabric.dll. O módulo PowerShell do tecido do sistema encontra-se no conjunto Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação do tempo de funcionamento, o módulo ServiceFabric PowerShell está instalado para permitir uma facilidade de utilização.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Graciosa vs. ações de culpa graciosas
As ações de testabilidade são classificadas em dois grandes baldes:

* Falhas ingraciosas: Estas falhas simulam falhas como o reinício da máquina e as falhas de processo. Nestes casos de falhas, o contexto de execução do processo para abruptamente. Isto significa que nenhuma limpeza do Estado pode ser executada antes que a aplicação recomece.
* Falhas graciosas: Estas falhas simulam ações graciosas como movimentos de réplica e gotas desencadeadas pelo equilíbrio de carga. Nesses casos, o serviço recebe uma notificação do encerramento e pode limpar o estado antes de sair.

Para uma melhor validação de qualidade, gere a carga de trabalho de serviço e de negócio, ao mesmo tempo que induz várias falhas graciosas e enão graciosas. Falhas ingraciosas exercitam cenários onde o processo de serviço sai abruptamente no meio de algum fluxo de trabalho. Isto testa o caminho de recuperação assim que a réplica do serviço é restaurada pela Service Fabric. Isto ajudará a testar a consistência dos dados e se o estado de serviço é mantido corretamente após falhas. O outro conjunto de falhas (as falhas graciosas) testa que o serviço reage corretamente a réplicas que estão a ser movidas pela Service Fabric. Isto testa o tratamento do cancelamento no método RunAsync. O serviço precisa verificar se o token de cancelamento está a ser definido, salvar corretamente o seu estado e sair do método RunAsync.

## <a name="testability-actions-list"></a>Lista de ações de testabilidade
| Ação | Descrição | API gerido | PowerShell cmdlet | Falhas graciosas/ingraciosas |
| --- | --- | --- | --- | --- |
| CleantestState |Remove todo o estado de ensaio do cluster em caso de paragem do condutor de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| Invocar DataLoss |Induz a perda de dados numa divisória de serviço. |Invocar DataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Gracioso |
| InvocarQuorumLoss |Coloca uma partição de serviço imponente em perda de quórum. |InvocarQuorumLossAsync |Invocar-ServiceFabricQuorumLoss |Gracioso |
| MovePrimary |Move a réplica primária especificada de um serviço de estado para o nó de cluster especificado. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Gracioso |
| MoveSecondary |Move a réplica secundária atual de um serviço imponente para um nó de cluster diferente. |MoveSecondaryAsync |Réplica secundária de serviço de movimentoFabricSecondary |Gracioso |
| Remover Replica |Simula uma falha de réplica removendo uma réplica de um cluster. Isto fechará a réplica e a transirá para o papel "Nenhum", retirando todo o seu estado do cluster. |Remover ReplicaAsync |Remover-ServiceFabricReplica |Gracioso |
| Pacote de código reiniciado |Simula uma falha de processo de pacote de código reiniciando um pacote de código implantado num nó num cluster. Isto aborta o processo do pacote de código, que reiniciará todas as réplicas do serviço de utilizador alojadas nesse processo. |ReinícioDoCódigoPackageAsync |Pacote de código implantado de reinício-serviço |Ingracioso |
| Reinício Do nono |Simula uma falha do nó de cluster de tecido de serviço reiniciando um nó. |ReiniciarNodeAsync |Reiniciar-ServiceFabricNode |Ingracioso |
| Reiniciar A Partição |Simula um apagão de datacenter ou um apagão de cluster reiniciando algumas ou todas as réplicas de uma divisória. |Reiniciar PartitionAsync |Restart-ServiceFabricPartition |Gracioso |
| Reinício Da Réplica |Simula uma falha de réplica reiniciando uma réplica persistiu num cluster, fechando a réplica e reabrindo-a. |ReinícioReplicaAsync |Reinício-ServiceFabricReplica |Gracioso |
| Início Nóde |Começa um nó num aglomerado que já está parado. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha no nó, parando um nó num aglomerado. O nó vai ficar para baixo até que o StartNode seja chamado. |StopNodeAsync |Stop-ServiceFabricNode |Ingracioso |
| Validação Aplicação |Valida a disponibilidade e saúde de todos os serviços de Tecido de Serviço dentro de uma aplicação, geralmente depois de induzir alguma falha no sistema. |ValidaçãoAplicaAsync |Aplicação de fabricação de teste |Não aplicável |
| Validaserviço |Valida a disponibilidade e a saúde de um serviço de Tecido de Serviço, geralmente depois de induzir alguma falha no sistema. |ValidateServiceAsync |Serviço de Teste De Serviçode Tecidos |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executando uma ação de testability usando PowerShell
Este tutorial mostra-lhe como executar uma ação de testability utilizando o PowerShell. Você aprenderá a executar uma ação de testability contra um cluster local (uma caixa) ou um cluster Azure. Microsoft.Fabric.Powershell.dll -- o módulo PowerShell de tecido de serviço -- é instalado automaticamente quando instala o Microsoft Service Fabric MSI. O módulo é carregado automaticamente quando abre um pedido PowerShell.

Segmentos tutoriais:

* [Execute uma ação contra um aglomerado de uma caixa](#run-an-action-against-a-one-box-cluster)
* [Execute uma ação contra um cluster Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Execute uma ação contra um aglomerado de uma caixa
Para executar uma ação de testabilidade contra um cluster local, ligue-se primeiro ao cluster e abra o pedido powerShell no modo de administrador. Vejamos a ação **Restart-ServiceFabricNode.**

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui a ação **Restart-ServiceFabricNode** está a ser executada num nó chamado "Nó1". O modo de conclusão especifica que não deve verificar se a ação do nó de reinício realmente conseguiu. Especificar o modo de conclusão como "Verificar" fará com que verifique se a ação de reinício realmente foi bem sucedida. Em vez de especificar diretamente o nó pelo seu nome, pode especificá-lo através de uma chave de partição e do tipo de réplica, da seguinte forma:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reiniciar o serviçoFabricNode** deve ser utilizado para reiniciar um nó de tecido de serviço num cluster. Isto irá parar o processo Fabric.exe, que reiniciará todo o serviço de sistema e réplicas de serviço de utilizador alojadas nesse nó. A utilização desta API para testar o seu serviço ajuda a descobrir bugs ao longo dos caminhos de recuperação de failover. Ajuda a simular falhas no nó no cluster.

A imagem seguinte mostra o comando de testabilidade **Restart-ServiceFabricNode** em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída do primeiro **Get-ServiceFabricNode** (um cmdlet do módulo PowerShell de tecido de serviço) mostra que o cluster local tem cinco nós: Nó.1 a Nó.5. Após a ação de testabilidade (cmdlet) **Reiniciar o ServiçoFabricNode** é executado no nó, chamado Node.4, vemos que o tempo de uptime do nó foi reposto.

### <a name="run-an-action-against-an-azure-cluster"></a>Execute uma ação contra um cluster Azure
Executar uma ação de testabilidade (utilizando powerShell) contra um cluster Azure é semelhante a executar a ação contra um cluster local. A única diferença é que antes de poder executar a ação, em vez de se ligar ao cluster local, precisa de se ligar primeiro ao cluster Azure.

## <a name="running-a-testability-action-using-c35"></a>Executar uma ação de testability utilizando C&#35;
Para executar uma ação de testability utilizando C#, primeiro precisa de se ligar ao cluster utilizando o FabricClient. Em seguida, obtenha os parâmetros necessários para executar a ação. Diferentes parâmetros podem ser usados para executar a mesma ação.
Olhando para a ação RestartServiceFabricNode, uma maneira de executá-lo é usando a informação do nó (nome do nó e id da instância do nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

* **CompleteMode** especifica que o modo não deve verificar se a ação de reinício realmente foi bem sucedida. Especificar o modo de conclusão como "Verificar" fará com que verifique se a ação de reinício realmente foi bem sucedida.  
* **Operação O tempo** de tempo definido para a operação terminar antes de ser lançada uma exceção timeoutException.
* **CancelamentoToken** permite que uma chamada pendente seja cancelada.

Em vez de especificar diretamente o nó pelo seu nome, pode especificá-lo através de uma chave de partição e do tipo de réplica.

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

## <a name="partitionselector-and-replicaselector"></a>Seletor de divisórias e replicaselector
### <a name="partitionselector"></a>Seletor de partição
PartitionSelector é um ajudante exposto na testability e é usado para selecionar uma divisória específica para executar qualquer uma das ações de testability. Pode ser usado para selecionar uma divisória específica se o ID da partição for conhecido previamente. Ou, pode fornecer a chave de partição e a operação resolverá internamente o ID da partilha. Também tem a opção de selecionar uma partição aleatória.

Para utilizar este ajudante, crie o objeto PartitionSelector e selecione a partição utilizando um dos métodos Select* . Em seguida, passe o objeto PartitionSelector para a API que o exija. Se não for selecionada nenhuma opção, não se aplica a uma divisória aleatória.

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
O ReplicaSelector é um ajudante exposto na testabilidade e é utilizado para ajudar a selecionar uma réplica para executar qualquer uma das ações de testabilidade. Pode ser usado para selecionar uma réplica específica se o ID da réplica for conhecido previamente. Além disso, tem a opção de selecionar uma réplica primária ou uma secundária aleatória. O ReplicaSelector deriva do PartitionSelector, por isso tem de selecionar tanto a réplica como a divisória na qual deseja realizar a operação de testability.

Para utilizar este ajudante, crie um objeto ReplicaSelector e detetete a forma como pretende selecionar a réplica e a partição. Pode então passá-lo para a API que o exige. Se não for selecionada nenhuma opção, não se aplica a uma réplica aleatória e a uma partição aleatória.

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
  * [Simular falhas durante as cargas de trabalho do serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação serviço-serviço](service-fabric-testability-scenarios-service-communication.md)

