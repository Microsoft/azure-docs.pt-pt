---
title: Escalar um cluster de tecido de serviço dentro ou fora
description: Dimensione um cluster de tecido de serviço dentro ou fora para corresponder à procura, definindo regras de escala automática para cada conjunto de escala tipo de nó/máquina virtual. Adicione ou remova os nosdes a um cluster de Tecido de Serviço
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409965"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou aumentar horizontalmente um cluster

> [!WARNING]
> Leia esta secção antes de escaloná-la

A escala de recursos de cálculo para obter a carga de trabalho da sua aplicação requer planeamento intencional, levará quase sempre mais de uma hora para ser concluída para um ambiente de produção, e exige que compreenda a sua carga de trabalho e contexto de negócio; na verdade, se nunca fez esta atividade antes, recomenda-se que comece por ler e compreender considerações de planeamento de capacidade de [cluster de tecidos](service-fabric-cluster-capacity.md)de serviço , antes de continuar o restante deste documento. Esta recomendação é para evitar problemas não intencionais do LiveSite, e também recomenda-se que teste com sucesso as operações que decide realizar contra um ambiente de não produção. A qualquer momento pode [reportar problemas de produção ou solicitar apoio pago à Azure.](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure) Para os engenheiros afetados a realizar estas operações que possuam o contexto adequado, este artigo descreverá operações de escala, mas deve decidir e compreender quais as operações adequadas para o seu caso de utilização; tais como quais os recursos à escala (CPU, Armazenamento, Memória), que direção à escala (Vertical ou Horizontalmente), e que operações a executar (implantação do modelo de recurso, portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Escalar um cluster de tecido de serviço dentro ou fora usando regras de escala automática ou manualmente
Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que pode utilizar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster de Tecido de Serviço é configurado como um conjunto de escala de máquina virtual separada. Cada tipo de nó pode então ser dimensionado dentro ou fora de forma independente, ter diferentes conjuntos de portas abertas, e pode ter métricas de capacidade diferentes. Leia mais sobre o documento dos tipos de nó de tecido de [serviço.](service-fabric-cluster-nodetypes.md) Uma vez que os tipos de nó de tecido de serviço no seu cluster são feitos de conjuntos de balança de máquina virtual no backend, é necessário configurar regras de escala automática para cada conjunto de escala tipo de nó/máquina virtual.

> [!NOTE]
> A sua subscrição deve ter núcleos suficientes para adicionar os novos VMs que compõem este cluster. Não existe atualmente uma validação do modelo, pelo que obtém uma falha no tempo de implantação, se algum dos limites de quota for atingido. Também um único tipo de nó não pode simplesmente exceder 100 nós por VMSS. Pode ser necessário adicionar VMSS's para atingir a escala direcionada, e a auto-escala não pode adicionar a VMSS's autógicamente. A adição do lugar da VMSS a um cluster vivo é uma tarefa desafiante, e geralmente isso resulta no fornecimento de novos clusters com os tipos de nó adequados a provisionados no momento da criação; [planear a capacidade do cluster](./service-fabric-cluster-capacity.md) em conformidade. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolha o tipo de nó/escala de máquina virtual definida para escalar
Atualmente, não é capaz de especificar as regras de escala automática para conjuntos de escala de máquinas virtuais usando o portal para criar um Cluster de Tecido de Serviço, por isso, vamos usar o Azure PowerShell (1.0+) para listar os tipos de nós e, em seguida, adicionar regras de escala automática a eles.

Para obter a lista de escala de máquina virtual configurada que compõem o seu cluster, execute os seguintes cmdlets:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Definir regras de escala automática para o conjunto de escala tipo de nó/máquina virtual
Se o seu cluster tiver vários tipos de nós, repita-o para cada tipo de nó/conjuntos de balança de máquinas virtuais que pretende escalar (dentro ou fora). Antes de configurar o dimensionamento automático, tenha em conta o número de nós que tem de ter. O número mínimo de nós que tem de ter para o tipo de nó principal é condicionado pelo nível de fiabilidade que escolheu. Leia mais sobre [os níveis de fiabilidade.](service-fabric-cluster-capacity.md)

> [!NOTE]
> A escala no tipo de nó primário para menos do que o número mínimo tornará o cluster instável ou até mesmo derrubá-lo. Isto pode resultar na perda de dados para as suas aplicações e para os serviços do sistema.
> 
> 

Atualmente, a função de escala automática não é conduzida pelas cargas que as suas aplicações podem estar a reportar ao Service Fabric. Assim, neste momento, a escala automática que obtém é puramente impulsionada pelos contadores de desempenho que são emitidos por cada uma das instâncias de escala de máquina virtual.  

Siga estas instruções [para configurar a escala automática para cada conjunto de balanças de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Numa escala em cenário, a menos que o seu tipo de nó tenha um nível de [durabilidade][durability] de Ouro ou Prata, precisa de chamar o [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó apropriado. Para a durabilidade do Bronze, não é recomendado escalar em mais de um nó de cada vez.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicione manualmente VMs a um conjunto de balança de máquina tipo de nó/máquina virtual

Ao aumentar horizontalmente, adiciona mais instâncias de máquina virtual ao conjunto de dimensionamento. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. 

> [!NOTE]
> A adição de VMs leva tempo, por isso não espere que as adições sejam instantâneas. Planeie adicionar capacidade com muita antecedência, permitindo que por mais de 10 minutos antes da capacidade VM esteja disponível para que as réplicas/instâncias de serviço seja colocada.
> 

### <a name="add-vms-using-a-template"></a>Adicionar VMs usando um modelo
Siga a amostra/instruções na galeria do [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Adicionar VMs utilizando comandos PowerShell ou CLI
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

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Remova manualmente VMs de um conjunto de balança de máquina tipo de nó/máquina virtual
Quando escala num tipo de nó, remove-se as instâncias VM do conjunto de escala. Se o tipo de nó for o nível de durabilidade de Bronze, o Service Fabric desconhece o que aconteceu e informa que um nó desapareceu. O Service Fabric comunica então o mau estado de funcionamento do cluster. Para evitar esse mau estado, deve remover explicitamente o nó do aglomerado e remover o estado do nó.

Os serviços de sistema de tecido de serviço funcionam no tipo de nó primário no seu cluster. Ao escalonar o tipo de nó primário, nunca escale o número de casos para menos do que o nível de [fiabilidade](service-fabric-cluster-capacity.md) garante. 
 
Para um serviço estatal, você precisa de um certo número de nós para estar sempre à altura para manter a disponibilidade e preservar o estado do seu serviço. No mínimo, precisa do número de nós iguais à contagem definida de réplica do alvo da partição/serviço.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

Os passos para remover manualmente o estado do nó aplicam-se apenas aos tipos de nó com um nível de durabilidade *de Bronze.*  Para o nível de durabilidade *prata* e *ouro,* estes passos são feitos automaticamente pela plataforma. Para obter mais informações sobre a durabilidade, veja [Planeamento da capacidade de cluster do Service Fabric][durability].

>[!NOTE]
> Mantenha uma contagem mínima de cinco nós para qualquer conjunto de balança de máquina virtual que tenha um nível de durabilidade de Ouro ou Prata ativado. O seu cluster entrará em estado de erro se escalar abaixo deste limiar, e terá de limpar manualmente os nós removidos.

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
> **Verificar estado de desativação**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Verifique o estado da paragem**
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

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que pode observar no Service Fabric Explorer
Quando escalona um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de escala de máquina virtual) que fazem parte do cluster.  No entanto, quando escalar um cluster, verá a instância nóla de nó/VM removida exibida num estado pouco saudável, a menos que ligue para [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó apropriado.   

Aqui está a explicação para este comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços de sistema de Tecido de Serviço (FM especificamente) sabem sobre o número de nós que o cluster tinha/tem. Quando escala a escala de máquina virtual definida, o VM foi eliminado, mas o serviço de sistema FM ainda pensa que o nó (que foi mapeado para o VM que foi eliminado) voltará. Assim, o Service Fabric Explorer continua a exibir esse nó (embora o estado de saúde possa ser erro ou desconhecido).

Para se certificar de que um nó é removido quando um VM é removido, tem duas opções:

1. Escolha um nível de durabilidade de Ouro ou Prata para os tipos de nó no seu cluster, o que lhe dá a integração da infraestrutura. Que irá então remover automaticamente os nós do nosso estado de serviços de sistema (FM) quando você escala dentro
Consulte [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

> [!NOTE]
> Mantenha uma contagem mínima de cinco nós para qualquer conjunto de balança de máquina virtual que tenha um nível de durabilidade de Ouro ou Prata ativado. O seu cluster entrará em estado de erro se escalar abaixo deste limiar, e terá de limpar manualmente os nós removidos.

2. Uma vez que a instância VM tenha sido dimensionada, você precisa chamar o [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Os clusters de tecidos de serviço requerem que um certo número de nós esteja sempre de pé para manter a disponibilidade e preservar o estado - referido como "manutenção do quórum". Assim, é normalmente inseguro desligar todas as máquinas do cluster, a menos que tenha feito pela primeira vez uma [cópia de segurança completa do seu estado.](service-fabric-reliable-services-backup-restore.md)
> 
> 

## <a name="next-steps"></a>Passos seguintes
Leia o seguinte para também aprender sobre a capacidade de planeamento do cluster, atualização de um cluster e serviços de partição:

* [Planeie a sua capacidade de cluster](service-fabric-cluster-capacity.md)
* [Atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Serviços estatais de partição para a escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
