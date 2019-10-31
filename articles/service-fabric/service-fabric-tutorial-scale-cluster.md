---
title: Dimensionar um cluster do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a dimensionar um Cluster Service Fabric no Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 6270237e2319c42ed30fc347b7ab9c1c2a008314
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177742"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Tutorial: Dimensionar um cluster do Service Fabric no Azure

Este tutorial é a parte três de uma série e mostra como dimensionar seu cluster existente para fora e para dentro. Quando tiver terminado, saberá como dimensionar o seu cluster e como limpar quaisquer recursos restantes.  Para obter mais informações sobre como dimensionar um cluster em execução no Azure, leia o [dimensionamento de Service Fabric clusters](service-fabric-cluster-scaling.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar e remover nós (escalar horizontalmente e reduzir horizontalmente)
> * Adicionar e remover tipos de nó (escalar horizontalmente e reduzir horizontalmente)
> * Aumentar os recursos do nó (escalar verticalmente)

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure usando um modelo
> * [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * Reduzir ou aumentar horizontalmente um cluster
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou [CLI do Azure](/cli/azure/install-azure-cli).
* Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure

## <a name="important-considerations-and-guidelines"></a>Considerações e diretrizes importantes

As cargas de trabalho do aplicativo mudam com o tempo, os serviços existentes precisam de mais (ou menos) recursos?  [Adicione ou remova nós](#add-nodes-to-or-remove-nodes-from-a-node-type) de um tipo de nó para aumentar ou diminuir os recursos de cluster.

Você precisa adicionar mais de 100 nós ao seu cluster?  Um único tipo de nó de Service Fabric/conjunto de dimensionamento não pode conter mais de 100 nós/VMs.  Para dimensionar um cluster além de 100 nós, [adicione outros tipos de nó](#add-nodes-to-or-remove-nodes-from-a-node-type).

Seu aplicativo tem vários serviços e qualquer um deles precisa ser público ou voltado para a Internet?  Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end. Nesse caso, recomendamos que você [adicione pelo menos dois tipos de nó](#add-nodes-to-or-remove-nodes-from-a-node-type) ao cluster.  

Seus serviços têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, seu aplicativo contém um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (tamanhos de VM, como D2) que têm portas abertas para a Internet. O serviço de back-end, no entanto, é de computação intensiva e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltados para a Internet. Nesse caso, recomendamos que você [adicione dois ou mais tipos de nó](#add-nodes-to-or-remove-nodes-from-a-node-type) ao cluster. Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade da Internet ou tamanho da VM. O número de VMs também pode ser dimensionado de forma independente.

Ao dimensionar um cluster do Azure, tenha em mente as seguintes diretrizes:

* Um único tipo de nó de Service Fabric/conjunto de dimensionamento não pode conter mais de 100 nós/VMs.  Para dimensionar um cluster além de 100 nós, adicione outros tipos de nó.
* Os tipos de nó primários que executam cargas de trabalho de produção devem ter um [nível de durabilidade][durability] ouro ou prata e ter sempre cinco ou mais nós.
* Os tipos de nó não primário que executam cargas de trabalho de produção com estado sempre devem ter cinco ou mais nós.
* Os tipos de nó não primário que executam cargas de trabalho de produção sem monitoração de estado sempre devem ter dois ou mais nós.
* Qualquer tipo de nó de [nível de durabilidade][durability] Gold ou prata sempre deve ter cinco ou mais nós.
* Se estiver dimensionando (removendo nós de) um tipo de nó primário, você nunca deverá diminuir o número de instâncias para menos do que o [nível de confiabilidade][reliability] requer.

Para obter mais informações, leia [diretrizes de capacidade de cluster](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportar o modelo para o grupo de recursos

Depois de criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro e configurar o grupo de recursos com êxito, exporte o modelo do Resource Manager para o grupo de recursos. Exportar o modelo permite automatizar futuras implantações do cluster e seus recursos, pois o modelo contém toda a infraestrutura completa.  Para obter mais informações sobre como exportar modelos, leia [gerenciar Azure Resource Manager grupos de recursos usando o portal do Azure](/azure/azure-resource-manager/manage-resource-groups-portal).

1. No [portal do Azure](https://portal.azure.com), vá para o grupo de recursos que contém o cluster (**sfclustertutorialgroup**, se você estiver seguindo este tutorial). 

2. No painel esquerdo, selecione **implantações**ou selecione o link em **implantações**. 

3. Selecione a implantação bem-sucedida mais recente na lista.

4. No painel esquerdo, selecione **modelo** e, em seguida, selecione **baixar** para exportar o modelo como um arquivo zip.  Salve o modelo e os parâmetros em seu computador local.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Adicionar nós ou remover nós de um tipo de nó

A colocação em escala horizontal e vertical ou dimensionamento vertical altera o número de nós no cluster. Quando você reduz ou reduz horizontalmente, adiciona mais instâncias de máquina virtual ao conjunto de dimensionamento. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exporte um modelo e arquivo de parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implantação mais recente.  Abra o arquivo *Parameters. JSON* .  Se você implantou o cluster usando o [modelo de exemplo][template] neste tutorial, há três tipos de nó no cluster e três parâmetros que definem o número de nós para cada tipo de nó: *nt0InstanceCount*, *nt1InstanceCount*e  *nt2InstanceCount*.  O parâmetro *nt1InstanceCount* , por exemplo, define a contagem de instâncias para o segundo tipo de nó e define o número de VMs no conjunto de dimensionamento de máquinas virtuais associado.

Portanto, ao atualizar o valor de *nt1InstanceCount* , você altera o número de nós no segundo tipo de nó.  Lembre-se de que você não pode dimensionar um tipo de nó para mais de 100 nós.  Os tipos de nó não primário que executam cargas de trabalho de produção com estado sempre devem ter cinco ou mais nós. Os tipos de nó não primário que executam cargas de trabalho de produção sem monitoração de estado sempre devem ter dois ou mais nós.

Se você estiver dimensionando, removendo nós de, um tipo de nó de [nível de durabilidade][durability] bronze, deverá [remover manualmente o estado desses nós](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Para a camada de durabilidade prata e ouro, essas etapas são feitas automaticamente pela plataforma.

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Salve as alterações nos arquivos *Template. JSON* e *Parameters. JSON* .  Para implantar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Ou o seguinte comando de CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Adicionar um tipo de nó ao cluster

Cada tipo de nó definido em um Cluster Service Fabric em execução no Azure é configurado como um [conjunto de dimensionamento de máquinas virtuais separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerenciado separadamente. Você pode dimensionar de forma independente cada tipo de nó para cima ou para baixo, ter diferentes conjuntos de portas abertas e usar métricas de capacidade diferentes. Você também pode alterar independentemente a SKU do sistema operacional em execução em cada nó do cluster, mas observe que não é possível ter uma combinação do Windows e Linux em execução no cluster de exemplo. Um conjunto de dimensionamento/tipo de nó único não pode conter mais de 100 nós.  Você pode dimensionar um cluster horizontalmente em mais de 100 nós Adicionando tipos de nó/conjuntos de dimensionamento adicionais. Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exporte um modelo e arquivo de parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implantação mais recente.  Abra o arquivo *Parameters. JSON* .  Se você implantou o cluster usando o [modelo de exemplo][template] neste tutorial, há três tipos de nó no cluster.  Nesta seção, você adiciona um quarto tipo de nó atualizando e implantando um modelo do Resource Manager. 

Além do novo tipo de nó, você também adiciona o conjunto de dimensionamento de máquinas virtuais associado (que é executado em uma sub-rede separada da rede virtual) e o grupo de segurança de rede.  Você pode optar por adicionar um endereço IP público novo ou existente e recursos do Azure Load Balancer para o novo conjunto de dimensionamento.  O novo tipo de nó tem um [nível de durabilidade][durability] de prata e tamanho de "Standard_D2_V2".

No arquivo *Template. JSON* , adicione os seguintes novos parâmetros:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

No arquivo *Template. JSON* , adicione as seguintes novas variáveis:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

No arquivo *Template. JSON* , adicione uma nova sub-rede ao recurso de rede virtual:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

No arquivo *Template. JSON* , adicione novos recursos de endereço IP público e balanceador de carga:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

No arquivo *Template. JSON* , adicione novos recursos de grupo de segurança de rede e conjunto de dimensionamento de máquinas virtuais.  A propriedade NodeTypeRef dentro das propriedades de extensão de Service Fabric do conjunto de dimensionamento de máquinas virtuais mapeia o tipo de nó especificado para o conjunto de dimensionamento.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

No arquivo *Template. JSON* , atualize o recurso de cluster e adicione um novo tipo de nó:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

No arquivo *Parameters. JSON* , adicione os seguintes novos parâmetros e valores:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Salve as alterações nos arquivos *Template. JSON* e *Parameters. JSON* .  Para implantar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Ou o seguinte comando de CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Remover um tipo de nó do cluster
Depois de criar um Cluster Service Fabric, você pode dimensionar horizontalmente um cluster removendo um tipo de nó (conjunto de dimensionamento de máquinas virtuais) e todos os seus nós. Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster. À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

> [!WARNING]
> O uso de Remove-AzServiceFabricNodeType para remover um tipo de nó de um cluster de produção não é recomendado para ser usado com frequência. É um comando perigoso, pois exclui o recurso do conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. 

Para remover o tipo de nó, execute o cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  O tipo de nó deve ser um [nível de durabilidade][durability] prata ou ouro o cmdlet exclui o conjunto de dimensionamento associado ao tipo de nó e leva algum tempo para ser concluído.  Em seguida, execute o cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em cada um dos nós a serem removidos, o que exclui o estado do nó e remove os nós do cluster. Se houver serviços nos nós, os serviços serão movidos primeiro para outro nó. Se o Gerenciador de cluster não conseguir localizar um nó para a réplica/serviço, a operação será atrasada/bloqueada.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Aumentar os recursos do nó 
Depois de criar um cluster de Service Fabric, você pode dimensionar um tipo de nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operacional das VMs do tipo de nó.  

> [!WARNING]
> Recomendamos que você não altere a SKU da VM de um conjunto de dimensionamento/tipo de nó, a menos que ele esteja sendo executado na durabilidade de prata ou superior. Alterar o tamanho do SKU da VM é uma operação de infraestrutura in-loco destrutiva de dados. Sem alguma capacidade de atrasar ou monitorar essa alteração, é possível que a operação possa causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem estado.

> [!WARNING]
> Recomendamos que você não altere a SKU da VM do tipo de nó primário, que é uma operação perigosa e sem suporte.  Se você precisar de mais capacidade de cluster, poderá adicionar mais instâncias de VM ou tipos de nós adicionais.  Se isso não for possível, você poderá criar um novo cluster e [restaurar o estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo.  Se isso não for possível, você poderá [alterar o SKU da VM do tipo de nó primário](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Atualizar o modelo

[Exporte um modelo e arquivo de parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implantação mais recente.  Abra o arquivo *Parameters. JSON* .  Se você implantou o cluster usando o [modelo de exemplo][template] neste tutorial, há três tipos de nó no cluster.  

O tamanho das VMs no segundo tipo de nó é definido no parâmetro *vmNodeType1Size* .  Altere o valor do parâmetro *vmNodeType1Size* de Standard_D2_V2 para [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), que dobra os recursos de cada instância de VM.

A SKU da VM para todos os três tipos de nó é definida no parâmetro *vmImageSku* .  Novamente, a alteração do SKU da VM de um tipo de nó deve ser abordada com cautela e não é recomendada para o tipo de nó primário.

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Salve as alterações nos arquivos *Template. JSON* e *Parameters. JSON* .  Para implantar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Ou o seguinte comando de CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar e remover nós (escalar horizontalmente e reduzir horizontalmente)
> * Adicionar e remover tipos de nó (escalar horizontalmente e reduzir horizontalmente)
> * Aumentar os recursos do nó (escalar verticalmente)

Em seguida, avance para o tutorial seguinte para saber como atualizar o runtime de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Adicionar e remover tipos de nó (escalar horizontalmente e reduzir horizontalmente)
> * Aumentar os recursos do nó (escalar verticalmente)

Em seguida, avance para o tutorial seguinte para saber como atualizar o runtime de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
