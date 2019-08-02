---
title: Escalar verticalmente um tipo de nó de Service Fabric do Azure | Microsoft Docs
description: Saiba como dimensionar um Cluster Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 272bc571a0ea71fd6e7bd45a426460d2e0faf1d7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599295"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Escalar verticalmente um tipo de nó primário de Cluster Service Fabric
Este artigo descreve como escalar verticalmente um tipo de nó primário de Cluster Service Fabric aumentando os recursos de máquina virtual. Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster de Service Fabric, você pode dimensionar um tipo de nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operacional das VMs do tipo de nó.  Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

> [!WARNING]
> Não comece a alterar o SKU da VM NodeType primária, se a integridade do cluster não estiver íntegra. Se a integridade do cluster não estiver íntegra, você só desestabilizará o cluster se tentar alterar a SKU da VM.
>
> Recomendamos que você não altere a SKU da VM de um conjunto de dimensionamento/tipo de nó, a menos que ele esteja sendo executado na durabilidade de [prata ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). A alteração do tamanho do SKU da VM é uma operação de infraestrutura in-loco destrutiva de dados. Sem alguma capacidade de atrasar ou monitorar essa alteração, é possível que a operação possa causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem estado. Isso significa o tipo de nó primário, que está executando serviços de sistema do Service Fabric com estado ou qualquer tipo de nó que esteja executando suas cargas de trabalho de aplicativo com estado.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Atualizar o tamanho e o sistema operacional das VMs do tipo de nó primário
Aqui está o processo para atualizar o tamanho da VM e o sistema operacional das VMs do tipo de nó primário.  Após a atualização, as VMs do tipo de nó primário são de tamanho padrão D4_V2 e executando o Windows Server 2016 datacenter com contêineres.

> [!WARNING]
> Antes de tentar esse procedimento em um cluster de produção, recomendamos que você estude os modelos de exemplo e verifique o processo em relação a um cluster de teste. O cluster também está indisponível por um tempo. Você não pode fazer alterações em vários VMSS declarados como o mesmo NodeType em paralelo; será necessário executar operações de implantação separadas para aplicar as alterações a cada NodeType VMSS individualmente.

1. Implante o cluster inicial com dois tipos de nó e dois conjuntos de dimensionamento (um conjunto de dimensionamento por tipo de nó) usando esses arquivos de [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) de exemplo.  Ambos os conjuntos de dimensionamento têm o tamanho padrão D2_V2 e executando o Windows Server 2012 R2 Datacenter.  Aguarde até que o cluster conclua a atualização da linha de base.   
2. Opcional – implante uma amostra com monitoração de estado no cluster.
3. Depois de decidir atualizar as VMs do tipo de nó primário, adicione um novo conjunto de dimensionamento ao tipo de nó primário usando esses arquivos de [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) e [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) de exemplo para que o tipo de nó primário agora tenha dois conjuntos de dimensionamento.  Os serviços do sistema e os aplicativos de usuário são capazes de migrar entre VMs nos dois conjuntos de dimensionamento diferentes.  As novas VMs do conjunto de dimensionamento são de tamanho padrão D4_V2 e executam o Windows Server 2016 datacenter com contêineres.  Um novo balanceador de carga e um endereço IP público também são adicionados com o novo conjunto de dimensionamento.  
    Para localizar o novo conjunto de dimensionamento no modelo, procure o recurso "Microsoft. Compute/virtualMachineScaleSets" denominado pelo parâmetro *vmNodeType2Name* .  O novo conjunto de dimensionamento é adicionado ao tipo de nó primário usando as propriedades-> virtualMachineProfile-> extensionProfile-> extensões-> Propriedades-> Configurações-> nodeTypeRef configuração.
4. Verifique a integridade do cluster e verifique se todos os nós estão íntegros.
5. Desabilite os nós no conjunto de dimensionamento antigo do tipo de nó primário com a intenção de remover o nó. Você pode desabilitar tudo de uma vez e as operações são enfileiradas. Aguarde até que todos os nós sejam desabilitados, o que pode levar algum tempo.  Como os nós mais antigos no tipo de nó estão desabilitados, os serviços do sistema e os nós de propagação migram para as VMs do novo conjunto de dimensionamento no tipo de nó primário.
6. Remova o conjunto de dimensionamento mais antigo do tipo de nó primário.
7. Remova o balanceador de carga associado ao conjunto de dimensionamento antigo. O cluster não está disponível enquanto o novo endereço IP público e o balanceador de carga estão configurados para o novo conjunto de dimensionamento.  
8. Armazene as configurações de DNS do endereço IP público associado ao conjunto de dimensionamento do tipo de nó primário antigo em uma variável e remova esse endereço IP público.
9. Substitua as configurações de DNS do endereço IP público associado ao novo conjunto de dimensionamento do tipo de nó primário pelas configurações de DNS do endereço IP público excluído.  O cluster agora está acessível novamente.
10. Remova o estado do nó dos nós do cluster.  Se o nível de durabilidade do conjunto de dimensionamento antigo era prata ou ouro, essa etapa é feita automaticamente pelo sistema.
11. Se você implantou o aplicativo com estado em uma etapa anterior, verifique se o aplicativo está funcionando.

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
* Saiba como [Adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre a escalabilidade do [aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar ou reduzir um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster do Azure](service-fabric-cluster-programmatic-scaling.md) programaticamente usando o SDK de computação do Azure fluente.
* [Dimensionar ou reduzir um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

