---
title: Dimensionar um cluster do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, saiba como dimensionar um cluster do Service Fabric no Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 315e3c05e23d68edb701e705fd1ed02a840a8936
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226815"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Tutorial: Dimensionar um cluster do Service Fabric no Azure

Este tutorial é a parte três de uma série e mostra-lhe como dimensionar o cluster horizontalmente no. Quando tiver terminado, saberá como dimensionar o seu cluster e como limpar quaisquer recursos restantes.  Para obter mais informações sobre como dimensionar um cluster em execução no Azure, leia [clusters do Service Fabric dimensionamento](service-fabric-cluster-scaling.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar e remover nós (ampliar e reduzir horizontalmente)
> * Adicionar e remover tipos de nós (ampliar e reduzir horizontalmente)
> * Recursos de nó de aumento (aumento vertical)

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) no Azure através de um modelo
> * [Monitorizar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * Reduzir ou aumentar horizontalmente um cluster
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [módulo do Azure PowerShell na versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ou a [CLI do Azure ](/cli/azure/install-azure-cli).
* Criar um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) no Azure

## <a name="important-considerations-and-guidelines"></a>Considerações importantes e diretrizes

Cargas de trabalho de aplicação alteram ao longo do tempo, os serviços existentes precisa recursos mais (ou menos)?  [Adicionar ou remover nós](#add-nodes-to-or-remove-nodes-from-a-node-type) de um tipo de nó para aumentar ou diminuir recursos do cluster.

Precisa adicionar mais do que 100 nós ao cluster?  Um único conjunto de dimensionamento/tipo de nó do Service Fabric não pode conter mais de 100 nós/VMs.  Dimensionar um cluster para além de 100 nós, [adicionar tipos de nó adicional](#add-nodes-to-or-remove-nodes-from-a-node-type).

A aplicação tem vários serviços, e qualquer um deles precisa de ser público ou com acesso à internet?  Os aplicativos típicos de conter um serviço de gateway de front-end que recebe a entrada de um cliente e um ou mais serviços back-end que se comunicam com os serviços front-end. Neste caso, recomendamos que [adicione, pelo menos, dois tipos de nó](#add-nodes-to-or-remove-nodes-from-a-node-type) ao cluster.  

Seus serviços tem necessidades de infraestrutura diferentes, como o maior de RAM ou superior ciclos de CPU? Por exemplo, a aplicação tiver um serviço de front-end e um serviço de back-end. O serviço de front-end pode executar em VMs menores (tamanhos de VM, como D2) que têm portas abertas para a internet. O serviço de back-end, no entanto, está intensivas de computação e tem de ser executadas nas VMs maiores (com tamanhos de VM, como D4, D6, D15) que não são internet a ter. Neste caso, recomendamos que [adicionar dois ou mais tipos de nó](#add-nodes-to-or-remove-nodes-from-a-node-type) ao seu cluster. Isso permite que cada tipo de nó ter propriedades distintas, como conectividade à internet ou de tamanho da VM. O número de VMs pode ser dimensionado de forma independente, bem.

Quando dimensionar um cluster do Azure, mantenha as seguintes diretrizes em mente:

* Um único conjunto de dimensionamento/tipo de nó do Service Fabric não pode conter mais de 100 nós/VMs.  Dimensionar um cluster para além de 100 nós, adicione tipos de nó adicional.
* Executar cargas de trabalho de produção de tipos de nó primário devem ter uma [nível de durabilidade] [ durability] de Gold ou Silver e sempre ter cinco ou mais nós.
* executar cargas de trabalho de produção com monitorização de estado de tipos de nó não primário sempre devem ter cinco ou mais nós.
* executar cargas de trabalho de produção sem monitoração de estado de tipos de nó não primário devem ter sempre dois ou mais nós.
* Qualquer tipo de nó de [nível de durabilidade] [ durability] de Gold ou Silver sempre deve ter cinco ou mais nós.
* Se reduzir horizontalmente (remover nós de) um tipo de nó principal, nunca deve diminuir o número de instâncias para menos do que o [nível de fiabilidade] [ reliability] requer.

Para obter mais informações, leia [diretrizes de capacidade do cluster](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportar o modelo para o grupo de recursos

Depois de criar um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) e configurar com êxito, o seu grupo de recursos a exportar o modelo do Resource Manager para o grupo de recursos. Exportar o modelo permite-lhe automatizar Implantações futuras do cluster e de recursos, porque o modelo contém toda a infraestrutura completa.  Para obter mais informações sobre a exportação de modelos, leia [grupos de recursos de gerir o Azure Resource Manager com o portal do Azure](/azure/azure-resource-manager/manage-resource-groups-portal).

1. Na [portal do Azure](https://portal.azure.com), vá para o grupo de recursos que contém o cluster (**sfclustertutorialgroup**, se estiver a seguir este tutorial). 

2. No painel esquerdo, selecione **implementações**, ou selecione a ligação sob **implementações**. 

3. Selecione a implementação com êxito mais recente a partir da lista.

4. No painel esquerdo, selecione **modelo** e, em seguida, selecione **transferir** para exportar o modelo como um ficheiro ZIP.  Guarde o modelo e parâmetros para o computador local.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Adicionar ou remover nós de um tipo de nó

Dimensionamento e reduzir ou o dimensionamento horizontal, muda o número de nós do cluster. Quando aumenta e reduzir, adicionar mais instâncias de máquina virtual ao conjunto de dimensionamento. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um ficheiro de modelo e parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implementação mais recente.  Abra o *Parameters. JSON* ficheiro.  Se tiver implementado o cluster com o [modelo de exemplo] [ template] neste tutorial, existem três tipos de nós no cluster e três parâmetros que o número de nós para cada tipo de nó:  *nt0InstanceCount*, *nt1InstanceCount*, e *nt2InstanceCount*.  O *nt1InstanceCount* parâmetro, por exemplo, define a contagem de instâncias para o segundo tipo de nó e define o número de VMs no conjunto de dimensionamento de máquina virtual associada.

Assim, ao atualizar o valor do *nt1InstanceCount* é alterar o número de nós no segundo tipo de nó.  Lembre-se de que não é possível dimensionar um tipo de nó fora de mais de 100 nós.  executar cargas de trabalho de produção com monitorização de estado de tipos de nó não primário sempre devem ter cinco ou mais nós. executar cargas de trabalho de produção sem monitoração de estado de tipos de nó não primário devem ter sempre dois ou mais nós.

Se estão a dimensionar em, remover nós de um tipo de nó de Bronze [nível de durabilidade] [ durability] tem [remova manualmente o estado de nós](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Para o escalão de durabilidade Silver e Gold, essas etapas são feitas automaticamente pela plataforma.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guardar as alterações para o *Template* e *Parameters. JSON* ficheiros.  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Ou o seguinte comando da CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Adicionar um tipo de nó ao cluster

Cada tipo de nó que está definido no cluster do Service Fabric em execução no Azure é configurado como um [separar o conjunto de dimensionamento de máquina virtual](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerido separadamente. Forma independente pode aumentar cada tipo de nó ou reduzir verticalmente, têm conjuntos diferentes de portas abertas e utilize métricas de capacidade diferente. Independentemente também pode alterar o SKU de SO em execução em cada nó de cluster, mas tenha em atenção que não pode ter uma combinação de Windows e Linux em execução no cluster de exemplo. Um conjunto de dimensionamento/tipo de nó único não pode conter mais de 100 nós.  Pode dimensionar um cluster horizontalmente com mais de 100 nós ao adicionar o nó adicional tipos/conjuntos de dimensionamento. Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um ficheiro de modelo e parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implementação mais recente.  Abra o *Parameters. JSON* ficheiro.  Se tiver implementado o cluster com o [modelo de exemplo] [ template] neste tutorial, existem três tipos de nós no cluster.  Nesta secção é adicionar um tipo de nó quarto, atualizar e implementar um modelo do Resource Manager. 

Além do novo tipo de nó, também adicionar o conjunto de dimensionamento de máquina virtual associada (que é executado numa sub-rede separada da rede virtual) e grupo de segurança de rede.  Pode optar por adicionar o endereço IP público novo ou existente e recursos do Balanceador de carga do Azure para o novo conjunto de dimensionamento.  O novo tipo de nó tem um [nível de durabilidade] [ durability] de prata e tamanho de "Standard_D2_V2".

Na *Template* de ficheiros, adicione os seguintes parâmetros de novo:
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

Na *Template* de ficheiros, adicione as seguintes variáveis de novo:
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

Na *Template* de ficheiros, adicione uma nova sub-rede para o recurso de rede virtual:
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

Na *Template* de ficheiros, adicione o novo endereço e a carga balanceador recursos de IP público:
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

Na *Template* de ficheiros, adicionar nova rede grupo e a máquina virtual dimensionamento conjunto de recursos de segurança.  A propriedade NodeTypeRef dentro as propriedades de extensão do Service Fabric do conjunto de dimensionamento de máquina virtual mapeia o tipo de nó especificado para o conjunto de dimensionamento.

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
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
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

Na *Template* de ficheiros, atualizar o recurso de cluster e adicionar um novo tipo de nó:
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

Na *Parameters. JSON* de ficheiros, adicione os seguintes novos parâmetros e valores:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guardar as alterações para o *Template* e *Parameters. JSON* ficheiros.  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Ou o seguinte comando da CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Remover um tipo de nó do cluster
Depois de criar um cluster do Service Fabric, pode dimensionar um cluster horizontalmente ao remover um tipo de nó (conjunto de dimensionamento de máquina virtual) e todos nós do mesmo. Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster. Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

> [!WARNING]
> Utilizar Remove-AzureRmServiceFabricNodeType remover um tipo de nó de um cluster de produção não é recomendada a ser utilizada com frequência. É um comando perigoso como elimina o recurso de conjunto de dimensionamento de máquina virtual por trás do tipo de nó. 

Para remover o tipo de nó, execute o [Remove-AzureRmServiceFabricNodeType](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) cmdlet.  O tipo de nó tem de ser Gold ou Silver [nível de durabilidade] [ durability] o cmdlet elimina o conjunto de dimensionamento associado ao tipo de nó e demora algum tempo a concluir.  Em seguida, execute o [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) cmdlet em cada um de nós para remover, que elimina o estado do nó e remove os nós do cluster. Se existirem serviços em nós, em seguida, os serviços são primeiro movidos para outro nó. Se o Gestor de clusters não é possível encontrar um nó para o serviço/réplica, em seguida, a operação é atrasado/bloqueado.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

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

## <a name="increase-node-resources"></a>Aumentar os recursos de nó 
Depois de criar um cluster do Service Fabric, pode dimensionar um tipo de nó de cluster verticalmente (alterar os recursos de nós) ou atualizar o sistema operativo do tipo de nó VMs.  

> [!WARNING]
> Recomendamos que não altere o SKU de VM de um tipo de nó/conjunto de dimensionamento, a menos que ele está em execução em durabilidade Silver ou superior. Alterar o tamanho de SKU de VM é uma operação de infraestrutura do destrutiva dados no local. Sem capacidade algum atraso ou monitorizar esta alteração, é possível que a operação pode causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem monitorização de estado.

> [!WARNING]
> Recomendamos que não altere o SKU de VM do tipo de nó principal, que é uma operação perigosa e não suportados.  Se precisar de mais capacidade de cluster, pode adicionar mais instâncias de VM ou tipos de nós adicionais.  Se não for possível, pode criar um novo cluster e [restaurar estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo.  Se não for possível, pode [alterar o SKU de VM do tipo de nó primário](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um ficheiro de modelo e parâmetros](#export-the-template-for-the-resource-group) do grupo de recursos para a implementação mais recente.  Abra o *Parameters. JSON* ficheiro.  Se tiver implementado o cluster com o [modelo de exemplo] [ template] neste tutorial, existem três tipos de nós no cluster.  

O tamanho das VMs no segundo tipo de nó definido na *vmNodeType1Size* parâmetro.  Alteração da *vmNodeType1Size* valor do parâmetro de Standard_D2_V2 para [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), que duplica os recursos de cada instância VM.

O SKU de VM para todos os tipos de três nós está definido para o *vmImageSku* parâmetro.  Novamente, alterar o SKU de VM de um tipo de nó deve ser abordado com cuidado e não é recomendada para o tipo de nó primário.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guardar as alterações para o *Template* e *Parameters. JSON* ficheiros.  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Ou o seguinte comando da CLI do Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar e remover nós (ampliar e reduzir horizontalmente)
> * Adicionar e remover tipos de nós (ampliar e reduzir horizontalmente)
> * Recursos de nó de aumento (aumento vertical)

Em seguida, avance para o tutorial seguinte para saber como atualizar o runtime de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
