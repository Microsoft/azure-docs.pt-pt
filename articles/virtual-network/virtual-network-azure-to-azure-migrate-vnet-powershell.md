---
title: Mover a rede virtual do Azure para outra região do Azure usando Azure PowerShell
description: Use Azure Resource Manager modelo para mover a rede virtual do Azure de uma região do Azure para outra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0037419570ceffcd11dbc8dfe85b45d7ec0ee8ce
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997448"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Mover a rede virtual do Azure para outra região usando o Azure PowerShell

Há vários cenários em que você deseja mover suas VNETs (redes virtuais) do Azure existentes de uma região para outra. Por exemplo, talvez você queira criar uma rede virtual com a mesma configuração para teste e disponibilidade de sua rede virtual existente. Você também pode querer mover uma rede virtual de produção para outra região como parte do planejamento de recuperação de desastre.

Você pode usar um modelo de Azure Resource Manager para concluir a movimentação da rede virtual para outra região. Você faz isso exportando a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região.  Para obter mais informações sobre o Gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se a rede virtual do Azure está na região do Azure da qual você deseja mover.

- Para exportar uma rede virtual e implantar um modelo para criar uma rede virtual em outra região, você precisará da função de colaborador de rede ou superior.

- Os emparelhamentos de rede virtual não serão recriados e ocorrerão falhas se ainda estiverem presentes no modelo.  Você precisará remover os pares de rede virtual antes de exportar o modelo e, em seguida, restabelecer os pares após a movimentação da rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se sua assinatura do Azure permite que você crie redes virtuais na região de destino usada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de redes virtuais para esse processo.  Confira [assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar a rede virtual para a movimentação usando um modelo do Resource Manager e mover a rede virtual para a região de destino usando comandos Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Exportar a rede virtual e implantar a rede virtual de destino com o PowerShell

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha a ID da assinatura na qual você deseja implantar a rede virtual de destino e coloque-a em uma variável usando [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):
   
    ```azurepowershell-interactive
    Get-AzSubscription | format-table

    $Subscription = "MySubscriptionID"
    ```
3. Obtenha a ID de recurso da rede virtual que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
4. Exporte a rede virtual de origem para um arquivo. JSON no diretório em que você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

5. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando denominado **< Resource-Group-name >. JSON** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

6. Para editar o parâmetro do nome da rede virtual, altere a propriedade **DefaultValue** do nome da rede virtual de origem para o nome da sua rede virtual de destino, verifique se o nome está entre aspas:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

7.  Para editar a região de destino em que a VNET será movida, altere a propriedade **local** em recursos:

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
  
8. Para obter códigos de localização de região, você pode usar o cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
9.  Você também pode alterar outros parâmetros no arquivo **<-Group-name >. JSON** se escolher e forem opcionais, dependendo dos seus requisitos:

    * **Espaço de endereço** -o espaço de endereço da VNET pode ser alterado antes de salvar, modificando a seção **recursos** > de**addressSpace** e alterando a propriedade **addressPrefixes** no **< Resource-Group-Name Arquivo >. JSON** :
    
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

    * **Sub** -rede-o nome da sub-rede e o espaço de endereço de sub-rede podem ser alterados ou adicionados ao modificando a seção **sub-redes** do **< nome do grupo de recursos > arquivo. JSON** . O nome da sub-rede pode ser alterado alterando a propriedade **Name** . O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **addressPrefix** no arquivo **< nome-do-grupo de recursos >. JSON** :
    
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
                    ],
    ```

    No **< Resource-Group-name > arquivo. JSON** , para alterar o prefixo do endereço, ele deve ser editado em dois locais, a seção listada acima e a seção **Type** listada abaixo.  Altere a propriedade **addressPrefix** para corresponder à que está acima:
                
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

10. Salve o **< nome do grupo de recursos > arquivo. JSON** .

11. Criar um grupo de recursos na região de destino para a VNET de destino a ser implantada usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
12. Implante o < edited **Resource-Group-name > arquivo. JSON** para o grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

13. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Dispensar 

Após a implantação, se você quiser iniciar ou descartar a rede virtual no destino, exclua o grupo de recursos que foi criado no destino e a rede virtual movida será excluída.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma rede virtual do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
