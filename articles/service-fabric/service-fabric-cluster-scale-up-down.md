---
title: Dimensionar um cluster do Service Fabric in ou out | Documentos da Microsoft
description: Dimensione um cluster do Service Fabric dentro ou para fora de acordo com a pedido através da definição de regras de dimensionamento automático para cada conjunto de dimensionamento de máquina virtual/tipo do nó. Adicionar ou remover nós do cluster do Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: 4c3a9f00ed92194c4f81ab44cb9ca86d4a85fea1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224350"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou aumentar horizontalmente um cluster

> [!WARNING]
> Leia esta secção antes de aumentar

Dimensionar recursos de computação para a origem a carga de trabalho de aplicação requer um planejamento intencional, quase sempre irá demorar mais de uma hora para concluir para um ambiente de produção e exige que compreender a sua carga de trabalho e o contexto de negócios; na verdade se nunca tiver feito essa atividade antes, recomenda-se começar por ler e entender [considerações de planeamento de capacidade do cluster de Service Fabric](service-fabric-cluster-capacity.md), antes de continuar o restante deste documento. Esta recomendação é evitar problemas de LiveSite indesejados, e também é recomendável que testar com êxito as operações que decidir executar em relação a um ambiente de não produção. Em qualquer altura, pode [comunicar problemas de produção ou pedido de suporte pago para o Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Para os engenheiros alocados para executar estas operações que possuem o contexto apropriado, este artigo descreverá as operações de dimensionamento, mas tem de decidir e compreender as operações são adequadas para seu caso de utilização; Por exemplo, quais recursos para dimensionamento (CPU, armazenamento, memória), que direção de dimensionamento (vertical ou horizontalmente) e quais operações a serem executadas (modelo do Resource a implementação, do Portal, o PowerShell/CLI).

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Dimensionar um cluster do Service Fabric dentro ou para fora usando regras de dimensionamento automático ou manual
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido no cluster do Service Fabric é configurado como um conjunto de dimensionamento de máquina virtual separada. Cada tipo de nó, em seguida, pode ser reduzido horizontalmente ou horizontalmente de forma independente, têm conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Saiba mais sobre ele na [tipos de nó do Service Fabric](service-fabric-cluster-nodetypes.md) documento. Uma vez que os tipos de nós no cluster do Service Fabric são constituídos por conjuntos de dimensionamento de máquinas virtuais no back-end, terá de configurar regras de dimensionamento automático para cada conjunto de dimensionamento de máquinas virtuais/tipo do nó.

> [!NOTE]
> A sua subscrição tem de ter núcleos suficientes para adicionar as VMs novas que compõem este cluster. Não existe nenhuma validação de modelo atualmente, para que tenha uma falha de tempo de implementação, se qualquer um dos limites são atingidos. Também um tipo de nó único não pode simplesmente exceder 100 nós por VMSS. Poderá ter de adicionar o VMSS alcançar a dimensão de destino, e o dimensionamento automático não pode automagicamente adicionar do VMSS. Adição direta do VMSS a um cluster ativo é uma tarefa desafiadora e normalmente isso resulta em novos clusters de aprovisionamento com os tipos de nó adequado aprovisionados no momento de criação de utilizadores [planear a capacidade de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) em conformidade. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolha o nó tipo/Virtual conjunto de dimensionamento de dimensionamento de máquina
Atualmente, não é possível especificar as regras de dimensionamento automático para conjuntos de dimensionamento de máquina virtual com o portal para criar um Cluster do Service Fabric, então, vamos utilizar o Azure PowerShell (1.0 +) para listar os tipos de nó e, em seguida, adicionar regras de dimensionamento automático aos mesmos.

Para obter a lista de conjunto de dimensionamento de máquinas virtuais que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Definir regras de dimensionamento automático para o conjunto de dimensionamento de máquinas virtuais/tipo do nó
Se o cluster tem vários tipos de nó, em seguida, repetir que isso para cada dimensionamento de máquinas virtuais/tipos de nó define de que pretende dimensionar (entrada ou saída). Antes de configurar o dimensionamento automático, tenha em conta o número de nós que tem de ter. O número mínimo de nós que tem de ter para o tipo de nó principal é condicionado pelo nível de fiabilidade que escolheu. Leia mais sobre [níveis de confiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> Reduzir verticalmente o nó principal, escreva para o menor do que a marca de número mínimo, o cluster instável ou colocá-la para baixo. Isto pode resultar em perda de dados para as suas aplicações e para os serviços do sistema.
> 
> 

Atualmente a funcionalidade de dimensionamento automático não é orientada por potenciais cargas que os seus aplicativos podem reportar ao Service Fabric. Então, neste momento, o dimensionamento automático, que obtém é puramente orientado pelos contadores de desempenho que são emitidos por cada da máquina virtual conjunto de dimensionamento de instâncias.  

Siga estas instruções [para configurar o dimensionamento automático para cada conjunto de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Numa escala para baixo do cenário, a menos que o seu tipo de nó tem um [nível de durabilidade] [ durability] de Gold ou Silver precisa chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó adequado. Para a durabilidade de Bronze, não é recomendado para reduzir verticalmente mais de um nó por vez.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicionar VMs manualmente a um conjunto de dimensionamento de máquinas virtuais/tipo do nó

Ao aumentar horizontalmente, adiciona mais instâncias de máquina virtual ao conjunto de dimensionamento. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. 

> [!NOTE]
> Adicionar VMs demora tempo, pelo que não conta as adições ser instantâneo. Então, planeie adicionar capacidade de antecedência, para permitir mais de 10 minutos antes da capacidade VM está disponível para as instâncias de serviço/réplicas para serão colocados.
> 

### <a name="add-vms-using-a-template"></a>Adicionar VMs através de um modelo
Siga as instruções/exemplo no [Galeria de modelos de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Adicionar VMs a utilizar comandos do PowerShell ou CLI
O código seguinte obtém um conjunto de dimensionamento por nome e aumenta a **capacidade** do conjunto de dimensionamento em 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Este código define a capacidade para 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Remova manualmente as VMs a partir de um conjunto de dimensionamento de máquinas virtuais/tipo do nó
Ao reduzir horizontalmente um tipo de nó, remover instâncias de VM do conjunto de dimensionamento. Se o tipo de nó é o nível de durabilidade de Bronze, Service Fabric não tem o que aconteceu e relatórios que um nó tornou-se em falta. O Service Fabric comunica então o mau estado de funcionamento do cluster. Para impedir esse mau estado, explicitamente tem de remover o nó do cluster e remover o estado do nó.

Executam os serviços de sistema do service fabric no tipo de nó principal no seu cluster. Ao reduzir verticalmente o tipo de nó primário, nunca reduzir verticalmente o número de instâncias para menos do que o [escalão de fiabilidade](service-fabric-cluster-capacity.md) justifica. 
 
Para um serviço com estado, terá de um determinado número de nós sempre até ser manter a disponibilidade e preservar o estado do seu serviço. No mínimo, terá do número de nós igual à contagem de conjunto de réplicas de destino do serviço/partição.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

Os passos para remover manualmente o estado do nó aplicam-se apenas aos tipos de nós com um *Bronze* escalão de durabilidade.  Para *prata* e *Gold* escalão de durabilidade, essas etapas são feitas automaticamente pela plataforma. Para obter mais informações sobre a durabilidade, veja [Planeamento da capacidade de cluster do Service Fabric][durability].

Para manter os nós do cluster distribuídos uniformemente entre os domínios de atualização e de falha e, por conseguinte, ativar a utilização dos mesmos, o nó criado mais recentemente deve ser removido primeiro. Por outras palavras, os nós devem ser removidos na ordem inversa da sua criação. O nó criado mais recentemente é aquele com o maior valor da propriedade `virtual machine scale set InstanceId`. Os exemplos de código abaixo devolvem o nó criado mais recentemente.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
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

Depois de estes três passos serem aplicados ao nó, este pode ser removido do conjunto de dimensionamento. Se estiver a utilizar qualquer escalão de durabilidade além de [bronze][durability], estes passos serão efetuados quando a instância do conjunto de dimensionamento for removida.

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

```azurecli
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
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Este código define a capacidade para 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos, que pode observar no Service Fabric Explorer
Ao aumentar verticalmente um cluster do Service Fabric Explorer irá refletir o número de nós (instâncias de conjunto de dimensionamento de máquinas virtuais), que fazem parte do cluster.  No entanto, quando dimensiona um cluster para baixo, verá a instância VM/nó removido apresentada em mau estado de funcionamento, a menos que chamar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó adequado.   

Aqui está a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços de sistema do Service Fabric (FM especificamente) sabe sobre o número de nós de cluster tinha/tem. Quando dimensionar o conjunto de dimensionamento para baixo, a VM foi eliminada, mas serviço de sistema de FM ainda acha que o nó (que foi mapeado para a VM que foi eliminada) será retornar. Portanto, o Service Fabric Explorer continua exibir esse nó (embora o estado de funcionamento pode ser erro ou desconhecido).

Para certificar-se de que um nó é removido quando uma VM é removida, tem duas opções:

1. Escolha um nível de durabilidade de Gold ou Silver para os tipos de nó do cluster, o que lhe dá a integração de infraestrutura. Que, em seguida, removerá automaticamente os nós do nosso estado de serviços (FM) do sistema quando reduzir verticalmente.
Consulte [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2. Assim que a instância VM foi reduzida, precisa chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clusters do Service Fabric necessitam de um determinado número de nós para ficar operacional em todo o tempo para manter a disponibilidade e preservar o estado - referido como "manter o quórum." Assim, é normalmente não seguro para encerrar todas as máquinas no cluster, a menos que o utilizador tiver sido executado pela primeira vez um [cópia de segurança completa do seu estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Leia o seguinte para também saber mais sobre o planeamento de capacidade do cluster, atualização de um cluster e a criação de partições de serviços:

* [Planear a capacidade de cluster](service-fabric-cluster-capacity.md)
* [Atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Serviços com estado de partição para dimensionamento máximo](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
