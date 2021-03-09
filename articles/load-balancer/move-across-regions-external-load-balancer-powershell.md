---
title: Mover o balanceador de carga externo Azure para outra região do Azure utilizando a Azure PowerShell
description: Utilize o modelo Azure Resource Manager para mover o Azure external Load Balancer de uma região Azure para outra usando a Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 94cb46536bcf029a9e71a7238772ccc7b186b1dc
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500279"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Mover o balanceador de carga externo Azure para outra região usando a Azure PowerShell

Há vários cenários em que gostaria de mover o seu equilibrador de carga externo existente de uma região para outra. Por exemplo, é melhor criar um equilibrador de carga externo com a mesma configuração para testes. Também pode querer mover um equilibrador de carga externo para outra região como parte do planeamento de recuperação de desastres.

Os equilibradores externos de carga não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente e IP público de um equilibrador de carga externo.  Em seguida, pode encenar o recurso noutra região exportando o equilibrador de carga e o IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [grupos de recursos de exportação para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador de carga externo Azure se encontra na região de Azure, de onde pretende mover-se.

- Os equilibradores de carga externos não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração do balanceador de carga externa e implementar um modelo para criar um equilibrador de carga externo noutra região, você precisará da função de Contribuinte de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibrar cargas, grupos de segurança de rede, IPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga externos na região-alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviços da Azure, quotas e constrangimentos](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os passos seguintes mostram como preparar o balançador de carga externo para o movimento utilizando um modelo de Gestor de Recursos e mover a configuração do balançador de carga externo para a região-alvo utilizando a Azure PowerShell.  Como parte deste processo, a configuração IP pública do equilibrador de carga externo deve ser incluída e deve fazer-me primeiro antes de mover o balançador de carga externo.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de IP público e implementar a partir do Azure PowerShell

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Obtenha o ID de recursos do IP público que pretende mover-se para a região alvo e colocá-lo numa variável usando [Get-AzPublicIPAddress:](/powershell/module/az.network/get-azpublicipaddress)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte o IP público de origem para um ficheiro .json para o diretório onde executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado em homenagem ao grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado **\<resource-group-name> .json** e abra-o num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome IP público, altere o padrão de **propriedadeValue** do nome IP público de origem para o nome do seu IP público alvo, certifique-se de que o nome está em aspas:
    
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

6. Para editar a região-alvo onde o IP público será movido, altere o imóvel de **localização** sob recursos:

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
  
7. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation AzUre](/powershell/module/az.resources/get-azlocation) PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Também pode alterar outros parâmetros no modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão, alterando a propriedade do nome **sku**  >   no ficheiro **\<resource-group-name> .json:**

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

         Para obter mais informações sobre as diferenças entre ips públicos básicos e padrão sku, consulte [Criar, alterar ou eliminar um endereço IP público](../virtual-network/virtual-network-public-ip-address.md).

    * **Método de atribuição de IP público** e tempo limite de marcha **lenta** - Pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou **Static** to **Dynamic**. O tempo limite de marcha lenta pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a quantidade desejada.  O padrão é **4:**

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

        Para obter mais informações sobre os métodos de atribuição e os valores de tempo limite inativo, consulte [Criar, alterar ou eliminar um endereço IP público](../virtual-network/virtual-network-public-ip-address.md).


9. Guarde o ficheiro **\<resource-group-name> .json.**

