---
title: Dimensione um nó de tecido de serviço Azure
description: Aprenda a escalar um cluster de tecido de serviço adicionando um conjunto de escala de máquina virtual.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464222"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentar verticalmente um nó do tipo primário do cluster do Service Fabric
Este artigo descreve como escalar um tipo de nó primário de cluster de tecido de serviço, aumentando os recursos da máquina virtual. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os conjuntos de escala de máquinas virtuais são um recurso de computação Azure que utiliza para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um tipo de nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operativo dos VMs do tipo nó.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não comece a alterar o nóprimário VM SKU, se a saúde do cluster não for saudável. Se a saúde do cluster não for saudável, só irá desestabilizar ainda mais o cluster, se tentar alterar o VM SKU.
>
> Recomendamos que não altere o VM SKU de um tipo de conjunto/nó de escala, a menos que esteja a funcionar na [durabilidade da Prata ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Mudar o tamanho VM SKU é uma operação de infraestrutura destrutiva em dados. Sem alguma capacidade de atrasar ou monitorizar esta alteração, é possível que a operação possa causar perda de dados para serviços estatais ou causar outras questões operacionais imprevistas, mesmo para cargas de trabalho apátridas. Isto significa que o seu tipo principal de nó, que está a executar serviços de sistema de tecido de serviço audais, ou qualquer tipo de nó que esteja a executar as suas cargas de trabalho de aplicação audais.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Atualizar o tamanho e o sistema operativo dos VMs do nó primário
Aqui está o processo de atualização do tamanho vm e do sistema operativo dos VM do tipo principal vMs.  Após a atualização, os VMs do nó principal são o tamanho Standard D4_V2 e executando o Datacenter Do Windows Server 2016 com contentores.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que estude os modelos de amostra e verifique o processo contra um cluster de teste. O cluster também está indisponível por um tempo. NÃO pode fazer alterações em múltiplos VMSS declarados como o mesmo Nó Type em paralelo; terá de efetuar operações de implantação separadas para aplicar alterações individualmente em cada VMSS no nó.

1. Desloque o cluster inicial com dois tipos de nó e dois conjuntos de escala (um conjunto de escala por tipo de nó) utilizando estes [modelos](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) de amostra e ficheiros [de parâmetros.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Ambos os conjuntos de escala são tamanho Standard D2_V2 e executando O Datacenter Do Windows Server 2012 R2.  Aguarde que o cluster complete a atualização de base.   
2. Opcional: coloque uma amostra de estado no cluster.
3. Depois de decidir atualizar os VMs do nó primário, adicione um novo conjunto de escala ao tipo de nó primário utilizando estes [modelos](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) de amostra e ficheiros de [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) para que o tipo de nó primário tenha agora dois conjuntos de escala.  Os serviços de sistema e as aplicações dos utilizadores são capazes de migrar entre VMs nos dois conjuntos de escala diferentes.  Os novos VMs conjuntos de escala são tamanho Standard D4_V2 e executam o Datacenter Do Windows Server 2016 com contentores.  Um novo equilibrador de carga e endereço IP público também são adicionados com o novo conjunto de escala.  
    Para encontrar a nova escala definida no modelo, procure o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro *vmNodeType2Name.*  O novo conjunto de escala é adicionado ao tipo de nó primário utilizando a extensão virtual >>de propriedades->>Máquinasdeperfil->de extensões->definições >>definição de nóTypeRef >definição.
4. Verifique a saúde do cluster e verifique se todos os nós estão saudáveis.
5. Desative os nódosos no conjunto de calcário antigo do tipo de nó primário com a intenção de remover o nó. Podedes desativar tudo de uma vez e as operações estão em fila. Espere até que todos os nós estejam desativados, o que pode levar algum tempo.  Como os nós mais velhos do tipo de nó são desativados, os serviços do sistema e os nós de sementes migram para os VMs da nova escala definida no tipo de nó primário.
6. Retire a balança mais antiga do tipo de nó primário.
7. Retire o equilíbrio de carga associado ao conjunto de calcário antigo. O cluster não está disponível enquanto o novo endereço IP público e o equilibrador de carga estão configurados para o novo conjunto de escala.  
8. Guarde as definições de DNS do endereço IP público associado à antiga escala do tipo de nó primário definida numa variável e remova esse endereço IP público.
9. Substitua as definições de DNS do endereço IP público associado seleção do novo tipo de nó primário com as definições dNS do endereço IP público eliminado.  O aglomerado está agora acessível novamente.
10. Retire o estado do nó dos nós do aglomerado.  Se o nível de durabilidade do conjunto de escala antiga for prateado ou dourado, este passo é feito automaticamente pelo sistema.
11. Se implementou a aplicação imponente num passo anterior, verifique se a aplicação está funcional.

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

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escala um aglomerado Azure para dentro ou para fora.](service-fabric-tutorial-scale-cluster.md)
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) utilizando o fluente sdk de computação Azure.
* [Escala um aglomerado autónomo dentro ou fora.](service-fabric-cluster-windows-server-add-remove-nodes.md)

