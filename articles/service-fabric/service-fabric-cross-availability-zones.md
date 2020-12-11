---
title: Implementar um cluster em zonas de disponibilidade
description: Saiba como criar um cluster de tecido de serviço Azure em zonas de disponibilidade.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d8e4a9201c14e71520bd58ff1017b700ca47fa21
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109827"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implementar um cluster de tecido de serviço Azure em zonas de disponibilidade
Availability Zones in Azure é uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas do datacenter. Uma Zona de Disponibilidade é um local físico único equipado com potência independente, arrefecimento e networking dentro de uma região de Azure.

O Service Fabric suporta clusters que se estendem por zonas de disponibilidade, implantando tipos de nó que estão presos a zonas específicas. Isto garantirá uma elevada disponibilidade das suas aplicações. As Zonas de Disponibilidade Azure só estão disponíveis em regiões selecionadas. Para mais informações, consulte [a Visão geral das Zonas de Disponibilidade do Azure.](../availability-zones/az-overview.md)

Modelos de amostra estão disponíveis: [Modelo de zona de disponibilidade cruzada de tecido de serviço](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia recomendada para o tipo de nó primário de clusters de tecido de serviço Azure abrangendo as zonas de disponibilidade
Um cluster de tecido de serviço distribuído por Zonas de Disponibilidade garante uma elevada disponibilidade do estado do cluster. Para abranger um cluster de Tecido de Serviço através de zonas, você deve criar um tipo de nó primário em cada Zona de Disponibilidade suportada pela região. Isto distribuirá os nós de sementes uniformemente em cada um dos tipos de nó primário.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* O nível de fiabilidade do cluster definido para Platinum.
* Três tipos de nó marcados como primários.
    * Cada nó de nó deve ser mapeado para o seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de balança de máquina virtual deve ter pelo menos cinco nós (Durabilidade prateada).
* Um único recurso IP público utilizando o SKU Standard.
* Um recurso de balançador de carga único utilizando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual implementa os conjuntos de escala de máquina virtual.

>[!NOTE]
> A escala de máquina virtual definida como propriedade de grupo de colocação única deve ser definida como verdadeira, uma vez que o Service Fabric não suporta um único conjunto de escala de máquina virtual que se estende por zonas.

 ![Arquitetura da zona de disponibilidade de tecido de serviço Azure][sf-architecture]

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>Recurso público de equilibrador de IP e carga
Para permitir a propriedade das zonas num recurso de escala de máquina virtual, o balanceador de carga e o recurso IP referenciados por esse conjunto de escala de máquina virtual devem estar ambos a utilizar um SKU *Standard.* A criação de um equilibrador de carga ou de um recurso IP sem a propriedade SKU criará um SKU Básico, que não suporta Zonas de Disponibilidade. Um balanceador de carga Standard SKU bloqueará todo o tráfego do exterior por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

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
> Não é possível fazer uma alteração in-place do SKU no PI público e recursos de balançadores de carga. Se estiver a migrar de recursos existentes que possuam um SKU Básico, consulte a secção de migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Escala de máquina virtual definir regras NAT
As regras NAT de entrada do balançador de carga devem coincidir com as piscinas NAT do conjunto de balanças de máquina virtual. Cada conjunto de balança de máquina virtual deve ter uma piscina NAT de entrada única.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regras de saída standard SKU Load Balancer
O Balancer de Carga Padrão e o IP público standard introduzem novas habilidades e comportamentos diferentes para a conectividade de saída quando comparados com o uso de SKUs básicos. Se quiser conectividade de saída ao trabalhar com SKUs padrão, deve defini-lo explicitamente com endereços IP públicos padrão ou Balancer de Carga pública Padrão. Para obter mais informações, consulte [as ligações outbound](../load-balancer/load-balancer-outbound-connections.md) e [o balançador de carga padrão Azure](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> O modelo padrão refere-se a um NSG que permite todo o tráfego de saída por padrão. O tráfego de entrada está limitado às portas que são necessárias para operações de gestão de Tecidos de Serviço. As regras da NSG podem ser modificadas para satisfazer os seus requisitos.

>[!NOTE]
> Qualquer cluster de tecido de serviço que utilize um SKU SLB standard precisa de garantir que cada tipo de nó tem uma regra que permite o tráfego de saída na porta 443. Isto é necessário para completar a configuração do cluster, e qualquer implementação sem tal regra falhará.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zonas de habilitação em um conjunto de escala de máquina virtual
Para ativar uma zona, num conjunto de escala de máquina virtual deve incluir os seguintes três valores no recurso conjunto de escala de máquina virtual.

* O primeiro valor é a propriedade **zonas,** que especifica qual a Zona de Disponibilidade para a qual será implantado o conjunto de escala de máquina virtual.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como verdadeira.
* O terceiro valor é a propriedade "faultDomainOverride" na extensão de escala virtual de máquina do Tecido de Serviço. O valor desta propriedade deve incluir apenas a zona em que este conjunto de escala de máquina virtual será colocado. Exemplo: "faultDomdomainOverride": "az1" Todos os recursos de escala de máquina virtual devem ser colocados na mesma região porque os clusters de tecido de serviço Azure não têm suporte de região cruzada.

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
        "publisher": "Microsoft.Azure.ServiceFabric",
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
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Ativar vários tipos de nó primários no recurso de cluster de tecido de serviço
Para definir um ou mais tipos de nó como primários num recurso de cluster, desajuste a propriedade "isPrimary" para "verdadeiro". Ao implantar um cluster de tecido de serviço em zonas de disponibilidade, deverá ter três tipos de nós em zonas distintas.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para usar zonas de disponibilidade de um cluster usando um Balancer de Carga SKU Básico e um IP SKU Básico
Para migrar um cluster, que estava a utilizar um Balanceador de Carga e IP com um SKU básico, primeiro deve criar um balanceador de carga inteiramente novo e recurso IP utilizando o SKU padrão. Não é possível atualizar estes recursos no local.

Os novos LB e IP devem ser referenciados nos novos tipos de nó de Zona de Disponibilidade cruzada que gostaria de utilizar. No exemplo acima, foram adicionados recursos de três novas escalas de máquinas virtuais nas zonas 1,2 e 3. Estes conjuntos de escala de máquina virtual referem-se ao LB e IP recém-criados e são marcados como tipos de nó primário no Recurso de Cluster de Tecido de Serviço.

Para começar, terá de adicionar os novos recursos ao modelo de Gestor de Recursos existente. Estes recursos incluem:
* Um recurso IP público utilizando o SKU Standard.
* Um recurso de balançador de carga utilizando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual implementa os conjuntos de escala de máquina virtual.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para o seu próprio conjunto de escala de máquina virtual localizado em diferentes zonas.
    * Cada conjunto de balança de máquina virtual deve ter pelo menos cinco nós (Durabilidade prateada).

Um exemplo destes recursos pode ser encontrado no modelo de [amostra.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Uma vez que os recursos tenham terminado a implantação, pode começar a desativar os nós no tipo de nó primário do cluster original. À medida que os nós são desativados, os serviços do sistema migrarão para o novo tipo de nó primário que tinha sido implantado no degrau acima.

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

Uma vez que os nós são todos desactivdos, os serviços do sistema estarão funcionando no tipo de nó primário, que é espalhado por zonas. Em seguida, pode remover os nós desativados do cluster. Uma vez removidos os nós, pode remover os recursos de definição de IP, Load Balancer e de escala de máquina virtual.

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

Em seguida, deve remover as referências a estes recursos do modelo de Gestor de Recursos que tinha implementado.

O passo final passará pela atualização do nome DNS e ip público.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Pré-visualização) Ativar várias zonas de disponibilidade em conjunto de escala de máquina virtual única

A solução anteriormente mencionada utiliza um nóType por AZ. A seguinte solução permitirá aos utilizadores implantar 3 AZ's no mesmo nóType.

O modelo completo da amostra está presente [aqui.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Arquitetura da zona de disponibilidade de tecido de serviço Azure][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Zonas de configuração em um conjunto de escala de máquina virtual
Para ativar zonas num conjunto de escala de máquina virtual, deve incluir os seguintes três valores no recurso conjunto de escala de máquina virtual.

* O primeiro valor é a propriedade **zonas,** que especifica as Zonas de Disponibilidade presentes no conjunto de escala de máquina virtual.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como verdadeira.
* O terceiro valor é "zoneBalance" e é opcional, o que garante um equilíbrio estrito da zona se definido como verdadeiro. Leia sobre [a zonaBalancing](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing).
* As sobreposições FaultDomain e UpgradeDomain não são necessárias para serem configuradas.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": false
    }
}
```

>[!NOTE]
> * **Os clusters SF devem ter pelo menos um nó de nó primário. Durabilidade O nível de nó de nó primário deve ser prateado ou superior.**
> * O conjunto de escala de máquina virtual AZ abrangendo deve ser configurado com pelo menos 3 zonas de disponibilidade, independentemente da durabilidadeLevel.
> * A AZ abrangendo a balança de máquina virtual definida com durabilidade prateada (ou superior), deve ter pelo menos 15 VMs.
> * A AZ abrangendo a balança de máquina virtual definida com durabilidade de Bronze, deve ter pelo menos 6 VMs.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Habilitando o suporte para várias zonas no nó de tecido de serviço
O nó de tecido de serviçoType deve ser ativado para suportar várias zonas de disponibilidade.

* O primeiro valor é **multipleSSeabilityZones** que devem ser definidos como verdadeiros para o nóType.
* O segundo valor é **sfZonalUpgradeMode** e é opcional. Esta propriedade não pode ser modificada se um nó detipo com vários AZ's já estiver presente no cluster.
      A propriedade controla o agrupamento lógico de VMs em domínios de upgrade.
          Se o valor for definido como falso (modo plano): os VMs sob o tipo de nó serão agrupados em UD ignorando a informação da zona em 5 UDs.
          Se o valor for omitido ou definido para verdadeiro (modo hierárquico): os VMs serão agrupados para refletir a distribuição zonal em até 15 UDs. Cada uma das 3 zonas terá 5 UDs.
          Esta propriedade apenas define o comportamento de upgrade para aplicação ServiceFabric e atualizações de código. As atualizações de escala de máquinas virtuais subjacentes continuarão paralelas em todos os AZ's.
      Esta propriedade não terá qualquer impacto na distribuição de UD para tipos de nó que não têm várias zonas ativadas.
* O terceiro valor é **vmssZonalUpgradeMode = Paralelo.** Esta é uma propriedade *obrigatória* a ser configurada no cluster, se for adicionado um nó com várias AZs. Esta propriedade define o modo de upgrade para as atualizações de escala de máquina virtual que irão acontecer em paralelo em todos os AZ's de uma só vez.
      Neste momento, esta propriedade só pode ser definida paralelamente.
* A apiversão de recursos do cluster de tecido de serviço deve ser "2020-12-01-pré-visualização" ou superior.
* A versão do código de cluster deve ser "7.2.445" ou superior.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Os Recursos De Equilíbrio de IP e carga pública devem utilizar o SKU Standard, conforme descrito anteriormente no artigo.
> * A propriedade "multipleAvailabilityZones" no nóType só pode ser definida no momento da criação do nodeType e não pode ser modificada mais tarde. Assim, os nótipos existentes não podem ser configurados com esta propriedade.
> * Quando o "HierarchicalUpgradeDomain" for omitido ou definido como verdadeiro, as implementações de cluster e aplicação serão mais lentas, uma vez que existem mais domínios de atualização no cluster. É importante ajustar corretamente os intervalos de tempo da política de atualização para incorporar durante a duração do tempo de atualização de 15 domínios de atualização.
> * Recomenda-se definir o nível de fiabilidade do cluster para platinum para garantir que o cluster sobrevive ao cenário de uma zona para baixo.

>[!NOTE]
> Para as melhores práticas recomendamos que o HierarchicalUpgradeDomain seja definido para verdadeiro ou omitido. A implantação seguirá a distribuição zonal de VMs com impacto numa menor quantidade de réplicas e/ou instâncias tornando-as mais seguras.
> Use hierarquiaUpgradeDomain definido para falso se a velocidade de implantação for uma prioridade ou apenas a carga de trabalho apátrida for executado no tipo de nó com vários AZ's. Isto resultará na caminhada da UD para acontecer paralelamente em todos os AZ's.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migração para o tipo de nó com múltiplas Zonas de Disponibilidade
Para todos os cenários de migração, um novo nóType precisa de ser adicionado que terá múltiplas zonas de disponibilidade suportadas. Um nótype existente não pode ser migrado para suportar várias zonas.
O artigo [aqui](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-primary-node-type ) captura os passos detalhados de adicionar um novo nóType e também adicionar os outros recursos necessários para o novo nóType como os recursos IP e LB. O mesmo artigo também descreve agora a retirada do nótype existente após o nó deType com várias zonas de Disponibilidade ser adicionado ao cluster.

* Migração a partir de um nóType que está a utilizar recursos básicos LB e IP: Isto já está descrito [aqui](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) para a solução com um nó por AZ. 
    Para o novo tipo de nó, a única diferença é que há apenas 1 conjunto de balança de máquina virtual e 1 nodetipo para todos os AZ's em vez de 1 cada AZ.
* Migração a partir de um nóType que está a utilizar os recursos Standard SKU LB e IP com NSG: Siga o mesmo procedimento acima descrito com a exceção de que não há necessidade de adicionar novos recursos LB, IP e NSG, e os mesmos recursos podem ser reutilizados no novo nóType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
