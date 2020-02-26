---
title: Escala rés em tecido de serviço dentro ou fora
description: Escala rés em conjunto de tecido de serviço para dentro ou fora para corresponder à procura, estabelecendo regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual. Adicionar ou remover nós do cluster do Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587486"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou aumentar horizontalmente um cluster

> [!WARNING]
> Leia esta secção antes de escalar

A escalação de recursos computacionais para obter a sua carga de trabalho de aplicação requer planeamento intencional, levará quase sempre mais de uma hora para completar para um ambiente de produção, e requer que compreenda a sua carga de trabalho e contexto de negócio; na verdade, se nunca fez esta atividade antes, é aconselhável começar por ler e compreender considerações de planeamento de capacidade de cluster de tecido de [serviço,](service-fabric-cluster-capacity.md)antes de continuar o restante deste documento. Esta recomendação é evitar problemas de LiveSite indesejados, e também é recomendável que testar com êxito as operações que decidir executar em relação a um ambiente de não produção. A qualquer momento pode reportar questões de [produção ou solicitar apoio pago ao Azure.](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure) Para os engenheiros alocados para executar estas operações que possuem o contexto apropriado, este artigo descreverá as operações de dimensionamento, mas tem de decidir e compreender as operações são adequadas para seu caso de utilização; Por exemplo, quais recursos para dimensionamento (CPU, armazenamento, memória), que direção de dimensionamento (vertical ou horizontalmente) e quais operações a serem executadas (modelo do Resource a implementação, do Portal, o PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Dimensionar um cluster do Service Fabric dentro ou para fora usando regras de dimensionamento automático ou manual
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido no cluster do Service Fabric é configurado como um conjunto de dimensionamento de máquina virtual separada. Cada tipo de nó, em seguida, pode ser reduzido horizontalmente ou horizontalmente de forma independente, têm conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Leia mais sobre isso no documento de tipos de [nó de tecido de serviço.](service-fabric-cluster-nodetypes.md) Uma vez que os tipos de nó de tecido de serviço no seu cluster são feitos de conjuntos de escala de máquina virtual na parte de trás, você precisa configurar regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual.

> [!NOTE]
> A sua subscrição tem de ter núcleos suficientes para adicionar as VMs novas que compõem este cluster. Não existe nenhuma validação de modelo atualmente, para que tenha uma falha de tempo de implementação, se qualquer um dos limites são atingidos. Também um tipo de nó único não pode simplesmente exceder 100 nós por VMSS. Poderá ter de adicionar o VMSS alcançar a dimensão de destino, e o dimensionamento automático não pode automagicamente adicionar do VMSS. Adicionar o VMSS's no local a um cluster vivo é uma tarefa desafiante, e geralmente isso resulta em utilizadores que aprovisionam novos clusters com os tipos de nó adequados aprovisionados no momento da criação; [capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) de cluster de plano em conformidade. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolha o nó tipo/Virtual conjunto de dimensionamento de dimensionamento de máquina
Atualmente, não é possível especificar as regras de dimensionamento automático para conjuntos de dimensionamento de máquina virtual com o portal para criar um Cluster do Service Fabric, então, vamos utilizar o Azure PowerShell (1.0 +) para listar os tipos de nó e, em seguida, adicionar regras de dimensionamento automático aos mesmos.

Para obter a lista de conjunto de dimensionamento de máquinas virtuais que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Defina regras de escala automática para o conjunto de escala de tipo de nó/máquina virtual
Se o seu cluster tiver vários tipos de nó, repita isto para cada tipo de nó/conjuntos de escala de máquinavirtual que pretende escalar (dentro ou fora). Antes de configurar o dimensionamento automático, tenha em conta o número de nós que tem de ter. O número mínimo de nós que tem de ter para o tipo de nó principal é condicionado pelo nível de fiabilidade que escolheu. Leia mais sobre os níveis de [fiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> Reduzir verticalmente o nó principal, escreva para o menor do que a marca de número mínimo, o cluster instável ou colocá-la para baixo. Isto pode resultar em perda de dados para as suas aplicações e para os serviços do sistema.
> 
> 

Atualmente a funcionalidade de dimensionamento automático não é orientada por potenciais cargas que os seus aplicativos podem reportar ao Service Fabric. Então, neste momento, o dimensionamento automático, que obtém é puramente orientado pelos contadores de desempenho que são emitidos por cada da máquina virtual conjunto de dimensionamento de instâncias.  

Siga estas instruções [para configurar](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)a escala automática para cada conjunto de escala de máquina virtual .

> [!NOTE]
> Num cenário de descida de escala, a menos que o seu tipo de nó tenha um nível de [durabilidade][durability] de Ouro ou Prata, você precisa chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado. Para a durabilidade do Bronze, não é recomendado reduzir mais do que um nó de cada vez.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicione mVmanualmente a um conjunto de escala de máquinas/máquinas virtuais tipo nó

Ao aumentar horizontalmente, adiciona mais instâncias de máquina virtual ao conjunto de dimensionamento. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. 

> [!NOTE]
> A adição de VMs leva tempo, por isso não espere que as adições sejam instantâneas. Por isso, planeie adicionar capacidade com muita antecedência, para permitir que mais de 10 minutos antes da capacidade vm esteja disponível para que as réplicas/instâncias de serviço possam ser colocadas.
> 

### <a name="add-vms-using-a-template"></a>Adicione VMs usando um modelo
Siga a amostra/instruções na galeria do [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Adicione VMs usando comandos PowerShell ou CLI
O código seguinte obtém um conjunto de dimensionamento por nome e aumenta a **capacidade** do conjunto de dimensionamento em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Este código define a capacidade para 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Remova manualmente VMs de um conjunto de escala de máquina sonâmlo/virtual
Quando escala num nó, remove as instâncias vm do conjunto de escala. Se o nó é o nível de durabilidade de Bronze, o Service Fabric desconhece o que aconteceu e informa que um nó desapareceu. O Service Fabric comunica então o mau estado de funcionamento do cluster. Para evitar esse mau estado, deve remover explicitamente o nó do aglomerado e remover o estado do nó.

Os serviços de sistema de tecido de serviço funcionam no tipo de nó primário no seu cluster. Ao escalonar o tipo de nó primário, nunca reduza o número de instâncias para menos do que o nível de [fiabilidade](service-fabric-cluster-capacity.md) garante. 
 
Para um serviço com estado, terá de um determinado número de nós sempre até ser manter a disponibilidade e preservar o estado do seu serviço. No mínimo, terá do número de nós igual à contagem de conjunto de réplicas de destino do serviço/partição.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

Os passos para a remoção manual do estado do nó aplicam-se apenas aos tipos de nó com um nível de durabilidade de *Bronze.*  Para o nível de durabilidade *prata* e *ouro,* estes passos são feitos automaticamente pela plataforma. Para obter mais informações sobre durabilidade, consulte o planeamento da capacidade de [cluster do Tecido de Serviço.][durability]

Para manter os nós do cluster distribuídos uniformemente entre os domínios de atualização e de falha e, por conseguinte, ativar a utilização dos mesmos, o nó criado mais recentemente deve ser removido primeiro. Por outras palavras, os nós devem ser removidos na ordem inversa da sua criação. O nó criado mais recentemente é aquele com o maior valor da propriedade `virtual machine scale set InstanceId`. Os exemplos de código abaixo devolvem o nó criado mais recentemente.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

O cluster do Service Fabric tem de saber que este nó vai ser removido. Tem de seguir três passos:

1. Desative o nó para que deixe de ser uma replicação dos dados.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Pare o nó, para que o runtime do Service Fabric encerre corretamente e que a aplicação obtenha um pedido para terminar.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Remova o nó do cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Depois de estes três passos serem aplicados ao nó, este pode ser removido do conjunto de dimensionamento. Se estiver a utilizar qualquer nível de durabilidade além do [bronze,][durability]estes passos são feitos para si quando a instância de conjunto de escala for removida.

O bloco de código seguinte obtém o último nó criado, desativa, interrompe e remove o nó do cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

No código **sfctl** abaixo, o comando seguinte é utilizado para aceder ao valor **node-name** do último nó criado: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Utilize as seguintes consultas **sfctl** para verificar o estado de cada passo
>
> **Verificar o estado de desativação**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Verificar o estado de paragem**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Reduzir horizontalmente o conjunto de dimensionamento

Agora que o nó do Service Fabric foi removido do cluster, o conjunto de dimensionamento de máquinas virtuais pode ser reduzido horizontalmente. No exemplo abaixo, a capacidade do conjunto de dimensionamento foi reduzida em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Este código define a capacidade para 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos, que pode observar no Service Fabric Explorer
Ao aumentar verticalmente um cluster do Service Fabric Explorer irá refletir o número de nós (instâncias de conjunto de dimensionamento de máquinas virtuais), que fazem parte do cluster.  No entanto, quando escala um cluster para baixo, verá a instância do nó/VM removida exibida em estado pouco saudável, a menos que chame [remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado.   

Aqui está a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços de sistema do Service Fabric (FM especificamente) sabe sobre o número de nós de cluster tinha/tem. Quando dimensionar o conjunto de dimensionamento para baixo, a VM foi eliminada, mas serviço de sistema de FM ainda acha que o nó (que foi mapeado para a VM que foi eliminada) será retornar. Portanto, o Service Fabric Explorer continua exibir esse nó (embora o estado de funcionamento pode ser erro ou desconhecido).

Para certificar-se de que um nó é removido quando uma VM é removida, tem duas opções:

1. Escolha um nível de durabilidade de Gold ou Silver para os tipos de nó do cluster, o que lhe dá a integração de infraestrutura. Que, em seguida, removerá automaticamente os nós do nosso estado de serviços (FM) do sistema quando reduzir verticalmente.
Consulte [os detalhes sobre os níveis](service-fabric-cluster-capacity.md) de durabilidade aqui

2. Uma vez que a instância VM tenha sido reduzida, é necessário ligar para o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clusters do Service Fabric necessitam de um determinado número de nós para ficar operacional em todo o tempo para manter a disponibilidade e preservar o estado - referido como "manter o quórum." Portanto, é tipicamente inseguro desligar todas as máquinas do cluster a menos que tenha primeiro realizado uma [cópia de segurança completa do seu estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Leia o seguinte para também saber mais sobre o planeamento de capacidade do cluster, atualização de um cluster e a criação de partições de serviços:

* [Planeie a sua capacidade de cluster](service-fabric-cluster-capacity.md)
* [Upgrades de cluster](service-fabric-cluster-upgrade.md)
* [Serviços de estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
