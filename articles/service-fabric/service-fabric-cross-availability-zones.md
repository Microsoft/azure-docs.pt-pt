---
title: Implementar um cluster através de Zonas de Disponibilidade
description: Aprenda a criar um cluster de tecido de serviço Azure em zonas de disponibilidade.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609983"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implementar um cluster de tecido de serviço Azure em zonas de disponibilidade
As Zonas de Disponibilidade em Azure são uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. Uma Zona de Disponibilidade é uma localização física única equipada com potência independente, arrefecimento e networking dentro de uma região do Azure.

O Serviço Fabric suporta clusters que se estendem por zonas de disponibilidade, implantando tipos de nó que estão presos a zonas específicas. Isto garantirá uma elevada disponibilidade das suas aplicações. As Zonas de Disponibilidade Azure só estão disponíveis em regiões selecionadas. Para mais informações, consulte a visão geral das Zonas de [Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Modelos de amostra estão disponíveis: Modelo de zona de [disponibilidade transversal de tecido de serviço](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia recomendada para o tipo de nó primário de clusters de tecido de serviço Azure que se estendem por zonas de disponibilidade
Um cluster de tecido de serviço distribuído por Zonas de Disponibilidade garante uma elevada disponibilidade do estado de cluster. Para abranger um cluster de tecido de serviço através de zonas, você deve criar um tipo de nó primário em cada Zona de Disponibilidade suportada pela região. Isto distribuirá os nós de sementes uniformemente em cada um dos tipos primários do nó.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* O nível de fiabilidade do cluster definido para platinum.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para o seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de escala de máquina virtual deve ter pelo menos cinco nós (Durabilidade prateada).
* Um único recurso ip público usando o SKU padrão.
* Um recurso de equilíbrio de carga única utilizando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual implanta os conjuntos de escala de máquina virtual.

>[!NOTE]
> A propriedade de grupo de colocação de uma única escala de máquina virtual deve ser definida como verdadeira, uma vez que o Tecido de Serviço não suporta um único conjunto de escala de máquina virtual que se estende por zonas.

 ![Arquitetura da zona de disponibilidade de tecido de serviço Azure][sf-architecture]

## <a name="networking-requirements"></a>Requisitos de networking
### <a name="public-ip-and-load-balancer-resource"></a>Recursos públicos de IP e Balancer de Carga
Para ativar a propriedade das zonas num recurso conjunto de conjunto de máquinas virtuais, o balanceador de carga e o recurso IP referenciados por esse conjunto de escala de máquina virtual devem estar ambos a utilizar um SKU *Padrão.* A criação de um balanceor de carga ou recurso IP sem a propriedade SKU criará um SKU Básico, que não suporta Zonas de Disponibilidade. Um balancedor de carga SKU Standard bloqueará todo o tráfego do exterior por defeito; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

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
> Não é possível fazer uma alteração de SKU no PI público e recursos de equilibrista de carga. Se está a migrar dos recursos existentes que têm um SKU básico, consulte a secção de migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regras de nat de escala de máquina virtual
As regras de entrada do equilibrador de carga na NAT devem coincidir com as piscinas NAT do conjunto de escala de máquina virtual. Cada conjunto de escala de máquina virtual deve ter uma piscina NAT de entrada única.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regras padrão de saída do Balancer de Carga SKU
O Standard Load Balancer e o Standard Public IP introduzem novas habilidades e comportamentos diferentes para a conectividade de saída quando comparados com a utilização de SKUs Básicos. Se pretender conectividade de saída ao trabalhar com as SKUs Standard, deve defini-la explicitamente com endereços IP públicos padrão ou um balancer de carga público padrão. Para mais informações, consulte [as ligações de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e o Equilíbrio de Carga Padrão [Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> O modelo padrão refere-se a um NSG que permite todo o tráfego de saída por defeito. O tráfego de entrada está limitado aos portos que são necessários para operações de gestão de tecidos de serviço. As regras da NSG podem ser modificadas para satisfazer os seus requisitos.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Permitindo zonas num conjunto de escala de máquina virtual
Para ativar uma zona, num conjunto de escala de máquina virtual deve incluir os seguintes três valores no recurso conjunto de escala de máquina virtual.

* O primeiro valor é a propriedade das **zonas,** que especifica a zona de disponibilidade para onde será implantado o conjunto de escala de máquina virtual.
* O segundo valor é o imóvel "singlePlacementGroup", que deve ser definido como verdadeiro.
* O terceiro valor é a propriedade "faultDomainOverride" na extensão de conjunto de máquinas virtual de tecido de serviço. O valor desta propriedade deve incluir a região e a zona em que este conjunto de escala de máquina virtual será colocado. Exemplo: "faultDomainOverride": "eastus/az1" Todos os recursos conjuntos de máquinas virtuais devem ser colocados na mesma região porque os clusters azure service Fabric não têm suporte transversal à região.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Habilitar vários tipos de nó primário no recurso cluster de tecido de serviço
Para definir um ou mais tipos de nó como primárionum recurso de cluster, coloque a propriedade "isPrimary" como "verdadeira". Ao implantar um cluster de tecido de serviço em zonas de disponibilidade, deve ter três tipos de nó em zonas distintas.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para usar Zonas de Disponibilidade de um cluster usando um Equilíbrio de Carga SKU Básico e um IP Básico SKU
Para migrar um cluster, que estava a utilizar um Balancer de Carga e IP com um SKU básico, tem primeiro de criar um novo recurso load Balancer e IP utilizando o SKU padrão. Não é possível atualizar estes recursos em vigor.

Os novos LB e IP devem ser referenciados nos novos tipos de nódaa da Zona de Disponibilidade cruzada que gostaria de utilizar. No exemplo acima, foram adicionados três novos recursos conjuntos de máquinas virtuais nas zonas 1,2 e 3. Estes conjuntos de escala de máquina virtual referenciam o LB e o IP recém-criados e são marcados como tipos de nó primário no Recurso de Cluster de Tecidos de Serviço.

Para começar, terá de adicionar os novos recursos ao seu modelo de Gestor de Recursos existente. Estes recursos incluem:
* Um recurso IP público usando o Standard SKU.
* Um recurso de equilíbrio de carga utilizando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual implanta os conjuntos de escala de máquina virtual.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para o seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de escala de máquina virtual deve ter pelo menos cinco nós (Durabilidade prateada).

Um exemplo destes recursos pode ser encontrado no modelo da [amostra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Uma vez que os recursos tenham terminado a implantação, você pode começar a desativar os nós no tipo de nó primário do cluster original. À medida que os nós são desativados, os serviços do sistema migrarão para o novo tipo de nó primário que tinha sido implantado no degrau acima.

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

Uma vez que os nós estão todos desativados, os serviços do sistema estarão funcionando no tipo de nó primário, que é espalhado por zonas. Em seguida, pode remover os nós desativados do cluster. Uma vez removidos os nós, pode remover os recursos conjuntos originais de IP, Load Balancer e virtual machine scale.

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

Em seguida, deve remover as referências a estes recursos do modelo de Gestor de Recursos que tinha implantado.

O passo final passará pela atualização do nome DNS e do IP público.

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
