---
title: Aumentar verticalmente um tipo de nó do Azure Service Fabric | Documentos da Microsoft
description: Saiba como dimensionar um cluster do Service Fabric através da adição de um conjunto de dimensionamento de Máquina Virtual.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: e6b429189491af71f6215f1c7660be5965741bf7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798218"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentar verticalmente um tipo de nó principal do cluster de Service Fabric
Este artigo descreve como aumentar verticalmente um tipo de nó principal do Service Fabric cluster ao aumentar os recursos de máquina virtual. Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerido separadamente. Depois de criar um cluster do Service Fabric, pode dimensionar um tipo de nó de cluster verticalmente (alterar os recursos de nós) ou atualizar o sistema operativo do tipo de nó VMs.  Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.  Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

> [!WARNING]
> Não se iniciam alterar o SKU de VM, o nodetype primário se o estado de funcionamento do cluster está danificado. Se o estado de funcionamento do cluster está danificado, apenas serão desestabilizar além disso, o cluster, se tentar alterar o SKU de VM.
>
> Recomendamos que não altere o SKU de VM de um tipo de nó/conjunto de dimensionamento, a menos que ele está em execução no [durabilidade de Silver ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Alterar tamanho da SKU de VM é uma operação de infraestrutura do destrutiva dados no local. Sem capacidade algum atraso ou monitorizar esta alteração, é possível que a operação pode causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem monitorização de estado. Isso significa que o seu tipo de nó primário, o que está a executar recursos de infraestrutura do serviço com estado serviços do sistema, ou qualquer tipo de nó que está a executar o seu trabalho com monitorização de estado do aplicativo é carregado.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Atualizar o tamanho e o sistema operativo do tipo de nó primário VMs
Eis o processo para atualizar o tamanho da VM e o sistema operativo do tipo de nó primário VMs.  Após a atualização, o tipo de nó primário VMs são Standard D4_V2 de tamanho e em execução do Windows Server 2016 Datacenter com contentores.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que Estude os modelos de exemplo e verifique se o processo em relação a um cluster de teste. O cluster também está disponível para um período de tempo. NÃO pode efetuar alterações a vários VMSS declarado como o NodeType mesmo em paralelo; terá de efetuar as operações de implementação separados para aplicar as alterações para cada VMSS NodeType individualmente.

1. Implementar o cluster inicial com dois tipos de nós e conjuntos de dimensionamento de dois (um conjunto de dimensionamento de por tipo de nó) com estas amostra [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) ficheiros.  Ambos os conjuntos de dimensionamento são tamanho D2_V2 padrão e em execução Windows Server 2012 R2 Datacenter.  Aguarde que o cluster concluir a atualização de linha de base.   
2. Opcional - implementar uma amostra com monitoração de estado no cluster.
3. Depois de decidir atualizar o tipo de nó primário VMs, adicionar um novo conjunto de dimensionamento para o tipo de nó primário a utilizar estas exemplo [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) ficheiros para o tipo de nó primário tem agora dois conjuntos de dimensionamento.  Serviços do sistema e aplicativos de usuários são capazes de migrar entre VMs nos dois conjuntos de dimensionamento diferente.  O novo conjunto de dimensionamento são VMs Standard D4_V2 de tamanho e executar o Windows Server 2016 Datacenter com contentores.  Um novo Balanceador de carga e o endereço IP público também são adicionados com o novo conjunto de dimensionamento.  
    Para encontrar o novo conjunto de dimensionamento no modelo, procure o recurso de "Compute/virtualmachinescalesets" com o nome da *vmNodeType2Name* parâmetro.  O novo conjunto de dimensionamento é adicionado para o tipo de nó principal com as propriedades -> virtualMachineProfile - > extensionProfile -> extensões -> propriedades -> Definições -> o nodeTypeRef definição.
4. Verifique o estado de funcionamento do cluster e certifique-se de que todos os nós estão em bom Estados.
5. Desative os nós no conjunto de dimensionamento antigo do tipo de nó principal com a intenção de remover o nó. Pode desativar ao mesmo tempo e as operações são colocados em fila. Aguarde até que todos os nós estão desativados, o que poderá demorar algum tempo.  Como os nós mais antigos no tipo de nó são desativados, os nós de semente e serviços do sistema migram para as VMs do novo conjunto de dimensionamento no tipo de nó primário.
6. Remova o conjunto do tipo de nó primário de dimensionamento mais antigo.
7. Remova o Balanceador de carga associado com o conjunto de dimensionamento antigo. O cluster não está disponível enquanto o novo IP endereço e a carga balanceador público são configuradas para o novo conjunto de dimensionamento.  
8. Store as definições de DNS do endereço IP público associado com o nó principal antigo tipo de conjunto de dimensionamento numa variável e remover esse endereço IP público.
9. Substitua as definições de DNS do endereço IP público associado com o novo nó primário tipo conjunto de dimensionamento com as definições de DNS do endereço IP público eliminado.  O cluster está agora acessível novamente.
10. Remova o estado do nó de nós do cluster.  Se o nível de durabilidade de conjunto de dimensionamento antigos competência silver ou gold, este passo é feito automaticamente pelo sistema.
11. Se implementou a aplicação com monitorização de estado num passo anterior, certifique-se de que o aplicativo é funcional.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar um cluster do Azure e reduzir](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure através de programação](service-fabric-cluster-programmatic-scaling.md) através do Azure fluent computação SDK.
* [Dimensionar um cluster autónomo e reduzir](service-fabric-cluster-windows-server-add-remove-nodes.md).

