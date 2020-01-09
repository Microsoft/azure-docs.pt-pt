---
title: Implantar um cluster entre Zonas de Disponibilidade
description: Saiba como criar um cluster de Service Fabric do Azure em Zonas de Disponibilidade.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609983"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implantar um cluster de Service Fabric do Azure entre Zonas de Disponibilidade
Zonas de Disponibilidade no Azure é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. Uma zona de disponibilidade é um local físico exclusivo equipado com energia, resfriamento e rede independentes em uma região do Azure.

O Service Fabric dá suporte a clusters que se estendem por Zonas de Disponibilidade implantando tipos de nós que são fixados em zonas específicas. Isso garantirá a alta disponibilidade de seus aplicativos. Zonas de Disponibilidade do Azure só estão disponíveis em regiões selecionadas. Para obter mais informações, consulte [zonas de disponibilidade do Azure visão geral](https://docs.microsoft.com/azure/availability-zones/az-overview).

Os modelos de exemplo estão disponíveis: [Service Fabric modelo de zona de disponibilidade cruzada](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia recomendada para o tipo de nó primário dos clusters de Service Fabric do Azure que abrangem em Zonas de Disponibilidade
Um Cluster Service Fabric distribuído entre Zonas de Disponibilidade garante a alta disponibilidade do estado do cluster. Para abranger um Cluster Service Fabric entre zonas, você deve criar um tipo de nó primário em cada zona de disponibilidade com suporte na região. Isso distribuirá os nós de semente uniformemente em cada um dos tipos de nó primários.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* O nível de confiabilidade do cluster definido como platina.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de dimensionamento de máquinas virtuais localizado em diferentes zonas.
    * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos cinco nós (durabilidade prateada).
* Um único recurso de IP público usando SKU padrão.
* Um único recurso de Load Balancer usando SKU padrão.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de dimensionamento de máquinas virtuais.

>[!NOTE]
> A propriedade de grupo de posicionamento único do conjunto de dimensionamento de máquinas virtuais deve ser definida como true, pois Service Fabric não dá suporte a um único conjunto de dimensionamento de máquinas virtuais que abrange zonas.

 ![Arquitetura de zona de disponibilidade do Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>IP público e recurso de Load Balancer
Para habilitar a propriedade Zones em um recurso de conjunto de dimensionamento de máquinas virtuais, o balanceador de carga e o recurso de IP referenciados por esse conjunto de dimensionamento de máquinas virtuais devem estar usando um SKU *padrão* . Criar um balanceador de carga ou um recurso de IP sem a propriedade SKU criará um SKU básico, que não oferece suporte a Zonas de Disponibilidade. Um balanceador de carga SKU padrão bloqueará todo o tráfego de fora por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Não é possível fazer uma alteração in-loco do SKU nos recursos de IP público e do balanceador de carga. Se você estiver migrando de recursos existentes que têm uma SKU básica, consulte a seção migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regras NAT do conjunto de dimensionamento de máquinas virtuais
As regras NAT de entrada do balanceador de carga devem corresponder aos pools NAT do conjunto de dimensionamento de máquinas virtuais. Cada conjunto de dimensionamento de máquinas virtuais deve ter um pool de NAT de entrada exclusivo.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard SKU Load Balancer regras de saída
Standard Load Balancer e IP público Standard introduzem novas capacidades e comportamentos diferentes para a conectividade de saída quando comparados ao uso de SKUs básicos. Se você quiser a conectividade de saída ao trabalhar com SKUs padrão, deverá defini-la explicitamente com endereços IP públicos padrão ou Load Balancer públicos padrão. Para obter mais informações, consulte [conexões de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e [Standard Load Balancer do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> O modelo padrão faz referência a um NSG que permite todo o tráfego de saída por padrão. O tráfego de entrada é limitado às portas necessárias para operações de gerenciamento de Service Fabric. As regras de NSG podem ser modificadas para atender às suas necessidades.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Habilitando zonas em um conjunto de dimensionamento de máquinas virtuais
Para habilitar uma zona, em um conjunto de dimensionamento de máquinas virtuais, você deve incluir os três valores a seguir no recurso do conjunto de dimensionamento de máquinas virtuais.

* O primeiro valor é a propriedade **Zones** , que especifica em qual zona de disponibilidade o conjunto de dimensionamento de máquinas virtuais será implantado.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como true.
* O terceiro valor é a propriedade "faultDomainOverride" na extensão do conjunto de dimensionamento de máquinas virtuais Service Fabric. O valor dessa propriedade deve incluir a região e a zona em que esse conjunto de dimensionamento de máquinas virtuais será colocado. Exemplo: "faultDomainOverride": "eastus/AZ1" todos os recursos do conjunto de dimensionamento de máquinas virtuais devem ser colocados na mesma região porque os clusters do Azure Service Fabric não têm suporte entre regiões.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Habilitando vários tipos de nó primários no recurso de Cluster Service Fabric
Para definir um ou mais tipos de nó como primário em um recurso de cluster, defina a propriedade "IsPrimary" como "true". Ao implantar um Cluster Service Fabric em Zonas de Disponibilidade, você deve ter três tipos de nó em zonas distintas.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para o uso de Zonas de Disponibilidade de um cluster usando um Load Balancer de SKU básico e um IP de SKU básico
Para migrar um cluster, que estava usando um Load Balancer e IP com um SKU básico, primeiro você deve criar um recurso de Load Balancer e IP totalmente novo usando o SKU Standard. Não é possível atualizar esses recursos in-loco.

O novo LB e o IP devem ser referenciados nos novos tipos de nó de zona de disponibilidade cruzada que você gostaria de usar. No exemplo acima, três novos recursos do conjunto de dimensionamento de máquinas virtuais foram adicionados nas zonas 1, 2 e 3. Esses conjuntos de dimensionamento de máquinas virtuais referenciam o LB e o IP recém-criados e são marcados como tipos de nó primários no recurso de Cluster Service Fabric.

Para começar, será necessário adicionar os novos recursos ao modelo do Resource Manager existente. Esses recursos incluem:
* Um recurso de IP público usando SKU padrão.
* Um recurso Load Balancer usando o SKU Standard.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de dimensionamento de máquinas virtuais.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de dimensionamento de máquinas virtuais localizado em diferentes zonas.
    * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos cinco nós (durabilidade prateada).

Um exemplo desses recursos pode ser encontrado no modelo de [exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Depois que os recursos terminarem de implantar, você poderá começar a desabilitar os nós no tipo de nó primário do cluster original. À medida que os nós estiverem desabilitados, os serviços do sistema serão migrados para o novo tipo de nó primário que foi implantado na etapa anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Depois que os nós estiverem todos desabilitados, os serviços do sistema serão executados no tipo de nó primário, que é distribuído entre as zonas. Em seguida, você pode remover os nós desabilitados do cluster. Depois que os nós forem removidos, você poderá remover o IP original, Load Balancer e os recursos do conjunto de dimensionamento de máquinas virtuais.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Em seguida, você deve remover as referências a esses recursos do modelo do Resource Manager que você implantou.

A etapa final envolverá a atualização do nome DNS e do IP público.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
