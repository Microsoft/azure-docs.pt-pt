---
title: Implementar um cluster do Azure Service Fabric em zonas de disponibilidade | Documentos da Microsoft
description: Saiba como criar um cluster do Azure Service Fabric em zonas de disponibilidade.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077459"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implementar um cluster do Azure Service Fabric em zonas de disponibilidade
As zonas de disponibilidade no Azure é uma oferta que protege os seus aplicativos e dados de falhas de datacenter de elevada disponibilidade. Zona de disponibilidade é uma localização física exclusiva equipada com energia, refrigeração e redes dentro de uma região do Azure.

O Service Fabric suporta clusters que abrangem em zonas de disponibilidade ao implementar os tipos de nós que são afixados a zonas específicas. Isto irá garantir a elevada disponibilidade das suas aplicações. As zonas de disponibilidade do Azure só estão disponíveis nas regiões selecionadas. Para obter mais informações, consulte [descrição geral das zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Modelos de exemplo estão disponíveis: [Modelo de zona de disponibilidade em várias de Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Recomendado topologia para o tipo de nó primário de expansão por zonas de disponibilidade de clusters de Azure Service Fabric
Um cluster do Service Fabric distribuído por zonas de disponibilidade garante elevada disponibilidade do Estado do cluster. Expanda o cluster do Service Fabric em zonas, tem de criar um tipo de nó principal em cada zona de disponibilidade suportadas pela região. Isto irá distribuir uniformemente nós de semente em cada um dos tipos de nó primário.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* Definir o nível de fiabilidade de cluster para platina.
* Três tipos de nó marcado como primários.
    * Cada tipo de nó deve ser mapeado para o seu próprio conjunto de dimensionamento de máquina virtual localizado em zonas diferentes.
    * Cada conjunto de dimensionamento de máquina virtual deve ter, pelo menos, cinco nós (durabilidade de prata).
* Um único recurso de IP público com o Standard SKU.
* Um recurso de Balanceador de carga único com o Standard SKU.
* Um NSG referenciado por sub-rede na qual implementar seus conjuntos de dimensionamento de máquina virtual.

>[!NOTE]
> O conjunto de dimensionamento de máquinas virtuais propriedade do grupo de colocação único tem de ser definida como true, uma vez que o Service Fabric não suporta um conjunto de dimensionamento de máquina virtual única que abrange as zonas.

 ![Arquitetura de zona de disponibilidade do Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>IP público e o recurso do Balanceador de carga
Para ativar as zonas de propriedade numa escala de máquina virtual definida recursos, o Balanceador de carga e o recurso IP referenciado por esse conjunto de dimensionamento de máquina virtual têm ambos de utilizar um *padrão* SKU. Criar um balanceador de carga ou o recurso IP sem a propriedade SKU criará um SKU básico, o que não suporta zonas de disponibilidade. Um balanceador de carga de Standard SKU irá bloquear todo o tráfego de fora, por padrão. para permitir que fora de tráfego, tem de ser implementado um NSG à sub-rede.

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
> Não é possível fazer uma alteração no local do SKU nos recursos de Balanceador de carga e IP públicos. Se estiver a migrar a partir de recursos existentes que têm um SKU básico, consulte a secção de migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regras NAT de conjunto de dimensionamento de máquina virtual
O Balanceador de carga regras NAT de entrada devem corresponder os conjuntos NAT do conjunto de dimensionamento de máquina virtual. Cada conjunto de dimensionamento de máquina virtual tem de ter um conjunto exclusivo de NAT de entrada.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regras de saída de Balanceador de carga de SKU padrão
Standard Balanceador de carga e o IP público Standard apresentam novas capacidades e comportamentos diferentes para conectividade de saída quando em comparação ao uso SKUs básica. Se quiser conectividade de saída ao trabalhar com Standard SKUs, tem de defini-lo, com endereços IP públicos Standard ou Balanceador de carga Standard público explicitamente. Para obter mais informações, consulte [ligações de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e [Balanceador de carga Standard do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> O modelo padrão faz referência a um NSG que permite que todo o tráfego de saída, por predefinição. Tráfego de entrada está limitado às portas que são necessárias para operações de gestão do Service Fabric. As regras do NSG podem ser modificadas para satisfazer os seus requisitos.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Ativar zonas numa escala de máquina virtual definido
Para ativar uma zona, numa escala de máquina virtual definida, deve incluir os seguintes três valores no recurso de conjunto de dimensionamento de máquina virtual.

* O primeiro valor é o **zonas** propriedade, que especifica o conjunto de dimensionamento de máquina virtual será implementado em qual zona de disponibilidade.
* O segundo valor é a propriedade de "singlePlacementGroup", que tem de ser definida como true.
* O terceiro valor é a propriedade de "faultDomainOverride" na extensão de conjunto de dimensionamento da máquina virtual de Service Fabric. O valor para esta propriedade deve incluir a região e zona em que este conjunto de dimensionamento de máquina virtual será colocado. Exemplo: "faultDomainOverride": "eastus/az1" conjunto de dimensionamento de máquinas virtuais de todos os recursos devem ser colocados na mesma região, porque o Azure Service Fabric clusters não tem cruzada suporte de região.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Ativar vários tipos de nó primário no recurso de Cluster do Service Fabric
Para definir um ou mais tipos de nó principal num recurso de cluster como, defina a propriedade de "isPrimary" como "true". Ao implementar um cluster do Service Fabric em zonas de disponibilidade, deve ter três tipos de nó em zonas diferentes.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para utilizar zonas de disponibilidade de um cluster com um balanceador de carga de SKU básico e um IP de SKU básico
Para migrar um cluster, o que estava a utilizar um balanceador de carga e o IP com um SKU básico, primeiro tem de criar um recurso de Balanceador de carga e IP totalmente novo com o SKU standard. Não é possível atualizar estes recursos no local.

O novo LB e IP devem ser referenciado nos novos tipos de nó de zona de disponibilidade em vários que deseja usar. No exemplo acima, três dimensionamento de máquinas virtuais de novos recursos do conjunto de foram adicionados em zonas de 1,2 e 3. Estes dimensionamento de máquinas virtuais referência de conjuntos do LB e IP recém-criado e estão assinalados como tipos de nó primário no recurso de Cluster do Service Fabric.

Para começar, terá de adicionar os novos recursos ao seu modelo de Gestor de recursos existente. Estes recursos incluem:
* Um recurso IP público com o Standard SKU.
* Um recurso do Balanceador de carga com o Standard SKU.
* Um NSG referenciado por sub-rede na qual implementar seus conjuntos de dimensionamento de máquina virtual.
* Três tipos de nó marcado como primários.
    * Cada tipo de nó deve ser mapeado para o seu próprio conjunto de dimensionamento de máquina virtual localizado em zonas diferentes.
    * Cada conjunto de dimensionamento de máquina virtual deve ter, pelo menos, cinco nós (durabilidade de prata).

Um exemplo desses recursos pode ser encontrado na [modelo de exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Assim que os recursos tem concluído a implantação, pode começar a desativar os nós no tipo de nó principal do original cluster. Como os nós estão desativados, os serviços do sistema serão migrados para o novo tipo de nó principal que tinha sido implementado no passo acima.

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

Assim que os nós estão todos desativados, os serviços do sistema serão executados sobre o tipo de nó primário, o que é distribuído por todos zonas. Em seguida, pode remover os nós desativado do cluster. Assim que os nós são removidos, pode remover o IP original, o Balanceador de carga, e recursos do conjunto de dimensionamento de máquina virtual.

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

Em seguida, deve remover as referências a estes recursos do modelo do Resource Manager que tinha implementado.

A última etapa envolverá a atualizar o nome DNS e o IP público.

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
