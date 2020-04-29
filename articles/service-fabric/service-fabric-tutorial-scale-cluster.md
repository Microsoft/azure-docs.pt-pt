---
title: Dimensionar um cluster do Service Fabric no Azure
description: Neste tutorial, aprende-se a escalar um cluster de Tecido sinuoso em Azure para fora e para dentro, e como limpar os recursos que sobra.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78251802"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Tutorial: Dimensionar um cluster do Service Fabric no Azure

Este tutorial é a terceira parte de uma série, e mostra-lhe como escalar o seu aglomerado existente para fora e para dentro. Quando tiver terminado, saberá como dimensionar o seu cluster e como limpar quaisquer recursos restantes.  Para obter mais informações sobre a escala de um cluster em funcionamento em Azure, leia os [clusters de Tecido de Serviço de Escala.](service-fabric-cluster-scaling.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione e remova os nós (escala para fora e escala dentro)
> * Adicione e remova os tipos de nó (escala para fora e escala dentro)
> * Aumentar os recursos do nó (escala para cima)

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro em Azure usando um modelo
> * [Monitorize um cluster](service-fabric-tutorial-monitor-cluster.md)
> * Reduzir ou aumentar horizontalmente um cluster
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale [o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou [o Azure CLI](/cli/azure/install-azure-cli).
* Criar um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro em Azure

## <a name="important-considerations-and-guidelines"></a>Considerações e orientações importantes

As cargas de trabalho das aplicações mudam ao longo do tempo, os seus serviços existentes precisam de mais (ou menos) recursos?  [Adicione ou remova os nós](#add-nodes-to-or-remove-nodes-from-a-node-type) de um tipo de nó para aumentar ou diminuir os recursos de cluster.

Precisa adicionar mais de 100 nós ao seu aglomerado?  Um único conjunto de nó/escala de tecido de serviço não pode conter mais de 100 nós/VMs.  Para escalar um cluster para além de 100 nós, [adicione tipos adicionais de nó](#add-nodes-to-or-remove-nodes-from-a-node-type).

A sua aplicação tem vários serviços, e algum deles precisa de ser público ou virado para a internet?  As aplicações típicas contêm um serviço de gateway frontal que recebe entrada de um cliente e um ou mais serviços de back-end que comunicam com os serviços front-end. Neste caso, recomendamos que [adicione pelo menos dois tipos de nós](#add-nodes-to-or-remove-nodes-from-a-node-type) ao cluster.  

Os seus serviços têm diferentes necessidades de infraestruturas, tais como maiores ciclos de RAM ou CPU mais elevados? Por exemplo, a sua aplicação contém um serviço frontal e um serviço de back-end. O serviço frontal pode funcionar em VMs menores (tamanhos VM como D2) que têm portas abertas à internet. O serviço back-end, no entanto, é intensivo em computação e precisa de funcionar em VMs maiores (com tamanhos VM como D4, D6, D15) que não estão virados para a internet. Neste caso, recomendamos que [adicione dois ou mais tipos de nós](#add-nodes-to-or-remove-nodes-from-a-node-type) ao seu cluster. Isto permite que cada tipo de nó tenha propriedades distintas, como conectividade de internet ou tamanho VM. O número de VMs também pode ser dimensionado de forma independente.

Ao escalonar um cluster Azure, tenha em mente as seguintes orientações:

* Um único conjunto de nó/escala de tecido de serviço não pode conter mais de 100 nós/VMs.  Para escalar um cluster para além de 100 nós, adicione tipos adicionais de nó.
* Os tipos primários de nós que executam cargas de trabalho de produção devem ter um nível de [durabilidade][durability] de Ouro ou Prata e ter sempre cinco ou mais nós.
* Os tipos de nós não primários que executam cargas horárias de produção audais devem ter sempre cinco ou mais nós.
* Os tipos não primários de nó que executam cargas de trabalho apátridas de produção apátrida devem ter sempre dois ou mais nós.
* Qualquer nó de nível de [durabilidade][durability] de Ouro ou Prata deve ter sempre cinco ou mais nós.
* Se escalonar (removendo nós de) um nó primário, nunca deve diminuir o número de instâncias para menos do que o nível de [fiabilidade][reliability] requer.

Para mais informações, leia a sua capacidade de [cluster.](service-fabric-cluster-capacity.md)

## <a name="export-the-template-for-the-resource-group"></a>Exportar o modelo para o grupo de recursos

Depois de criar um [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro e de criar o seu grupo de recursos com sucesso, exporte o modelo de Gestor de Recursos para o grupo de recursos. A exportação do modelo permite-lhe automatizar futuras implantações do cluster e dos seus recursos porque o modelo contém toda a infraestrutura completa.  Para obter mais informações sobre modelos de exportação, leia [Manage Azure Resource Manager grupos de recursos utilizando o portal Azure](/azure/azure-resource-manager/manage-resource-groups-portal).

1. No [portal Azure,](https://portal.azure.com)vá ao grupo de recursos que contém o cluster **(sfclustertutorialgroup,** se estiver a seguir este tutorial). 

2. No painel esquerdo, selecione **Implantações**, ou selecione o link em **implementações**. 

3. Selecione a mais recente implementação bem sucedida da lista.

4. No painel esquerdo, selecione **Template** e, em seguida, selecione **Baixar** para exportar o modelo como um ficheiro ZIP.  Guarde o modelo e os parâmetros para o seu computador local.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Adicione os nódosos ou remova os nódosos de um tipo de nó

A escala para dentro e para fora, ou escalação horizontal, altera o número de nós no cluster. Quando escala para dentro ou para fora, adiciona mais casos de máquinas virtuais ao conjunto de escala. Estas instâncias tornam-se os nós que o Service Fabric utiliza. O Service Fabric sabe quando são adicionadas mais instâncias ao conjunto de dimensionamento (ao aumentar horizontalmente) e reage automaticamente. Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um modelo e um ficheiro](#export-the-template-for-the-resource-group) de parâmetros do grupo de recursos para a mais recente implantação.  Abra o ficheiro *parâmetros.json.*  Se implementou o cluster utilizando o [modelo][template] de amostra neste tutorial, existem três tipos de nó no cluster e três parâmetros que definiram o número de nós para cada tipo de nó: *nt0Count,* *nt1InstanceCount*, e *nt2InstanceCount*.  O parâmetro *nt1InstanceCount,* por exemplo, define a contagem de exemplos para o segundo tipo de nó e define o número de VMs no conjunto de escala de máquina virtual associado.

Assim, ao atualizar o valor do *nt1InstanceCount* muda o número de nós no segundo tipo de nó.  Lembre-se, não pode escalar um nó para mais de 100 nós.  Os tipos de nós não primários que executam cargas horárias de produção audais devem ter sempre cinco ou mais nós. Os tipos não primários de nó que executam cargas de trabalho apátridas de produção apátrida devem ter sempre dois ou mais nós.

Se estiver a escalonar, retirando os nós de um [nível][durability] de durabilidade de bronze, deve [remover manualmente o estado desses nós](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Para o nível de durabilidade prata e ouro, estes passos são feitos automaticamente pela plataforma.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guarde quaisquer alterações nos *ficheiros template.json* e *parâmetros.json.*  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Ou o seguinte comando Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Adicione um tipo de nó ao cluster

Todos os tipos de nó que são definidos num cluster de tecido de serviço que funciona em Azure são configurados como um conjunto de escala de [máquina virtual separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente. Você pode escalar de forma independente cada nó tipo para cima ou para baixo, ter diferentes conjuntos de portas abertas, e usar métricas de capacidade diferentes. Também pode alterar de forma independente o OS SKU em cada nó de cluster, mas note que não pode ter uma mistura de Windows e Linux em execução no cluster de amostras. Um único conjunto de tipo/escala de nó não pode conter mais de 100 nós.  Pode escalar um cluster horizontalmente para mais de 100 nós adicionando tipos/conjuntos de escala adicionais. Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um modelo e um ficheiro](#export-the-template-for-the-resource-group) de parâmetros do grupo de recursos para a mais recente implantação.  Abra o ficheiro *parâmetros.json.*  Se você implantou o cluster usando o modelo de [amostra][template] neste tutorial, existem três tipos de nó no cluster.  Nesta secção, adicione um quarto tipo de nó atualizando e implementando um modelo de Gestor de Recursos. 

Além do novo tipo de nó, adicione também o conjunto de escala de máquina virtual associada (que funciona numa sub-rede separada da rede virtual) e o grupo de segurança da rede.  Pode optar por adicionar novos ou existentes recursos de ip público sinuoso ou já existente sapateado para o novo conjunto de escala.  O novo tipo de nó tem um nível de [durabilidade][durability] de Prata e tamanho de "Standard_D2_V2".

No ficheiro *template.json,* adicione os seguintes novos parâmetros:
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

No ficheiro *template.json,* adicione as seguintes novas variáveis:
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

No ficheiro *template.json,* adicione uma nova sub-rede ao recurso de rede virtual:
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

No ficheiro *template.json,* adicione novos recursos de endereço IP público e de equilíbrio de carga:
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

No ficheiro *template.json,* adicione novos recursos conjuntos de grupo de segurança de rede e máquina virtual.  A propriedade NodeTypeRef dentro das propriedades de extensão de tecido de serviço do conjunto de escala de máquina virtual mapeia o tipo de nó especificado para o conjunto de escala.

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

No ficheiro *template.json,* atualize o recurso do cluster e adicione um novo tipo de nó:
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

No ficheiro *parâmetros.json,* adicione os seguintes novos parâmetros e valores:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guarde quaisquer alterações nos *ficheiros template.json* e *parâmetros.json.*  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Ou o seguinte comando Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Remova um tipo de nó do cluster
Depois de criar um cluster de Tecido de Serviço, pode escalar um cluster horizontalmente removendo um tipo de nó (conjunto de escala de máquina virtual) e todos os seus nós. Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster. À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não é recomendável que se utilize o Remove-AzServiceFabricNodeType para remover um tipo de nó de um cluster de produção. É um comando perigoso pois elimina o recurso de conjunto de escala de máquina virtual por trás do tipo de nó. 

Para remover o tipo de nó, execute o cmdlet [Remove-AzServiceFabricNodeType.](/powershell/module/az.servicefabric/remove-azservicefabricnodetype)  O tipo de nó deve ser de prata ou nível de [durabilidade][durability] de ouro O cmdlet elimina o conjunto de escala associado ao tipo de nó e demora algum tempo a ser concluído.  Em seguida, execute o cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em cada um dos nós para remover, o que elimina o estado do nó e remove os nós do cluster. Se houver serviços nos nós, então os serviços são transferidos primeiro para outro nó. Se o gestor do cluster não encontrar um nó para a réplica/serviço, então a operação é adiada/bloqueada.

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
Depois de criar um cluster de Tecido de Serviço, pode escalar um tipo de nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operativo dos VMs do tipo nó.  

> [!WARNING]
> Recomendamos que não altere o VM SKU de um tipo de conjunto/nó de escala, a menos que esteja a funcionar na durabilidade da Prata ou maior. Mudar o tamanho VM SKU é uma operação de infraestrutura destrutiva em dados. Sem alguma capacidade de atrasar ou monitorizar esta alteração, é possível que a operação possa causar perda de dados para serviços estatais ou causar outras questões operacionais imprevistas, mesmo para cargas de trabalho apátridas.

> [!WARNING]
> Recomendamos que não altere o VM SKU do tipo de nó primário, que é uma operação perigosa e não suportado.  Se precisar de mais capacidade de cluster, pode adicionar mais instâncias vm ou tipos de nó adicionais.  Se isso não for possível, pode criar um novo cluster e restaurar o estado de [aplicação](service-fabric-reliable-services-backup-restore.md) (se aplicável) a partir do seu antigo cluster.  Se isso não for possível, pode [alterar o VM SKU do tipo de nó primário](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Atualizar o modelo

[Exportar um modelo e um ficheiro](#export-the-template-for-the-resource-group) de parâmetros do grupo de recursos para a mais recente implantação.  Abra o ficheiro *parâmetros.json.*  Se você implantou o cluster usando o modelo de [amostra][template] neste tutorial, existem três tipos de nó no cluster.  

O tamanho dos VMs no segundo tipo de nó é definido no parâmetro *vmNodeType1Size.*  Altere o valor do parâmetro *vmNodeType1Size* de Standard_D2_V2 para [Standard_D3_V2,](../virtual-machines/dv2-dsv2-series.md)o que duplica os recursos de cada instância VM.

O VM SKU para todos os três tipos de nó está definido no parâmetro *vmImageSku.*  Mais uma vez, a alteração do VM SKU de um tipo de nó deve ser abordada com cuidado e não é recomendada para o tipo de nó primário.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Guarde quaisquer alterações nos *ficheiros template.json* e *parâmetros.json.*  Para implementar o modelo atualizado, execute o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Ou o seguinte comando Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicione e remova os nós (escala para fora e escala dentro)
> * Adicione e remova os tipos de nó (escala para fora e escala dentro)
> * Aumentar os recursos do nó (escala para cima)

Em seguida, avance para o tutorial seguinte para saber como atualizar o runtime de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Adicione e remova os tipos de nó (escala para fora e escala dentro)
> * Aumentar os recursos do nó (escala para cima)

Em seguida, avance para o tutorial seguinte para saber como atualizar o runtime de um cluster.
> [!div class="nextstepaction"]
> [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