10. Criar um grupo de recursos na região alvo para que o IP público-alvo seja implantado utilizando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implementar o ficheiro **\<resource-group-name> .json** editado para o grupo de recursos criado no passo anterior utilizando o [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [o Get-AzPublicIPAddress:](/powershell/module/az.network/get-azpublicipaddress)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de balançador de carga externo e implementar a partir da Azure PowerShell

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do equilibrador de carga externo que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a configuração do balançador de carga externo de origem para um ficheiro .json para o diretório onde executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. O ficheiro descarregado será nomeado em homenagem ao grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado **\<resource-group-name> .json** e abra-o num editor à sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do balançador de carga externo, altere o padrão de **propriedadeValue** do nome do balançador externo de origem para o nome do equilibrador de carga externo alvo, certifique-se de que o nome está nas ações:

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

6.  Para editar o valor do IP público-alvo que foi movido acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colá-lo no ficheiro **\<resource-group-name> .json.**  Para obter o ID, utilize [o Get-AzPublicIPAddress:](/powershell/module/az.network/get-azpublicipaddress)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digite a variável e acerte entrar para exibir o ID do recurso.  Realce o caminho de ID e copie-o para a área de transferência:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  No ficheiro **\<resource-group-name> .json,** cole o **ID** de recurso da variável no lugar do **padrãoValue** no segundo parâmetro para o ID externo IP público, certifique-se de que encerra o caminho em aspas:

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

8.  Se tiver configurado as regras de saída e de saída para o equilibrador de carga, uma terceira entrada estará presente neste ficheiro para o ID externo para o IP público de saída.  Repita os passos acima na **região alvo** para obter o ID para o iP público de saída e colar essa entrada no ficheiro **\<resource-group-name> .json:**

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

10. Para editar a região-alvo onde será movida a configuração do balançador de carga externa, altere a propriedade de **localização** sob **recursos** no ficheiro **\<resource-group-name> .json:**

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

11. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation AzUre](/powershell/module/az.resources/get-azlocation) PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Também pode alterar outros parâmetros no modelo se escolher, e é opcional dependendo dos seus requisitos:
    
    * **Sku** - Pode alterar o sku do balançador de carga externo na configuração de padrão para básico ou básico para padrão, alterando a propriedade do nome **sku**  >   no ficheiro **\<resource-group-name> .json:**

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
      Para obter mais informações sobre as diferenças entre os balanceadores de carga sku básico e padrão, consulte [a visão geral do Balançor de Carga Padrão Azure](./load-balancer-overview.md)

    * **Regras de equilíbrio de carga** - Pode adicionar ou remover as regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do ficheiro **\<resource-group-name> .json:**

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
       Para obter mais informações sobre regras de equilíbrio de carga, consulte [o que é o Equilibr de Carga Azure?](./load-balancer-overview.md)

    * **Sondas** - Pode adicionar ou remover uma sonda para o balançador de carga na configuração adicionando ou removendo entradas na secção de **sondas** do ficheiro **\<resource-group-name> .json:**

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
       Para obter mais informações sobre as sondas de saúde do Azure Load Balancer, consulte [as sondas de saúde do Balancer de Carga](./load-balancer-custom-probe-overview.md)

    * **Regras NAT de entrada** - Pode adicionar ou remover as regras NAT de entrada para o equilibrador de carga adicionando ou removendo entradas para a secção **de inboundNatRules** do ficheiro **\<resource-group-name> .json:**

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do ficheiro **\<resource-group-name> .json:**

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
        Para obter mais informações sobre as regras NAT de entrada, consulte [o que é o Equilibr de Carga Azure?](./load-balancer-overview.md)

    * **Regras de saída** - Pode adicionar ou remover as regras de saída na configuração editando a propriedade **outboundRules** no ficheiro **\<resource-group-name> .json:**

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

         Para obter mais informações sobre as regras de saída, consulte [as regras de saída do Load Balancer](./load-balancer-outbound-connections.md#outboundrules)

13. Guarde o ficheiro **\<resource-group-name> .json.**
    
10. Criar ou criar um grupo de recursos na região-alvo para o balanceador de carga externo alvo a ser implantado utilizando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O grupo de recursos existente de cima também pode ser reutilizado como parte deste processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implementar o ficheiro **\<resource-group-name> .json** editado para o grupo de recursos criado no passo anterior utilizando o [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [o Get-AzLoadBalancer:](/powershell/module/az.network/get-azloadbalancer)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Eliminar 

Após a implantação, se pretender recomeçar ou descartar o IP público e o equilibrador de carga no alvo, elimine o grupo de recursos criado no alvo e o IP público movido e o balançador de carga serão eliminados.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento do NSG, elimine a fonte NSG ou grupo de recursos, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) e [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um grupo de segurança da rede Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)