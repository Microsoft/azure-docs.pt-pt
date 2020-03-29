---
title: Mova o Balancer de Carga externo Azure para outra região do Azure usando o Azure PowerShell
description: Utilize o modelo azure Resource Manager para mover o Balancer de Carga externa Azure de uma região azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644822"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Mova o Balancer de Carga externo Azure para outra região usando o Azure PowerShell

Há vários cenários em que você gostaria de mover o seu equilíbrio de carga externa existente de uma região para outra. Por exemplo, pode querer criar um equilibrador de carga externo com a mesma configuração para o teste. Também pode querer mover um equilibrador de carga externo para outra região como parte do planeamento da recuperação de desastres.

Os equilibradores de carga externos azure não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente e IP público de um equilibrador de carga externo.  Em seguida, pode encenar o recurso noutra região exportando o equilibrante de carga e IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador de carga externa Azure está na região de Azure, a partir da qual pretende mover-se.

- Os equilibradores de carga externos azure não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração de equilíbrio de carga externa e implementar um modelo para criar um equilibrador de carga externo noutra região, você precisará do papel de Colaborador de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede, iPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga externos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviço do Azure, quotas e constrangimentos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o equilibrador de carga externo para o movimento utilizando um modelo de Gestor de Recursos e mover a configuração do equilíbrio de carga externa para a região alvo utilizando o Azure PowerShell.  Como parte deste processo, a configuração ip pública do equilibrante de carga externa deve ser incluída e deve ser feita primeiro antes de mover o equilibrador de carga externo.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportar o modelo ip público e implantação a partir de Azure PowerShell

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Obtenha o ID de recurso do IP público que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportar a fonte pública IP para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome IP público, altere o **predefinição** do imóvelValor do nome IP público de origem para o nome do seu IP público alvo, certifique-se de que o nome está em cotações:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Para editar a região alvo onde o IP público será movido, altere a propriedade de **localização** sob recursos:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Também pode alterar outros parâmetros do modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão alterando a propriedade de**nome** **sku** > no ficheiro ** \<>.json** de nome de grupo de recursos:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Para obter mais informações sobre as diferenças entre ips públicos básicos e padrão, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método** de atribuição de IP público e **tempo limite de inativo** - Pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou **Static** to **Dynamic**. O tempo inativo pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a sua quantidade desejada.  O predefinido é **de 4:**

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Para obter mais informações sobre os métodos de atribuição e os valores de tempo inativo, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Guarde o ** \<ficheiro>.json de nome de grupo de recursos.**

10. Crie um grupo de recursos na região alvo para que o IP público alvo seja implantado utilizando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implemente o ficheiro ** \<>.json** de nome de grupo de recursos editado para o grupo de recursos criado na fase anterior utilizando a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de equilíbrio de carga externa e implantar a partir de Azure PowerShell

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do equilibrador de carga externo que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportar a configuração do equilíbriode carga externa fonte para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do equilibrista de carga externo, altere o padrão de **propriedadeValor** do nome do equilibrista de carga externo de origem para o nome do seu equilibrador de carga externo alvo, certifique-se de que o nome está em aspas:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Para editar o valor do IP público alvo que foi movido acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colá-lo no ** \<ficheiro>.json** de nome de grupo de recursos.  Para obter o ID, utilize [o Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digite a variável e bata para mostrar o ID do recurso.  Realce o caminho de ID e copie-o para a área de sobre-a-bordo:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  No ficheiro ** \<>.json,** de nome de grupo de recursos, colhe o ID de **recurso** da variável em vez do **valor padrãoValue** no segundo parâmetro para o ID externo ip público, certifique-se de que encerra o caminho em aspas:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Se tiver configurado as regras de saída do NAT e de saída para o equilibrista de carga, estará presente uma terceira entrada neste ficheiro para o ID externo para o IP público de saída.  Repita os passos acima na **região alvo** para obter o ID para o iP público de saída e colar essa entrada no ** \<ficheiro>.json** de nome de grupo de recursos:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Para editar a região alvo onde será movida a configuração do equilíbrio de carga externa, altere a propriedade de **localização** sob **recursos** no ** \<ficheiro>.json de nome** de grupo de recursos:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Também pode alterar outros parâmetros do modelo se escolher, e é opcional dependendo dos seus requisitos:
    
    * **Sku** - Pode alterar o sku do equilibrador de carga externo na configuração de padrão para básico ou básico para padrão alterando a propriedade de**nome** **sku** > no ficheiro ** \<>.json** de nome de grupo de recursos:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter mais informações sobre as diferenças entre os equilibradores básicos e standard de carga sku, consulte a visão geral do [Equilíbrio de Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regras** de equilíbrio de carga - Pode adicionar ou remover regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do ** \<ficheiro>.json de nome** de grupo de recursos:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Para obter mais informações sobre as regras de equilíbrio de carga, consulte [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondas** - Pode adicionar ou remover uma sonda para o equilibrador de carga na configuração adicionando ou removendo entradas na secção de **sondas** do ** \<ficheiro>.json** de nome de grupo de recursos:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Para obter mais informações sobre as sondas de saúde Azure Load Balancer, consulte sondas de [saúde Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regras naT de entrada** - Pode adicionar ou remover as regras nat de entrada para o equilibrador de carga adicionando ou removendo entradas na secção **inboundNatRules** do ** \<ficheiro>.json de nome** de grupo de recursos:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do ficheiro ** \<>.json** de nome de grupo de recursos:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Para obter mais informações sobre as regras de entrada na NAT, consulte [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regras** de saída - Pode adicionar ou remover regras de saída na configuração editando a propriedade **outboundRules** no ficheiro ** \<>.json de nome de grupo de recursos:**

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Para obter mais informações sobre as regras de saída, consulte [as regras de saída do Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Guarde o ** \<ficheiro>.json de nome de grupo de recursos.**
    
10. Criar ou criar um grupo de recursos na região alvo para o equilibrador de carga externo alvo a ser implantado utilizando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). O grupo de recursos existente de cima também pode ser reutilizado como parte deste processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implemente o ficheiro ** \<>.json** de nome de grupo de recursos editado para o grupo de recursos criado na fase anterior utilizando a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Eliminar 

Após a implantação, se desejar recomeçar ou descartar o IP público e o equilibrador de carga no alvo, elimine o grupo de recursos que foi criado no alvo e o IP público em movido e o equilibrador de carga serão eliminados.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento do NSG, elimine a fonte NSG ou o grupo de recursos, utilize [remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) e [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um grupo de segurança da rede Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
