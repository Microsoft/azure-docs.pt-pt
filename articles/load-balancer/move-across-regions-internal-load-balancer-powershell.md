---
title: Mover o balançador interno de carga Azure para outra região do Azure utilizando a Azure PowerShell
description: Utilize o modelo do Gestor de Recursos Azure para mover o Equilibr de Carga Interno Azure de uma região Azure para outra usando a Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 63083c4bd058c63e21a40f2d245312a3f010b696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808364"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Mover o balançador interno de carga Azure para outra região usando o PowerShell

Há vários cenários em que gostaria de mover o seu equilibrador interno de carga de uma região para outra. Por exemplo, é melhor criar um equilibrador de carga interno com a mesma configuração para testes. Também pode querer mover um equilibrador interno de carga para outra região como parte do planeamento de recuperação de desastres.

Os equilibradores internos de carga não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente e a rede virtual de um equilibrador de carga interno.  Em seguida, pode encenar o recurso noutra região exportando o equilibrador de carga e a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [grupos de recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador interno Azure se encontra na região de Azure, de onde pretende mover-se.

- Os equilibradores internos de carga não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração interna do balanceador de carga e implementar um modelo para criar um equilibrador de carga interno noutra região, você precisará do papel de Contribuinte de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibrar cargas, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga internos na região-alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviços da Azure, quotas e constrangimentos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os passos seguintes mostram como preparar o equilibrador de carga interno para o movimento utilizando um modelo de Gestor de Recursos e mover a configuração interna do balançador de carga para a região-alvo utilizando a Azure PowerShell.  Como parte deste processo, a configuração de rede virtual do balançador de carga interna deve ser incluída e deve ser feita primeiro antes de mover o balançador interno de carga.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de rede virtual e implementar a partir do Azure PowerShell

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Obtenha o ID de recursos da rede virtual que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [a Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um ficheiro .json para o diretório onde executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado em homenagem ao grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<resource-group-name> .json** e abra-o num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome de rede virtual, altere o padrão de **propriedadeValue** do nome de rede virtual de origem para o nome da sua rede virtual alvo, certifique-se de que o nome está em aspas:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Para editar a região-alvo onde o VNET será movido, altere a propriedade de **localização** sob recursos:

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
  
7. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation AzUre](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Também pode alterar outros parâmetros no ficheiro ** \<resource-group-name> .json,** se quiser, e é opcional dependendo dos seus requisitos:

    * **Address Space** - O espaço de endereço do VNET pode ser alterado antes de economizar modificando a secção de endereço **de**  >  **recursosSpace** e alterando a propriedade **de endereçoPrefixo** no ficheiro ** \<resource-group-name> .json:**

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

    * **Sub-rede** - O nome da sub-rede e o espaço do endereço da sub-rede podem ser alterados ou adicionados modificando a secção de **sub-redes** do ficheiro ** \<resource-group-name> .json.** O nome da sub-rede pode ser alterado alterando a propriedade **do nome.** O espaço de endereço da sub-rede pode ser alterado alterando a propriedade **addressPrefix** no ficheiro ** \<resource-group-name> .json:**

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

         No ficheiro ** \<resource-group-name> .json,** para alterar o prefixo do endereço, este deve ser editado em dois locais, a secção listada acima e a secção **de tipo** listada abaixo.  Alterar a **propriedade AddressPrefix** para corresponder à acima:

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

9.  Guarde o ficheiro ** \<resource-group-name> .json.**

10. Criar um grupo de recursos na região alvo para que o VNET-alvo seja implantado usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implementar o ficheiro ** \<resource-group-name> .json** editado para o grupo de recursos criado no passo anterior utilizando o [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Para verificar os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo interno do balançador de carga e implementar a partir da Azure PowerShell

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recursos do equilibrador de carga interno que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a configuração do balançador interno de carga de origem para um ficheiro .json para o diretório onde executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. O ficheiro descarregado será nomeado em homenagem ao grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<resource-group-name> .json** e abra-o num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do balançador de carga interno, altere o padrão de **propriedadeValue** do nome do equilibrador interno de carga de origem para o nome do equilibrador interno de carga do seu alvo, certifique-se de que o nome está nas ações:

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
 
6. Para editar o valor da rede virtual alvo que foi movida acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colá-lo no ficheiro ** \<resource-group-name> .json.**  Para obter o ID, utilize [a Rede Get-AzVirtual:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digite a variável e acerte entrar para exibir o ID do recurso.  Realce o caminho de ID e copie-o para a área de transferência:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  No ficheiro ** \<resource-group-name> .json,** cole o **ID** de recurso da variável no lugar do **padrãoValue** no segundo parâmetro para o ID da rede virtual alvo, certifique-se de que encerra o caminho em aspas:
   
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

8. Para editar a região-alvo onde será movida a configuração interna do balançador de carga, altere a propriedade de **localização** sob **recursos** no ficheiro ** \<resource-group-name> .json:**

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

11. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation AzUre](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Também pode alterar outros parâmetros no modelo se escolher, e é opcional dependendo dos seus requisitos:
    
    * **Sku** - Pode alterar o sku do balançador de carga interno na configuração de padrão para básico ou básico para padrão, alterando a propriedade do nome **sku**  >  **name** no ficheiro ** \<resource-group-name> .json:**

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
      Para obter mais informações sobre as diferenças entre os balanceadores de carga sku básico e padrão, consulte [a visão geral do Balançor de Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regras de equilíbrio de carga** - Pode adicionar ou remover as regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do ficheiro ** \<resource-group-name> .json:**

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
       Para obter mais informações sobre regras de equilíbrio de carga, consulte [o que é o Equilibr de Carga Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondas** - Pode adicionar ou remover uma sonda para o balançador de carga na configuração adicionando ou removendo entradas na secção de **sondas** do ficheiro ** \<resource-group-name> .json:**

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
       Para obter mais informações sobre as sondas de saúde do Azure Load Balancer, consulte [as sondas de saúde do Balancer de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regras NAT de entrada** - Pode adicionar ou remover as regras NAT de entrada para o equilibrador de carga adicionando ou removendo entradas para a secção **de inboundNatRules** do ficheiro ** \<resource-group-name> .json:**

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do ficheiro ** \<resource-group-name> .json:**

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
        Para obter mais informações sobre as regras NAT de entrada, consulte [o que é o Equilibr de Carga Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Guarde o ficheiro ** \<resource-group-name> .json.**
    
10. Criar ou criar um grupo de recursos na região-alvo para o balanceador de carga interno alvo a ser implantado utilizando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). O grupo de recursos existente de cima também pode ser reutilizado como parte deste processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implementar o ficheiro ** \<resource-group-name> .json** editado para o grupo de recursos criado no passo anterior utilizando o [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

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

Após a implementação, se pretender recomeçar ou descartar a rede virtual e o balançador de carga no alvo, elimine o grupo de recursos criado no alvo e a rede virtual e o balançador de carga movidos serão eliminados.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento do NSG, elimine a fonte NSG ou grupo de recursos, utilize [o Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) e [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu um equilibrador interno Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
