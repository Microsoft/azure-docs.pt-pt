---
title: Padrões de rede para tecido de serviço Azure
description: Descreve padrões comuns de networking para o Tecido de Serviço e como criar um cluster utilizando funcionalidades de rede Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87421472"
---
# <a name="service-fabric-networking-patterns"></a>Padrões de rede de tecido de serviço
Pode integrar o seu cluster Azure Service Fabric com outras funcionalidades de networking Azure. Neste artigo, mostramos-lhe como criar clusters que utilizam as seguintes funcionalidades:

- [Rede virtual ou sub-rede existente](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Equilibrador de carga interno](#internallb)
- [Equilibrador de carga interno e externo](#internalexternallb)

O tecido de serviço funciona num conjunto de escala de máquina virtual padrão. Qualquer funcionalidade que possa utilizar num conjunto de escala de máquina virtual, pode utilizar com um cluster de Tecido de Serviço. As secções de networking dos modelos Azure Resource Manager para conjuntos de escala de máquinas virtuais e tecido de serviço são idênticas. Depois de implementar para uma rede virtual existente, é fácil incorporar outras funcionalidades de networking, como a Azure ExpressRoute, Azure VPN Gateway, um grupo de segurança de rede e olhando para a rede virtual.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Permitindo que o fornecedor de recursos de Tecido de Serviço questione o seu cluster

O Tecido de Serviço é único a partir de outras funcionalidades de networking num aspeto. O [portal Azure](https://portal.azure.com) utiliza internamente o fornecedor de recursos de Service Fabric para ligar para um cluster para obter informações sobre nós e aplicações. O fornecedor de recursos service Fabric requer acesso de entrada acessível ao público à porta de gateway HTTP (porta 19080, por defeito) no ponto final de gestão. [O Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) utiliza o ponto final de gestão para gerir o seu cluster. O fornecedor de recursos service Fabric também utiliza esta porta para consultar informações sobre o seu cluster, para exibir no portal Azure. 

Se a porta 19080 não estiver acessível a partir do fornecedor de recursos do Service Fabric, aparece no portal uma mensagem como *Os Nós Não Encontrados* e a sua lista de nós e de aplicação. Se quiser ver o seu cluster no portal Azure, o seu equilibrador de carga deve expor um endereço IP público, e o seu grupo de segurança de rede deve permitir a entrada do tráfego da porta 19080. Se a sua configuração não satisfaça estes requisitos, o portal Azure não apresenta o estado do seu cluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Modelos

Todos os modelos de tecido de serviço estão no [GitHub.](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking) Deverá ser capaz de implementar os modelos como está utilizando os seguintes comandos PowerShell. Se estiver a implementar o modelo de Rede Virtual Azure existente ou o modelo ip público estático, leia primeiro a secção de [configuração inicial](#initialsetup) deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo seguinte, começamos com uma rede virtual existente chamada ExistingRG-vnet, no grupo **de recursosRG existente.** A sub-rede tem o nome de padrão. Estes recursos predefinidos são criados quando utiliza o portal Azure para criar uma máquina virtual padrão (VM). Poderia criar a rede virtual e a sub-rede sem criar o VM, mas o principal objetivo de adicionar um cluster a uma rede virtual existente é fornecer conectividade de rede a outros VMs. A criação do VM dá um bom exemplo de como uma rede virtual existente é normalmente utilizada. Se o seu cluster Service Fabric utilizar apenas um equilibrador de carga interno, sem endereço IP público, pode utilizar o VM e o seu IP público como *uma caixa de salto* segura.

### <a name="static-public-ip-address"></a>Endereço IP público estático

Um endereço IP público estático geralmente é um recurso dedicado que é gerido separadamente dos VM ou VMs a que é atribuído. É aprovisionado num grupo dedicado de recursos em rede (em oposição ao próprio grupo de recursos de cluster de tecidos de serviço). Criar um endereço IP público estático chamado staticIP1 no mesmo grupo de recursos existente, quer no portal Azure, quer utilizando o PowerShell:

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

### <a name="service-fabric-template"></a>Modelo de tecido de serviço

Nos exemplos deste artigo, utilizamos o tecido de serviço template.js. Pode utilizar o assistente de portal padrão para descarregar o modelo a partir do portal antes de criar um cluster. Também pode utilizar um dos modelos de [amostra,](https://github.com/Azure-Samples/service-fabric-cluster-templates)como o [aglomerado de tecido de serviço de cinco nós seguro.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual ou sub-rede existente

1. Altere o parâmetro da sub-rede para o nome da sub-rede existente e, em seguida, adicione dois novos parâmetros para fazer referência à rede virtual existente:

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

   Também pode comentar o parâmetro com o nome "virtualNetworkName" para que não o indique duas vezes no nome da rede virtual na lâmina de implantação do cluster no portal Azure.

2. Comente `nicPrefixOverride` o atributo de , porque está a usar a `Microsoft.Compute/virtualMachineScaleSets` sub-rede existente e desativou esta variável no passo 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Alterar a `vnetID` variável para apontar para a rede virtual existente:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. `Microsoft.Network/virtualNetworks`Remova dos seus recursos, para que o Azure não crie uma nova rede virtual:

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

5. Comente a rede virtual a partir `dependsOn` do atributo de , para que não `Microsoft.Compute/virtualMachineScaleSets` dependa da criação de uma nova rede virtual:

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

6. Implementar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Após a implementação, a sua rede virtual deve incluir os novos VMs definidos em escala. O tipo de nó de conjunto de escala de máquina virtual deve mostrar a rede virtual existente e a sub-rede. Também pode utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) para aceder ao VM que já se encontrava na rede virtual e para pingar os novos VMs definidos em escala:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Por outro exemplo, veja [um que não seja específico do Service Fabric.](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet)


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicionar parâmetros para o nome do grupo de recursos IP estáticos existente, nome e nome de domínio totalmente qualificado (FQDN):

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

2. Retire o `dnsName` parâmetro. (O endereço IP estático já tem um.)

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

4. `Microsoft.Network/publicIPAddresses`Remova dos seus recursos, para que o Azure não crie um novo endereço IP:

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

5. Comente o endereço IP a partir `dependsOn` do atributo de , para que não `Microsoft.Network/loadBalancers` dependa da criação de um novo endereço IP:

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

6. No `Microsoft.Network/loadBalancers` recurso, altere o `publicIPAddress` elemento de referência ao endereço IP `frontendIPConfigurations` estático existente em vez de um recém-criado:

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

7. No `Microsoft.ServiceFabric/clusters` recurso, altere `managementEndpoint` para o DNS FQDN do endereço IP estático. Se estiver a utilizar um cluster seguro, certifique-se de que muda *http://* para *https://*. (Note que este passo se aplica apenas aos clusters de Tecidos de Serviço. Se estiver a utilizar um conjunto de balança de máquina virtual, salte este passo.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implementar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Após a implementação, pode ver que o seu balanceador de carga está ligado ao endereço IP estático público do outro grupo de recursos. O ponto final de ligação ao cliente do Service Fabric e o ponto final [do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) apontam para o DNS FQDN do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Equilibrador de carga interno

Este cenário substitui o balançador de carga externo no modelo de tecido de serviço predefinido por um equilibrador de carga interno. Consulte [anteriormente no artigo](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) as implicações para o portal Azure e para o fornecedor de recursos de Tecido de Serviço.

1. Retire o `dnsName` parâmetro. (Não é necessário.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, se utilizar um método de alocação estática, pode adicionar um parâmetro de endereço IP estático. Se utilizar um método de atribuição dinâmico, não precisa de fazer este passo.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. `Microsoft.Network/publicIPAddresses`Remova dos seus recursos, para que o Azure não crie um novo endereço IP:

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

4. Remova o atributo de endereço IP `dependsOn` de , para que não `Microsoft.Network/loadBalancers` dependa de criar um novo endereço IP. Adicione o atributo de rede virtual `dependsOn` porque o balançador de carga agora depende da sub-rede da rede virtual:

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

5. Mude a regulação do balançador de carga `frontendIPConfigurations` de uma , para usar uma `publicIPAddress` sub-rede e `privateIPAddress` . `privateIPAddress` utiliza um endereço IP interno estático predefinido. Para utilizar um endereço IP dinâmico, remova o `privateIPAddress` elemento e, em seguida, `privateIPAllocationMethod` altere para **Dynamic**.

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

6. No `Microsoft.ServiceFabric/clusters` recurso, altere `managementEndpoint` para apontar para o endereço do balançador interno de carga. Se utilizar um cluster seguro, certifique-se de que muda *http://* para *https://*. (Note que este passo se aplica apenas aos clusters de Tecidos de Serviço. Se estiver a utilizar um conjunto de balança de máquina virtual, salte este passo.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implementar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Após a implementação, o seu equilibrador de carga utiliza o endereço IP estático privado 10.0.0.250. Se tiver outra máquina nessa mesma rede virtual, pode ir ao ponto final interno do [Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md) Note que se liga a um dos nós por trás do balançador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Equilibrador de carga interno e externo

Neste cenário, começa-se com o balanceador de carga externo do tipo único existente e adiciona-se um balançador interno de carga para o mesmo tipo de nó. Uma porta traseira anexada a um conjunto de endereços de back-end só pode ser atribuída a um único equilibrador de carga. Escolha qual o balanceador de carga que terá as portas de aplicação e qual o balanceador de carga que terá os seus pontos finais de gestão (portas 19000 e 19080). Se colocar os pontos finais de gestão no balançador interno de carga, lembre-se das restrições do fornecedor de recursos service Fabric discutidas [anteriormente no artigo](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). No exemplo que usamos, os pontos finais de gestão permanecem no balanceador de carga externo. Adicione também uma porta de aplicação de porta 80 e coloque-a no equilibrador interno de carga.

Num aglomerado de dois nós, um tipo de nó está no balançador de carga externo. O outro tipo de nó está no balançador interno de carga. Para utilizar um cluster de dois nós, no modelo de dois nós criado pelo portal (que vem com dois equilibradores de carga), altere o segundo equilibrador de carga para um equilibrador de carga interno. Para obter mais informações, consulte a secção [de balançadores de carga apenas internas.](#internallb)

1. Adicione o parâmetro de endereço IP do balançador interno de carga interno estático. (Para notas relacionadas com a utilização de um endereço IP dinâmico, consulte secções anteriores deste artigo.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione um parâmetro de porta de aplicação 80.

3. Para adicionar versões internas das variáveis de rede existentes, copiá-las e colar e adicionar "-Int" ao nome:

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

4. Se começar com o modelo gerado pelo portal que utiliza a porta de aplicação 80, o modelo de portal padrão adiciona AppPort1 (porta 80) no equilibrador de carga externo. Neste caso, remova o AppPort1 do equilibrador de carga externo `loadBalancingRules` e das sondas, para que possa adicioná-lo ao equilibrador de carga interno:

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

5. Adicione um segundo `Microsoft.Network/loadBalancers` recurso. Parece semelhante ao equilibrador de carga interno criado na secção [de balançador de carga interno,](#internallb) mas utiliza as variáveis de balançador de carga "Int" e implementa apenas a porta de aplicação 80. Isto também remove `inboundNatPools` , para manter os pontos finais rdp no balanceador de carga pública. Se quiser PDR no balançador interno de carga, passe `inboundNatPools` do balançador de carga externo para este equilibrista interno:

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

6. Para `networkProfile` o `Microsoft.Compute/virtualMachineScaleSets` recurso, adicione o conjunto de endereços internos de back-end:

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

7. Implementar o modelo:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Após a colocação, pode ver dois equilibradores de carga no grupo de recursos. Se navegar nos esquiliadores de carga, pode ver o endereço IP público e os pontos finais de gestão (portes 19000 e 19080) atribuídos ao endereço IP público. Também pode ver o endereço IP interno estático e o ponto final da aplicação (porta 80) atribuído ao balançador interno de carga. Ambos os equilibradores de carga usam a mesma balança de máquina virtual definida na parte de trás da piscina.

## <a name="notes-for-production-workloads"></a>Notas para cargas de trabalho de produção

Os modelos gitHub acima são projetados para trabalhar com o SKU padrão para Azure Standard Load Balancer (SLB), o SKU Básico. Este SLB não tem SLA, pelo que, para as cargas de trabalho de produção, o SKU Standard deve ser utilizado. Para mais informações, consulte a visão geral do [Balanço de Carga Padrão Azure](../load-balancer/load-balancer-overview.md). Qualquer cluster de tecido de serviço que utilize o SKU Standard para SLB precisa de garantir que cada tipo de nó tem uma regra que permite o tráfego de saída na porta 443. Isto é necessário para completar a configuração do cluster, e qualquer implementação sem tal regra falhará. No exemplo acima de um equilibrador de carga "apenas interno", deve ser adicionado um equilibrador de carga externo adicional ao gabarito com uma regra que permite o tráfego de saída para a porta 443.

## <a name="next-steps"></a>Passos seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)

Após a colocação, pode ver dois equilibradores de carga no grupo de recursos. Se navegar nos esquiliadores de carga, pode ver o endereço IP público e os pontos finais de gestão (portes 19000 e 19080) atribuídos ao endereço IP público. Também pode ver o endereço IP interno estático e o ponto final da aplicação (porta 80) atribuído ao balançador interno de carga. Ambos os equilibradores de carga usam a mesma balança de máquina virtual definida na parte de trás da piscina.
