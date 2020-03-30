---
title: Mova o Balancer interno azure para outra região azure usando o Azure PowerShell
description: Use o modelo do Gestor de Recursos Azure para mover o Balancer de Carga Interna Azure de uma região azure para outra usando o Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644278"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Mova o Balancer de Carga interna Azure para outra região usando a PowerShell

Há vários cenários em que você gostaria de mover o seu equilibrador interno de carga existente de uma região para outra. Por exemplo, pode querer criar um equilibrador de carga interno com a mesma configuração para o teste. Também pode querer mover um equilibrador interno de carga para outra região como parte do planeamento de recuperação de desastres.

Os equilibradores de carga internos azure não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente e a rede virtual de um equilibrador de carga interna.  Em seguida, pode encenar o recurso noutra região exportando o equilibrante de carga e a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador de carga interna Azure está na região de Azure, de onde pretende mover-se.

- Os equilibradores de carga internos azure não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração interna do equilíbrio de carga e implementar um modelo para criar um equilibrador de carga interno noutra região, você precisará do papel de Colaborador de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga internos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviço do Azure, quotas e constrangimentos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o equilibrador de carga interna para o movimento utilizando um modelo de Gestor de Recursos, e mover a configuração interna do equilíbrio de carga para a região alvo utilizando o Azure PowerShell.  Como parte deste processo, a configuração da rede virtual do equilibrista interno de carga deve ser incluída e deve ser feita primeiro antes de mover o equilibrador interno de carga.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de rede virtual e implantar a partir de Azure PowerShell

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Obtenha o ID de recurso da rede virtual que pretende mover-se para a região alvo e colocá-la numa variável utilizando [get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome da rede virtual, altere o padrão de **propriedadeValor** do nome de rede virtual fonte para o nome da sua rede virtual alvo, certifique-se de que o nome está em aspas:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Para editar a região alvo onde o VNET será movido, altere a propriedade de **localização** sob recursos:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Também pode alterar outros parâmetros no ** \<ficheiro>.json de nome** de grupo de recursos, se assim o desejar, e é opcional dependendo dos seus requisitos:

    * **Endereço Espaço** - O espaço de endereço do VNET pode ser alterado antes de poupar modificando a secção de **endereços** > **de recursosSpace** e alterando a propriedade **de endereçoPrefixos** no ficheiro de nome de grupo de ** \<recursos>.json:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnet** - O nome da sub-rede e o espaço de endereço da sub-rede podem ser alterados ou adicionados modificando a secção de **sub-redes** do ** \<ficheiro>.json** de nome de grupo de recursos. O nome da subnet pode ser alterado alterando a propriedade do **nome.** O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **de endereçoPrefix** no ficheiro ** \<>.json de nome** de grupo de recursos:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         No ficheiro ** \<>.json,** de nome de grupo de recursos, para alterar o prefixo de endereço, deve ser editado em dois locais, na secção acima listada e na secção de **tipo** listada abaixo.  Altere a propriedade **de endereçoPrefix** para corresponder à acima:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Guarde o ** \<ficheiro>.json de nome de grupo de recursos.**

10. Criar um grupo de recursos na região alvo para que o VNET alvo seja implantado usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implemente o ficheiro ** \<>.json** de nome de grupo de recursos editado para o grupo de recursos criado na fase anterior utilizando a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de equilíbrio interno de carga e implantar a partir de Azure PowerShell

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do equilibrador de carga interna que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportar a configuração do equilíbriodor de carga interna de origem para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do equilibrista interno de carga, altere o padrão de **propriedadeValor** do nome do equilibrista de carga interna fonte para o nome do seu equilibrador de carga interna alvo, certifique-se de que o nome está em aspas:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Para editar o valor da rede virtual alvo que foi movida acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colar no ficheiro ** \<>.json** de nome de grupo de recursos.  Para obter o ID, utilize [get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digite a variável e bata para mostrar o ID do recurso.  Realce o caminho de ID e copie-o para a área de sobre-a-bordo:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  No ficheiro ** \<>.json,** de nome de grupo de recursos, colhe o ID de **recurso** da variável em vez do **valor padrãoValue** no segundo parâmetro para o ID de rede virtual alvo, certifique-se de que encerra o caminho em aspas:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Para editar a região alvo onde será movida a configuração interna do equilíbrio de carga, altere a propriedade de **localização** sob **recursos** no ** \<ficheiro>.json** de nome de grupo de recursos:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
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
    
    * **Sku** - Pode alterar o sku do equilibrador de carga interno na configuração de padrão para básico ou básico para padrão alterando a propriedade de**nome** **sku** > no ficheiro ** \<>.json de nome** de grupo de recursos:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
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
    
13. Guarde o ** \<ficheiro>.json de nome de grupo de recursos.**
    
10. Criar ou criar um grupo de recursos na região alvo para que o equilibrador de carga interna alvo seja implantado utilizando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). O grupo de recursos existente de cima também pode ser reutilizado como parte deste processo:
    
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

Após a implementação, se desejar recomeçar ou descartar a rede virtual e o equilíbrio de carga no alvo, elimine o grupo de recursos que foi criado no alvo e a rede virtual móvel e o equilíbrio de carga serão eliminados.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento do NSG, elimine a fonte NSG ou o grupo de recursos, utilize [remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) e [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um equilibrador de carga interna Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
