---
title: Implementar tipos de nó de nó apátrida num cluster de tecido de serviço
description: Aprenda a criar e implementar tipos de nódoaço apátrida no cluster de tecidos Azure Service.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: eb19005019a6e4e878f6b0bd6a145048d4a2804c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563781"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>Implementar um cluster de tecido de serviço Azure com tipos de nó apenas apátridas (Pré-visualização)
Os tipos de nó de tecido de serviço vêm com a presunção inerente de que em algum momento, os serviços estatais podem ser colocados nos nós. Os tipos de nó apátrida relaxam esta suposição para um tipo de nó, permitindo assim que o tipo de nó utilize outras funcionalidades, tais como operações de escala mais rápida, suporte para upgrades automáticos de SO na durabilidade de Bronze e escala para mais de 100 nós num único conjunto de escala de máquina virtual.

* Os tipos de nó primário não podem ser configurados para serem apátridas
* Os tipos de nó apátridas só são suportados com níveis de durabilidade de bronze
* Os tipos de nó apátridas só são suportados na versão 7.1.409 ou superior do sistema service fabric.


Modelos de amostra estão disponíveis: Modelo de [tipos de node apátrida de tecido de serviço](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Habilitando tipos de nódoaço apátridas no cluster de tecido de serviço
Para definir um ou mais tipos de nó como apátridas num recurso de cluster, desaprote a propriedade **isStateless** para "verdadeiro". Ao implantar um cluster de tecido de serviço com tipos de nó apátridas, lembre-se de ter pelo menos um tipo de nó primário no recurso cluster.

* A apiversão de recursos do cluster de tecido de serviço deve ser "2020-12-01-pré-visualização" ou superior.

```json
{
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
        "isStateless": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Configurar a escala de máquina virtual definida para os tipos de nódoas estepátrio
Para ativar os tipos de nóles apátridas, deve configurar o recurso de escala de máquina virtual subjacente da seguinte forma:

* O valor  **único Propriedade DoPlacementGroup,** que deve ser definido como **falso** se precisar de escalar para mais de 100 VMs.
* O modo de atualização do conjunto de **escalaPolicy** **deve** ser definido para **rolling**.
* O modo de atualização de rolamento requer extensão de saúde da aplicação ou sondas de saúde configuradas. Configure a sonda de saúde com configuração padrão para os tipos de nó apátrida, conforme sugerido abaixo. Uma vez implementadas as aplicações para o tipo de nó, as portas de extensão health probe/health podem ser alteradas para monitorizar a saúde da aplicação.

>[!NOTE]
> É necessário que a contagem de domínio de falha da plataforma seja atualizada para 5 quando um tipo de nó apátrida é apoiado por um conjunto de escala de máquina virtual que se estende por várias zonas. Consulte este [modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) para mais detalhes.
> 
> **plataformaFaultDomainCount:5**
```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
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
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>Recurso público de equilibrador de IP e carga
Para permitir a escala de mais de 100 VMs num recurso de escala de máquina virtual, o equilibrador de carga e o recurso IP referenciados por esse conjunto de balanças de máquinas virtuais devem estar ambos a utilizar um SKU *Standard.* A criação de um equilibrador de carga ou recurso IP sem a propriedade SKU criará um SKU Básico, que não suporta a escala para mais de 100 VMs. Um balanceador de carga Standard SKU bloqueará todo o tráfego do exterior por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

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



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para usar tipos de nódoas apátridas de um cluster utilizando um Balanceador de Carga SKU Básico e um IP SKU Básico
Para todos os cenários de migração, é necessário adicionar um novo tipo de nó só apátrida. O tipo de nó existente não pode ser migrado para ser apenas apátrida.

Para migrar um cluster, que estava a utilizar um Balanceador de Carga e IP com um SKU básico, primeiro deve criar um balanceador de carga inteiramente novo e recurso IP utilizando o SKU padrão. Não é possível atualizar estes recursos no local.

Os novos LB e IP devem ser referenciados nos novos tipos de nódoas estepáus que gostaria de utilizar. No exemplo acima, um novo conjunto de conjuntos de máquinas virtuais é adicionado para ser usado para tipos de nódoaço apátrida. Estes conjuntos de escala de máquina virtual referem-se ao LB e IP recém-criados e são marcados como tipos de nó apátridas no Recurso de Cluster de Tecido de Serviço.

Para começar, terá de adicionar os novos recursos ao modelo de Gestor de Recursos existente. Estes recursos incluem:
* Um recurso IP público utilizando o SKU Standard.
* Um recurso de balançador de carga utilizando o SKU padrão.
* Um NSG referenciado pela sub-rede na qual implementa os conjuntos de escala de máquina virtual.

Uma vez que os recursos tenham terminado de ser implantados, pode começar a desativar os nós no tipo de nó que pretende remover do cluster original.

>[!NOTE]
> Durante a utilização de AutoScaling com nóótipos apátridas com Durabilidade de Bronze, após a operação de escala, o estado do nó não é automaticamente limpo. Para limpar o Nó de Nó para baixo durante a AutoScale, é aconselhável utilizar o [Serviço de Apoio ao Sistema de Assistência autossacalista.](https://github.com/Azure/service-fabric-autoscale-helper)

## <a name="next-steps"></a>Passos seguintes 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Tipos de nós e conjuntos de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

