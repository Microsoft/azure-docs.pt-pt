---
title: Escala rés em tecido de serviço dentro ou fora
description: Escala rés em conjunto de tecido de serviço para dentro ou fora para corresponder à procura, estabelecendo regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual. Adicione ou remova nós para um cluster de tecido de serviço
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258736"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou aumentar horizontalmente um cluster

> [!WARNING]
> Leia esta secção antes de escalar

A escalação de recursos computacionais para obter a sua carga de trabalho de aplicação requer planeamento intencional, levará quase sempre mais de uma hora para completar para um ambiente de produção, e requer que compreenda a sua carga de trabalho e contexto de negócio; na verdade, se nunca fez esta atividade antes, é aconselhável começar por ler e compreender considerações de planeamento de capacidade de cluster de tecido de [serviço,](service-fabric-cluster-capacity.md)antes de continuar o restante deste documento. Esta recomendação é evitar problemas não intencionais do LiveSite, e também é recomendado que teste com sucesso as operações que decide realizar contra um ambiente de não produção. A qualquer momento pode reportar questões de [produção ou solicitar apoio pago ao Azure.](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure) Para os engenheiros afetados à realização destas operações que possuam um contexto adequado, este artigo descreverá as operações de escala, mas deve decidir e compreender quais as operações adequadas para o seu caso de utilização; tais como quais os recursos à escala (CPU, Armazenamento, Memória), que direção à escala (vertical ou horizontalmente) e que operações a realizar (implantação do modelo de recurso, portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Escala rés em forma de cluster de tecido de serviço dentro ou fora usando regras de escala automática ou manualmente
Os conjuntos de escala de máquinavirtual são um recurso de computação Azure que pode usar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó que é definido num cluster de tecido de serviço é configurado como um conjunto de escala de máquina virtual separado. Cada tipo de nó pode então ser escalado dentro ou fora de forma independente, ter diferentes conjuntos de portas abertas, e pode ter métricas de capacidade diferentes. Leia mais sobre isso no documento de tipos de [nó de tecido de serviço.](service-fabric-cluster-nodetypes.md) Uma vez que os tipos de nó de tecido de serviço no seu cluster são feitos de conjuntos de escala de máquina virtual na parte de trás, você precisa configurar regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual.

> [!NOTE]
> A sua subscrição deve ter núcleos suficientes para adicionar os novos VMs que compõem este cluster. Não existe atualmente uma validação de modelos, pelo que obtém uma falha de tempo de implantação, se algum dos limites de quota for atingido. Também um único tipo de nó não pode simplesmente exceder 100 nós por VMSS. Pode ser necessário adicionar VMSS's para atingir a escala direcionada, e a escala automática não pode adicionar automaticamente vMSS. Adicionar o VMSS's no local a um cluster vivo é uma tarefa desafiante, e geralmente isso resulta em utilizadores que aprovisionam novos clusters com os tipos de nó adequados aprovisionados no momento da criação; [capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) de cluster de plano em conformidade. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolha o tipo de nó/escala de máquina virtual definida à escala
Atualmente, não é possível especificar as regras de escala automática para conjuntos de escala de máquinas virtuais utilizando o portal para criar um Cluster de Tecidos de Serviço, por isso vamos usar o Azure PowerShell (1.0+) para listar os tipos do nó e, em seguida, adicionar-lhes regras de escala automática.

Para obter a lista de conjuntos de escala de máquinavirtual que compõem o seu cluster, execute os seguintes cmdlets:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Defina regras de escala automática para o conjunto de escala de tipo de nó/máquina virtual
Se o seu cluster tiver vários tipos de nó, repita isto para cada tipo de nó/conjuntos de escala de máquinavirtual que pretende escalar (dentro ou fora). Antes de configurar o dimensionamento automático, tenha em conta o número de nós que tem de ter. O número mínimo de nós que tem de ter para o tipo de nó principal é condicionado pelo nível de fiabilidade que escolheu. Leia mais sobre os níveis de [fiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> A redução do nó primário para menos do que o número mínimo torna o cluster instável ou derruba-o. Isto pode resultar em perda de dados para as suas aplicações e para os serviços do sistema.
> 
> 

Atualmente, a função de escala automática não é impulsionada pelas cargas que as suas aplicações podem estar a reportar ao Service Fabric. Por isso, neste momento, a escala automática que obtém é puramente impulsionada pelos contadores de desempenho que são emitidos por cada uma das instâncias de conjunto de escala de máquina virtual.  

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
 
Para um serviço imponente, precisa de um certo número de nós para estar sempre à altura para manter a disponibilidade e preservar o estado do seu serviço. No mínimo, precisa do número de nós iguais à contagem de réplicas-alvo da partilha/serviço.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

Os passos para a remoção manual do estado do nó aplicam-se apenas aos tipos de nó com um nível de durabilidade de *Bronze.*  Para o nível de durabilidade *prata* e *ouro,* estes passos são feitos automaticamente pela plataforma. Para obter mais informações sobre a durabilidade, veja [Planeamento da capacidade de cluster do Service Fabric][durability].

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
> **Verifique o estado de desativação**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Verifique o estado do stop**
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
Quando escalar um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de conjunto de máquinas virtuais) que fazem parte do cluster.  No entanto, quando escala um cluster para baixo, verá a instância do nó/VM removida exibida em estado pouco saudável, a menos que chame [remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado.   

Aqui está a explicação para este comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços de sistema service Fabric (FM especificamente) sabem sobre o número de nós que o cluster tinha/tem. Ao escalar a escala virtual da máquina definida, o VM foi eliminado, mas o serviço de sistema FM ainda pensa que o nó (que foi mapeado para o VM que foi eliminado) vai voltar. Assim, o Service Fabric Explorer continua a mostrar esse nó (embora o estado de saúde possa ser um erro ou desconhecido).

Para se certificar de que um nó é removido quando um VM é removido, tem duas opções:

1. Escolha um nível de durabilidade de Ouro ou Prata para os tipos de nó no seu cluster, o que lhe dá a integração da infraestrutura. O que removerá automaticamente os nós do nosso estado de serviços de sistema (FM) quando reduzir a escala.
Consulte [os detalhes sobre os níveis](service-fabric-cluster-capacity.md) de durabilidade aqui

2. Uma vez que a instância VM tenha sido reduzida, é necessário ligar para o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Os clusters de tecidos de serviço exigem que um certo número de nós esteja sempre em pé para manter a disponibilidade e preservar o estado - referido como "manutenção do quórum". Portanto, é tipicamente inseguro desligar todas as máquinas do cluster a menos que tenha primeiro realizado uma [cópia de segurança completa do seu estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Leia o seguinte para aprender também sobre planeamento da capacidade do cluster, modernização de um cluster e serviços de partição:

* [Planeie a sua capacidade de cluster](service-fabric-cluster-capacity.md)
* [Upgrades de cluster](service-fabric-cluster-upgrade.md)
* [Serviços de estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
