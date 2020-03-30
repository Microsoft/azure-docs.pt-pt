---
title: Padrões de networking para tecido de serviço Azure
description: Descreve padrões comuns de networking para o Tecido de Serviço e como criar um cluster utilizando funcionalidades de networking Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466286"
---
# <a name="service-fabric-networking-patterns"></a>Padrões de rede de tecido de serviço
Pode integrar o seu cluster Azure Service Fabric com outras funcionalidades de networking Azure. Neste artigo, mostramos-lhe como criar clusters que usam as seguintes funcionalidades:

- [Rede virtual existente ou sub-rede](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Equilibrador de carga interna](#internallb)
- [Equilibrador de carga interno e externo](#internalexternallb)

O tecido de serviço funciona num conjunto de escala de máquina virtual padrão. Qualquer funcionalidade que possa utilizar num conjunto de escala de máquina virtual, pode utilizar com um cluster de Tecido de Serviço. As secções de rede dos modelos do Gestor de Recursos Azure para conjuntos de escala de máquinavirtual e tecido de serviço são idênticas. Depois de se implantar numa rede virtual existente, é fácil incorporar outras funcionalidades de networking, como o Azure ExpressRoute, o Azure VPN Gateway, um grupo de segurança de rede e o peering de rede virtual.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Permitindo ao fornecedor de recursos service Fabric consultar o seu cluster

O Service Fabric é único a partir de outras funcionalidades de networking num aspeto. O [portal Azure](https://portal.azure.com) utiliza internamente o fornecedor de recursos service Fabric para chamar um cluster para obter informações sobre nós e aplicações. O prestador de recursos service Fabric requer acesso de entrada acessível ao acesso acessível ao portal http (porta 19080, por defeito) no ponto final da gestão. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) utiliza o ponto final de gestão para gerir o seu cluster. O fornecedor de recursos Service Fabric também utiliza esta porta para consultar informações sobre o seu cluster, para exibir no portal Azure. 

Se o porto 19080 não estiver acessível ao fornecedor de recursos service Fabric, uma mensagem como *Nós Não Encontrados* aparece no portal, e o seu nó e lista de aplicações parece vazio. Se quiser ver o seu cluster no portal Azure, o seu equilibrador de carga deve expor um endereço IP público, e o seu grupo de segurança de rede deve permitir o tráfego da porta de entrada 19080. Se a sua configuração não cumprir estes requisitos, o portal Azure não mostra o estado do seu cluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Modelos

Todos os modelos de tecido de serviço estão no [GitHub.](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking) Deverá ser capaz de implantar os modelos como está utilizando os seguintes comandos PowerShell. Se estiver a implementar o modelo de rede virtual Azure existente ou o modelo ip público estático, leia primeiro a secção inicial de [configuração](#initialsetup) deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo seguinte, começamos com uma rede virtual existente chamada ExistingRG-vnet, no grupo de recursos **ExistenteRG.** A sub-rede chama-se padrão. Estes recursos padrão são criados quando se utiliza o portal Azure para criar uma máquina virtual padrão (VM). Poderia criar a rede virtual e a sub-rede sem criar o VM, mas o principal objetivo de adicionar um cluster a uma rede virtual existente é fornecer conectividade de rede a outros VMs. A criação do VM dá um bom exemplo de como uma rede virtual existente é normalmente utilizada. Se o seu cluster Service Fabric utilizar apenas um equilibrador de carga interna, sem um endereço IP público, pode utilizar o VM e o seu IP público como uma caixa de *salto*segura .

### <a name="static-public-ip-address"></a>Endereço IP público estático

Um endereço IP público estático geralmente é um recurso dedicado que é gerido separadamente dos VM ou VMs a que é atribuído. É aprovisionado num grupo dedicado de recursos de rede (ao contrário do próprio grupo de recursos de cluster Fabric). Crie um endereço IP público estático chamado staticIP1 no mesmo grupo de recursos Existenterg, quer no portal Azure quer utilizando o PowerShell:

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

Nos exemplos deste artigo, usamos o modelo de tecido de serviço.json. Pode utilizar o assistente padrão do portal para descarregar o modelo a partir do portal antes de criar um cluster. Também pode utilizar um dos modelos de [amostra,](https://github.com/Azure-Samples/service-fabric-cluster-templates)como o cluster de tecido de [serviço seguro de cinco nós.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual existente ou sub-rede

1. Mude o parâmetro da sub-rede para o nome da subnet existente e, em seguida, adicione dois novos parâmetros para referência à rede virtual existente:

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

2. Comente `nicPrefixOverride` o `Microsoft.Compute/virtualMachineScaleSets`atributo de , porque está a usar a subnet existente e desativou esta variável no passo 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Alterar `vnetID` a variável para apontar para a rede virtual existente:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Remova `Microsoft.Network/virtualNetworks` dos seus recursos, para que o Azure não crie uma nova rede virtual:

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

5. Comente a rede `dependsOn` virtual a `Microsoft.Compute/virtualMachineScaleSets`partir do atributo de , para que não dependa da criação de uma nova rede virtual:

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

    Após a implantação, a sua rede virtual deve incluir os novos VMs conjuntos de escala. O tipo de nó de escala de máquina virtual deve mostrar a rede virtual existente e a sub-rede. Também pode utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) para aceder ao VM que já estava na rede virtual e para pingar os novos VMs conjuntos de escala:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Por outro exemplo, consulte [um que não seja específico do Tecido de Serviço](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicione parâmetros para o nome do grupo, nome e nome de domínio ip estático existente (FQDN):

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

2. Retire `dnsName` o parâmetro. (O endereço IP estático já tem um.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adicione uma variável para referência ao endereço IP estático existente:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Retire `Microsoft.Network/publicIPAddresses` dos seus recursos, para que o Azure não crie um novo endereço IP:

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

5. Comente o endereço `dependsOn` IP a `Microsoft.Network/loadBalancers`partir do atributo de , para que não dependa da criação de um novo endereço IP:

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

6. No `Microsoft.Network/loadBalancers` recurso, altere `publicIPAddress` `frontendIPConfigurations` o elemento de referência do endereço IP estático existente em vez de um recém-criado:

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

7. No `Microsoft.ServiceFabric/clusters` recurso, `managementEndpoint` altere para o DNS FQDN do endereço IP estático. Se estiver a utilizar um cluster seguro, certifique-se de que muda *http://* para *https://*. (Note que este passo se aplica apenas aos clusters de tecido de serviço. Se estiver a utilizar um conjunto de escala de máquina virtual, ignore este passo.)

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

Após a implantação, pode ver que o seu equilibrador de carga está ligado ao endereço IP estático público do outro grupo de recursos. O ponto final de ligação ao cliente do Tecido de Serviço e o ponto final do Explorador de [Tecidode Serviço](service-fabric-visualizing-your-cluster.md) apontam para o DNS FQDN do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Equilibrador de carga interna

Este cenário substitui o equilibrante de carga externo no modelo de tecido de serviço predefinido por um equilibrador de carga interna. Consulte [anteriormente no artigo](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) as implicações para o portal Azure e para o fornecedor de recursos Service Fabric.

1. Retire `dnsName` o parâmetro. (Não é necessário.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, se utilizar um método de atribuição estático, pode adicionar um parâmetro de endereço IP estático. Se utilizar um método de atribuição dinâmico, não precisa de fazer este passo.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Retire `Microsoft.Network/publicIPAddresses` dos seus recursos, para que o Azure não crie um novo endereço IP:

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

4. Remova o `dependsOn` atributo `Microsoft.Network/loadBalancers`do endereço IP de , para que não dependa da criação de um novo endereço IP. Adicione o `dependsOn` atributo da rede virtual porque o equilibrador de carga depende agora da subnet a partir da rede virtual:

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

5. Altere a regulação `frontendIPConfigurations` do equilíbrio `publicIPAddress`de carga de `privateIPAddress`utilização de a, para utilização de uma sub-rede e . `privateIPAddress`utiliza um endereço IP interno estático predefinido. Para utilizar um endereço IP `privateIPAddress` dinâmico, retire `privateIPAllocationMethod` o elemento e, em seguida, mude para **Dynamic**.

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

6. No `Microsoft.ServiceFabric/clusters` recurso, `managementEndpoint` altere para apontar para o endereço do equilibrador de carga interno. Se utilizar um cluster seguro, certifique-se de que muda *http://* para *https://*. (Note que este passo se aplica apenas aos clusters de tecido de serviço. Se estiver a utilizar um conjunto de escala de máquina virtual, ignore este passo.)

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

Após a implantação, o seu equilibrador de carga utiliza o endereço IP estático privado 10.0.0.250. Se tiver outra máquina nessa mesma rede virtual, pode ir ao ponto final do [Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md) Note que se liga a um dos nódosos por trás do equilibrador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Equilibrador de carga interno e externo

Neste cenário, começa-se pelo equilibrador externo de carga do tipo nó único existente e adiciona-se um equilibrante interno de carga para o mesmo tipo de nó. Uma porta traseira anexa a uma piscina de endereços traseiros só pode ser atribuída a um único equilibrador de carga. Escolha qual o equilibrador de carga que terá as suas portas de aplicação e qual o equilibrador de carga que terá os seus pontos finais de gestão (portas 19000 e 19080). Se colocar os pontos finais de gestão no equilibrador interno de carga, tenha em mente as restrições de prestador de recursos do Service Fabric discutidas [anteriormente no artigo](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). No exemplo que utilizamos, os pontos finais de gestão permanecem no equilibrador de carga externo. Adicione também uma porta de aplicação porta 80 e coloque-a no equilibrador interno de carga.

Num cluster de dois nós, um tipo de nó está no equilibrista de carga externo. O outro tipo de nó está no equilibrista interno de carga. Para utilizar um cluster de dois nós, no modelo de dois nós criado pelo portal (que vem com dois equilibradores de carga), mude o segundo equilibrante de carga para um equilibrante interno de carga. Para mais informações, consulte a secção de [equilíbrio de carga interna.](#internallb)

1. Adicione o parâmetro de endereço IP do equilíbrio interno estático. (Para notas relacionadas com a utilização de um endereço IP dinâmico, consulte secções anteriores deste artigo.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione uma porta de aplicação 80 parâmetro.

3. Para adicionar versões internas das variáveis de rede existentes, copie e cole-as, e adicione "-Int" ao nome:

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

4. Se começar com o modelo gerado pelo portal que utiliza a porta de aplicação 80, o modelo de portal padrão adiciona AppPort1 (porta 80) no equilibrador de carga externo. Neste caso, remova o AppPort1 do `loadBalancingRules` equilibrador de carga externo e das sondas, para que possa adicioná-lo ao equilibrador interno de carga:

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

5. Adicione um `Microsoft.Network/loadBalancers` segundo recurso. Parece semelhante ao equilibrista interno de carga criado na secção de equilíbrio de [carga interna,](#internallb) mas utiliza as variáveis de equilíbrio de carga "-Int", e implementa apenas a porta de aplicação 80. Isto também `inboundNatPools`remove, para manter os pontos finais do PDR no equilíbrio de carga pública. Se pretender RDP no equilibrador `inboundNatPools` de carga interno, passe do equilibrador de carga externo para este equilibrador de carga interno:

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

Após a implantação, pode ver dois equilibradores de carga no grupo de recursos. Se você navegar nos equilibradores de carga, você pode ver o endereço IP público e pontos finais de gestão (portas 19000 e 19080) atribuídos ao endereço IP público. Pode também ver o endereço IP interno estático e o ponto final de aplicação (porta 80) atribuído ao equilibrador interno de carga. Ambos os equilibradores de carga utilizam a mesma balança de calcário virtual definida na parte de trás.

## <a name="notes-for-production-workloads"></a>Notas para cargas horárias de produção

Os modelos GitHub acima são projetados para funcionar com o SKU padrão para o Equilíbrio de Carga Padrão Azure (SLB), o SKU Básico. Este SLB não tem SLA, pelo que para as cargas de trabalho de produção o SKU padrão deve ser utilizado. Para mais informações, consulte a visão geral do [Balancer de Carga Padrão Azure](/azure/load-balancer/load-balancer-standard-overview). Qualquer cluster de tecido de serviço que utilize o SKU padrão para SLB precisa de garantir que cada tipo de nó tem uma regra que permite o tráfego de saída na porta 443. Isto é necessário para completar a configuração do cluster, e qualquer implantação sem tal regra falhará. No exemplo acima de um equilibrador de carga "interno", deve ser adicionado ao modelo um equilibrante de carga externo adicional com uma regra que permita o tráfego de saída para a porta 443.

## <a name="next-steps"></a>Passos seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)

Após a implantação, pode ver dois equilibradores de carga no grupo de recursos. Se você navegar nos equilibradores de carga, você pode ver o endereço IP público e pontos finais de gestão (portas 19000 e 19080) atribuídos ao endereço IP público. Pode também ver o endereço IP interno estático e o ponto final de aplicação (porta 80) atribuído ao equilibrador interno de carga. Ambos os equilibradores de carga utilizam a mesma balança de calcário virtual definida na parte de trás.

