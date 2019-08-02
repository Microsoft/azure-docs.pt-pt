---
title: Padrões de rede para o Azure Service Fabric | Microsoft Docs
description: Descreve os padrões de rede comuns para Service Fabric e como criar um cluster usando os recursos de rede do Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: 0a411e0fe3b89eaaa19f4e18f5e614b03dd1d682
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599426"
---
# <a name="service-fabric-networking-patterns"></a>Padrões de rede Service Fabric
Você pode integrar seu cluster de Service Fabric do Azure com outros recursos de rede do Azure. Neste artigo, mostraremos como criar clusters que usam os seguintes recursos:

- [Rede virtual ou sub-rede existente](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Balanceador de carga somente interno](#internallb)
- [Balanceador de carga interno e externo](#internalexternallb)

O Service Fabric é executado em um conjunto de dimensionamento de máquinas virtuais padrão. Qualquer funcionalidade que você possa usar em um conjunto de dimensionamento de máquinas virtuais pode usar com um Cluster Service Fabric. As seções de rede dos modelos de Azure Resource Manager para conjuntos de dimensionamento de máquinas virtuais e Service Fabric são idênticas. Depois de implantar o em uma rede virtual existente, é fácil incorporar outros recursos de rede, como o Azure ExpressRoute, o gateway de VPN do Azure, um grupo de segurança de rede e o emparelhamento de rede virtual.

O Service Fabric é exclusivo de outros recursos de rede em um aspecto. O [portal do Azure](https://portal.azure.com) usa internamente o provedor de recursos Service Fabric para chamar um cluster para obter informações sobre nós e aplicativos. O provedor de recursos de Service Fabric requer acesso de entrada publicamente acessível à porta de gateway HTTP (porta 19080, por padrão) no ponto de extremidade de gerenciamento. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) usa o ponto de extremidade de gerenciamento para gerenciar o cluster. O provedor de recursos de Service Fabric também usa essa porta para consultar informações sobre o cluster, para exibir na portal do Azure. 

Se a porta 19080 não estiver acessível no provedor de recursos Service Fabric, uma mensagem como *nós não encontrados* aparecerá no portal e a lista de nós e aplicativos aparecerá vazia. Se você quiser ver o cluster na portal do Azure, o balanceador de carga deverá expor um endereço IP público e o grupo de segurança de rede deverá permitir o tráfego de entrada da porta 19080. Se a instalação não atender a esses requisitos, o portal do Azure não exibirá o status do cluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Modelos

Todos os modelos de Service Fabric estão no [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Você deve ser capaz de implantar os modelos no estado em que se encontram usando os comandos do PowerShell a seguir. Se você estiver implantando o modelo de rede virtual do Azure existente ou o modelo de IP público estático, leia primeiro a seção [instalação inicial](#initialsetup) deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo a seguir, começamos com uma rede virtual existente chamada ExistingRG-vnet, no grupo de recursos **ExistingRG** . A sub-rede é denominada default. Esses recursos padrão são criados quando você usa o portal do Azure para criar uma VM (máquina virtual) padrão. Você pode criar a rede virtual e a sub-rede sem criar a VM, mas o objetivo principal de adicionar um cluster a uma rede virtual existente é fornecer conectividade de rede para outras VMs. Criar a VM fornece um bom exemplo de como uma rede virtual existente normalmente é usada. Se o cluster de Service Fabric usar apenas um balanceador de carga interno, sem um endereço IP público, você poderá usar a VM e seu IP público como uma *caixa de salto*segura.

### <a name="static-public-ip-address"></a>Endereço IP público estático

Um endereço IP público estático geralmente é um recurso dedicado que é gerenciado separadamente da VM ou das VMs a que ele está atribuído. Ele é provisionado em um grupo de recursos de rede dedicado (em oposição ao grupo de recursos de Cluster Service Fabric em si). Crie um endereço IP público estático chamado staticIP1 no mesmo grupo de recursos ExistingRG, na portal do Azure ou usando o PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Modelo de Service Fabric

Nos exemplos deste artigo, usamos o Service Fabric template. JSON. Você pode usar o assistente do portal padrão para baixar o modelo do portal antes de criar um cluster. Você também pode usar um dos [modelos de exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates), como o [cluster de Service Fabric de cinco nós seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual ou sub-rede existente

1. Altere o parâmetro subnet para o nome da sub-rede existente e, em seguida, adicione dois novos parâmetros para fazer referência à rede virtual existente:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Comente o `Microsoft.Compute/virtualMachineScaleSets`atributo de, pois você está usando uma sub-rede existente e desabilitou essa variável na etapa 1. `nicPrefixOverride`

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Altere a `vnetID` variável para apontar para a rede virtual existente:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Remova `Microsoft.Network/virtualNetworks` de seus recursos para que o Azure não crie uma nova rede virtual:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Comente a rede virtual do `dependsOn` atributo de `Microsoft.Compute/virtualMachineScaleSets`, para que você não dependa da criação de uma nova rede virtual:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Implantar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Após a implantação, sua rede virtual deve incluir as novas VMs do conjunto de dimensionamento. O tipo de nó do conjunto de dimensionamento de máquinas virtuais deve mostrar a rede virtual e a sub-rede existentes. Você também pode usar protocolo RDP (RDP) para acessar a VM que já estava na rede virtual e executar ping nas VMs do novo conjunto de dimensionamento:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Para obter outro exemplo, consulte [um que não seja específico para Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicione parâmetros para o nome do grupo de recursos IP estáticos existente, o nome e o FQDN (nome de domínio totalmente qualificado):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Remova o `dnsName` parâmetro. (O endereço IP estático já tem um.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adicione uma variável para fazer referência ao endereço IP estático existente:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Remova `Microsoft.Network/publicIPAddresses` de seus recursos para que o Azure não crie um novo endereço IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Comente o endereço IP do `dependsOn` atributo de `Microsoft.Network/loadBalancers`, para que você não dependa da criação de um novo endereço IP:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. No recurso, altere o `publicIPAddress` elemento de `frontendIPConfigurations` para fazer referência ao endereço IP estático existente em vez de criar um recém-criado: `Microsoft.Network/loadBalancers`

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. No recurso, altere `managementEndpoint` para o FQDN do DNS do endereço IP estático. `Microsoft.ServiceFabric/clusters` Se você estiver usando um cluster seguro, certifique-se de alterar *http://* para *https://* . (Observe que essa etapa se aplica somente a clusters Service Fabric. Se você estiver usando um conjunto de dimensionamento de máquinas virtuais, ignore esta etapa.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implantar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Após a implantação, você pode ver que o balanceador de carga está associado ao endereço IP estático público do outro grupo de recursos. O ponto de extremidade de conexão de cliente do Service Fabric e [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) apontam para o FQDN do DNS do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Balanceador de carga somente interno

Esse cenário substitui o balanceador externo de carga no modelo de Service Fabric padrão por um balanceador de carga somente interno. Para obter as implicações para o portal do Azure e para o provedor de recursos Service Fabric, consulte a seção anterior.

1. Remova o `dnsName` parâmetro. (Isso não é necessário.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, se você usar um método de alocação estática, poderá adicionar um parâmetro de endereço IP estático. Se você usar um método de alocação dinâmica, não será necessário fazer essa etapa.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Remova `Microsoft.Network/publicIPAddresses` de seus recursos para que o Azure não crie um novo endereço IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Remova o atributo de `dependsOn` endereço IP `Microsoft.Network/loadBalancers`de, para que você não dependa da criação de um novo endereço IP. Adicione o atributo rede `dependsOn` virtual porque o balanceador de carga agora depende da sub-rede da rede virtual:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Altere a configuração do `frontendIPConfigurations` balanceador de carga do usando um `publicIPAddress`, para usar uma sub-rede `privateIPAddress`e. `privateIPAddress`usa um endereço IP interno estático predefinido. Para usar um endereço IP dinâmico, remova o `privateIPAddress` elemento e, em seguida `privateIPAllocationMethod` , altere para **dinâmico**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. No recurso, altere `managementEndpoint` para apontar para o endereço do balanceador de carga interno. `Microsoft.ServiceFabric/clusters` Se você usar um cluster seguro, certifique-se de alterar *http://* para *https://* . (Observe que essa etapa se aplica somente a clusters Service Fabric. Se você estiver usando um conjunto de dimensionamento de máquinas virtuais, ignore esta etapa.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implantar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Após a implantação, o balanceador de carga usa o endereço IP estático privado estático 10.0.0.250. Se você tiver outro computador na mesma rede virtual, poderá ir para o ponto de extremidade de [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) interno. Observe que ele se conecta a um dos nós por trás do balanceador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Balanceador de carga interno e externo

Nesse cenário, você começa com o balanceador de carga externo do tipo de nó único existente e adiciona um balanceador de carga interno para o mesmo tipo de nó. Uma porta de back-end anexada a um pool de endereços de back-end pode ser atribuída somente a um único balanceador de carga. Escolha qual balanceador de carga terá as portas do aplicativo e qual balanceador de carga terá seus pontos de extremidade de gerenciamento (portas 19000 e 19080). Se você colocar os pontos de extremidade de gerenciamento no balanceador de carga interno, tenha em mente as restrições do provedor de recursos Service Fabric discutidas anteriormente neste artigo. No exemplo que usamos, os pontos de extremidade de gerenciamento permanecem no balanceador externo de carga. Você também adiciona uma porta de aplicativo da porta 80 e a coloca no balanceador de carga interno.

Em um cluster de tipo de dois nós, um tipo de nó está no balanceador de carga externo. O outro tipo de nó está no balanceador de carga interno. Para usar um cluster de tipo de dois nós, no modelo de tipo de dois nós criado pelo portal (que vem com dois balanceadores de carga), alterne o segundo balanceador de carga para um balanceador de carga interno. Para obter mais informações, consulte a seção balanceador de [carga somente interno](#internallb) .

1. Adicione o parâmetro de endereço IP do balanceador de carga interno estático. (Para ver as observações relacionadas ao uso de um endereço IP dinâmico, consulte as seções anteriores deste artigo.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione um parâmetro de porta de aplicativo 80.

3. Para adicionar versões internas das variáveis de rede existentes, copie e cole-as e adicione "-int" ao nome:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Se você começar com o modelo gerado pelo portal que usa a porta de aplicativo 80, o modelo padrão do portal adicionará AppPort1 (porta 80) no balanceador externo de carga. Nesse caso, remova AppPort1 do balanceador `loadBalancingRules` externo de carga e investigações, para que você possa adicioná-lo ao balanceador de carga interno:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Adicione um segundo `Microsoft.Network/loadBalancers` recurso. Ele é semelhante ao balanceador de carga interno criado na seção de balanceador de [carga somente interno](#internallb) , mas usa as variáveis de balanceador de carga "-int" e implementa apenas a porta de aplicativo 80. Isso também remove `inboundNatPools`, para manter os pontos de extremidade RDP no balanceador de carga público. Se você quiser RDP no balanceador de carga interno, mova `inboundNatPools` do balanceador de carga externo para este balanceador de carga interno:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Em `networkProfile` para o `Microsoft.Compute/virtualMachineScaleSets` recurso, adicione o pool de endereços de back-end interno:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Implantar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Após a implantação, você pode ver dois balanceadores de carga no grupo de recursos. Se você procurar os balanceadores de carga, poderá ver o endereço IP público e os pontos de extremidade de gerenciamento (portas 19000 e 19080) atribuídos ao endereço IP público. Você também pode ver o endereço IP interno estático e o ponto de extremidade do aplicativo (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga usam o mesmo pool de back-end de conjunto de dimensionamento de máquinas virtuais.

## <a name="next-steps"></a>Passos Seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)

Após a implantação, você pode ver dois balanceadores de carga no grupo de recursos. Se você procurar os balanceadores de carga, poderá ver o endereço IP público e os pontos de extremidade de gerenciamento (portas 19000 e 19080) atribuídos ao endereço IP público. Você também pode ver o endereço IP interno estático e o ponto de extremidade do aplicativo (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga usam o mesmo pool de back-end de conjunto de dimensionamento de máquinas virtuais.

