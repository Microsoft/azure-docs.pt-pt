---
title: Padrões de redes para o Azure Service Fabric | Documentos da Microsoft
description: Descreve os padrões comuns de redes para o Service Fabric e como criar um cluster com funcionalidades de rede do Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: d5aa09f3ff899766e6eb6d1784e4417f7b48eac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66110256"
---
# <a name="service-fabric-networking-patterns"></a>Padrões de redes do Service Fabric
Pode integrar o seu cluster do Azure Service Fabric com outras funcionalidades de rede do Azure. Neste artigo, vamos mostrar como criar clusters que utilizam as seguintes funcionalidades:

- [Rede virtual existente ou sub-rede](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Balanceador de carga interno só](#internallb)
- [Balanceador de carga internos e externos](#internalexternallb)

Service Fabric é executado num conjunto de dimensionamento de máquina de virtual padrão. Qualquer funcionalidade que pode usar num conjunto de dimensionamento de máquina virtual, pode utilizar com um cluster do Service Fabric. As secções de rede dos modelos do Azure Resource Manager para conjuntos de dimensionamento de máquina virtual e o Service Fabric são idênticas. Depois de implementar uma rede virtual existente, é fácil incorporar a outras funcionalidades de rede, como o Azure ExpressRoute, o Gateway de VPN do Azure, um grupo de segurança de rede e peering da rede virtual.

O Service Fabric é exclusivo a partir de outras funcionalidades de rede num aspecto. O [portal do Azure](https://portal.azure.com) internamente utiliza o fornecedor de recursos do Service Fabric para a chamada para um cluster para obter informações sobre nós e aplicativos. O fornecedor de recursos do Service Fabric requer acesso de entrada acessível ao público para a porta de gateway HTTP (porta 19080, por padrão) no ponto final de gestão. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) utiliza o ponto final de gestão para gerir o cluster. O fornecedor de recursos do Service Fabric também utiliza esta porta consultar as informações sobre o cluster, para apresentar no portal do Azure. 

Se a porta 19080 não está acessível a partir do fornecedor de recursos do Service Fabric, uma mensagem, como *não encontrados nós* é apresentada no portal, e sua lista de nó e o aplicativo parece vazia. Se pretender ver o seu cluster no portal do Azure, o Balanceador de carga deve expor um endereço IP público e seu grupo de segurança de rede tem de permitir o tráfego de entrada porta 19080. Se a configuração não cumprir estes requisitos, o portal do Azure não apresenta o estado do cluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Modelos

Todos os modelos do Service Fabric estão na [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Deve ser capaz de implementar os modelos como-é utilizando os seguintes comandos do PowerShell. Se estiver a implementar o modelo de rede Virtual do Azure existente ou o modelo de IP público estático, leia primeiro o [inicial configuração](#initialsetup) seção deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo a seguir, vamos começar com uma rede virtual existente com o nome ExistingRG-vnet, no **ExistingRG** grupo de recursos. A sub-rede com o nome predefinido. Esses recursos predefinidos são criados ao utilizar o portal do Azure para criar uma máquina de virtual (VM) padrão. Poderia criar a rede virtual e uma sub-rede sem criar a VM, mas o principal objetivo da adição de um cluster a uma rede virtual existente é fornecer conectividade de rede para outras VMs. Criar a VM fornece um bom exemplo de como uma rede virtual existente, normalmente, é usada. Se o cluster do Service Fabric utiliza apenas um balanceador de carga interno, sem um endereço IP público, pode utilizar a VM e o IP público como um segura *saltar caixa*.

### <a name="static-public-ip-address"></a>Endereço IP público estático

Geralmente, um endereço IP público estático é um recurso dedicado gerenciado separadamente a partir da VM ou VMs que está atribuída a. Ele é aprovisionado num grupo de recursos de rede dedicado (em vez de no recurso de cluster do Service Fabric de grupo em si). Crie um endereço IP público estático denominado staticIP1 no mesmo ExistingRG grupo de recursos, no portal do Azure ou através do PowerShell:

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

### <a name="service-fabric-template"></a>Modelo do Service Fabric

Os exemplos neste artigo, utilizamos o Template de Service Fabric. Pode utilizar o Assistente do portal de standard para transferir o modelo do portal, antes de criar um cluster. Também pode utilizar um da [modelos de exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates), como o [cluster do Service Fabric de cinco nós seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual existente ou sub-rede

1. Altere o parâmetro de sub-rede para o nome da sub-rede existente e, em seguida, adicione dois novos parâmetros para fazer referência a rede virtual existente:

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

2. Comente `nicPrefixOverride` atributo do `Microsoft.Compute/virtualMachineScaleSets`, uma vez que estiver a utilizar a sub-rede existente e tiver desativado esta variável no passo 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Alterar o `vnetID` variável para apontar para a rede virtual existente:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Remover `Microsoft.Network/virtualNetworks` dos seus recursos, então, Azure não cria uma nova rede virtual:

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

5. Comente a rede virtual a partir da `dependsOn` atributo do `Microsoft.Compute/virtualMachineScaleSets`, por isso, não depende de criar uma nova rede virtual:

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

6. Implemente o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Após a implementação, a rede virtual deve incluir o novo conjunto de dimensionamento de VMs. O tipo de nó de conjunto de dimensionamento de máquina virtual deverá mostrar a rede virtual existente e a sub-rede. Também pode utilizar protocolo de ambiente de trabalho remoto (RDP) para aceder à VM que já estava na rede virtual e enviar um ping a nova escala para VMs do conjunto:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Para obter outro exemplo, veja [que não é específico para o Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicione parâmetros para o nome do grupo de recursos do IP estático existente, o nome e o nome de domínio completamente qualificado (FQDN):

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

2. Remover o `dnsName` parâmetro. (O endereço IP estático já tem um).

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adicione uma variável para o endereço IP estático existente de referência:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Remover `Microsoft.Network/publicIPAddresses` dos seus recursos, então, Azure não cria um novo endereço IP:

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

5. Comente o endereço IP a partir da `dependsOn` atributo do `Microsoft.Network/loadBalancers`, por isso, não depende de criar um novo endereço IP:

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

6. Na `Microsoft.Network/loadBalancers` recursos, alterar o `publicIPAddress` elemento de `frontendIPConfigurations` para referenciar o endereço IP estático existente em vez de um modelo criado recentemente:

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

7. Na `Microsoft.ServiceFabric/clusters` recurso, alteração `managementEndpoint` para o FQDN de DNS do endereço IP estático. Se estiver a utilizar um cluster seguro, certifique-se de que altere *http://* ao *https://*. (Tenha em atenção que este passo só se aplica a clusters do Service Fabric. Se estiver a utilizar um conjunto de dimensionamento de máquinas virtuais, ignore este passo.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implemente o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Após a implementação, pode ver que o Balanceador de carga está ligado para o endereço IP público estático do outro grupo de recursos. O ponto de final de ligação do Service Fabric cliente e [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ponto de endpoint para o FQDN do DNS do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Balanceador de carga interno só

Este cenário substitui o Balanceador de carga externo no modelo padrão do Service Fabric com um balanceador de carga apenas internos. De implicações para o portal do Azure e para o fornecedor de recursos do Service Fabric, consulte a secção anterior.

1. Remover o `dnsName` parâmetro. (Ela não é necessária.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, se usar um método de alocação estático, pode adicionar um parâmetro de endereço IP estático. Se usar um método de alocação dinâmica, não é necessário executar este passo.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Remover `Microsoft.Network/publicIPAddresses` dos seus recursos, então, Azure não cria um novo endereço IP:

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

4. Remover o endereço IP `dependsOn` atributo do `Microsoft.Network/loadBalancers`, por isso, não depende de criar um novo endereço IP. Adicionar a rede virtual `dependsOn` atributo porque o Balanceador de carga depende agora a sub-rede da rede virtual:

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

5. Alterar o Balanceador de carga `frontendIPConfigurations` definição da utilização de um `publicIPAddress`, para utilizar uma sub-rede e `privateIPAddress`. `privateIPAddress` utiliza um endereço IP interno de estático predefinido. Para utilizar um endereço IP dinâmico, remova os `privateIPAddress` elemento e, em seguida, altere `privateIPAllocationMethod` ao **dinâmico**.

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

6. Na `Microsoft.ServiceFabric/clusters` recurso, alteração `managementEndpoint` para apontar para o endereço de Balanceador de carga interno. Se utilizar um cluster seguro, certifique-se de que altere *http://* ao *https://*. (Tenha em atenção que este passo só se aplica a clusters do Service Fabric. Se estiver a utilizar um conjunto de dimensionamento de máquinas virtuais, ignore este passo.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implemente o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Após a implementação, o seu Balanceador de carga utiliza o endereço IP privado estático 10.0.0.250. Se tiver outra máquina dessa mesma rede virtual, pode ir para o interno [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ponto final. Tenha em atenção que se liga a um de nós por detrás do Balanceador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Balanceador de carga internos e externos

Neste cenário, começar com o Balanceador de carga externo do tipo de nó único existente e adicionar um balanceador de carga interno para o mesmo tipo de nó. Uma porta de back-end ligada a um agrupamento de endereços de back-end pode ser atribuída apenas a um balanceador de carga individual. Escolha qual Balanceador de carga terá suas portas de aplicação, e o Balanceador de carga terá seus pontos finais de gestão (as portas 19000 e 19080). Se colocar os pontos finais de gestão no balanceador de carga interno, tenha em atenção o recurso do Service Fabric restrições de fornecedor discutidas anteriormente neste artigo. No exemplo que utilizamos, a gestão de pontos de extremidade permanecem no balanceador de carga externo. Também adicionar uma porta de aplicação porta 80 e coloque-o no balanceador de carga interno.

Num cluster de tipo de nó de dois, um tipo de nó é no balanceador de carga externo. O tipo de nó é no balanceador de carga interno. Para utilizar um cluster de tipo de nó de dois, no modelo de tipo de nó de dois criados pelo portal (que vem com dois balanceadores de carga), mude o segundo Balanceador de carga para um balanceador de carga interno. Para obter mais informações, consulte a [Balanceador de carga interno só](#internallb) secção.

1. Adicione o parâmetro de endereço IP de Balanceador de carga interno estático. (Para obter notas relacionadas ao uso de um endereço IP dinâmico, consulte as secções anteriores deste artigo.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione um parâmetro de porta 80 do aplicativo.

3. Para adicionar versões internas do existente redes variáveis, copiem e cole-os e adicione "-Int" para o nome:

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

4. Se iniciar com o modelo gerado pelo portal que utiliza a porta 80 da aplicação, o modelo padrão do portal adiciona AppPort1 (a porta 80) no balanceador de carga externo. Neste caso, remover AppPort1 do Balanceador de carga externo `loadBalancingRules` e sondas, portanto, pode adicioná-lo para o Balanceador de carga interno:

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

5. Adicione um segundo `Microsoft.Network/loadBalancers` recursos. É semelhante ao balanceador de carga interno criado no [Balanceador de carga interno só](#internallb) secção, mas usa o "-Int" variáveis de Balanceador de carga e implementa apenas a porta da aplicação 80. Esta ação também remove `inboundNatPools`para manter os pontos de extremidade do RDP no balanceador de carga público. Se quiser RDP no balanceador de carga interno, mover `inboundNatPools` de externos Balanceador de carga para este Balanceador de carga interno:

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

6. Na `networkProfile` para o `Microsoft.Compute/virtualMachineScaleSets` recursos, adicione o conjunto de endereços de back-end interno:

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

7. Implemente o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Após a implementação, pode ver dois balanceadores de carga no grupo de recursos. Se navegar os balanceadores de carga, pode ver os IP endereço e a gestão de pontos finais públicos (as portas 19000 e 19080) atribuídos para o endereço IP público. Também pode ver o estático interno endereço e a aplicação de ponto final de IP (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga, utilize o conjunto de back-end de conjunto de dimensionamento do mesmo máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md) ternalLB.json
    ```

Após a implementação, pode ver dois balanceadores de carga no grupo de recursos. Se navegar os balanceadores de carga, pode ver os IP endereço e a gestão de pontos finais públicos (as portas 19000 e 19080) atribuídos para o endereço IP público. Também pode ver o estático interno endereço e a aplicação de ponto final de IP (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga, utilize o conjunto de back-end de conjunto de dimensionamento do mesmo máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)
