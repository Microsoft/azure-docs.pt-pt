---
title: Mover uma rede virtual do Azure para outra região do Azure usando Azure PowerShell
description: Mova uma rede virtual do Azure de uma região do Azure para outra usando um modelo do Resource Manager e Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646715"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Mover uma rede virtual do Azure para outra região usando Azure PowerShell

Há vários cenários para mover uma rede virtual do Azure existente de uma região para outra. Por exemplo, talvez você queira criar uma rede virtual com a mesma configuração para teste e disponibilidade como sua rede virtual existente. Ou talvez você queira mover uma rede virtual de produção para outra região como parte de seu planejamento de recuperação de desastre.

Você pode usar um modelo de Azure Resource Manager para concluir a movimentação da rede virtual para outra região. Você faz isso exportando a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região. Para obter mais informações sobre modelos do Resource Manager, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se sua rede virtual está na região do Azure da qual você deseja mover.

- Para exportar uma rede virtual e implantar um modelo para criar uma rede virtual em outra região, você precisa ter a função de colaborador de rede ou superior.

- Os emparelhamentos de rede virtual não serão recriados, e eles falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, você precisa remover os pares de rede virtual. Em seguida, você pode reestabelecer esses itens após a movimentação da rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se sua assinatura do Azure permite que você crie redes virtuais na região de destino. Para habilitar a cota necessária, contate o suporte.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de redes virtuais para esse processo. Para obter mais informações, veja [Subscrição do Azure e limites, quotas e restrições do serviço](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparar para a movimentação
Nesta seção, você prepara a rede virtual para a movimentação usando um modelo do Resource Manager. Em seguida, mova a rede virtual para a região de destino usando os comandos Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para exportar a rede virtual e implantar a rede virtual de destino usando o PowerShell, faça o seguinte:

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Obtenha a ID de recurso da rede virtual que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exporte a rede virtual de origem para um arquivo. JSON no diretório em que você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. O arquivo baixado tem o mesmo nome que o grupo de recursos do qual o recurso foi exportado. Localize o *\<nome do grupo de recursos > arquivo. JSON* , que você exportou com o comando e, em seguida, abra-o em seu editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Para editar o parâmetro do nome da rede virtual, altere a propriedade **DefaultValue** do nome da rede virtual de origem para o nome da sua rede virtual de destino. Certifique-se de colocar o nome entre aspas.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Para editar a região de destino em que a rede virtual será movida, altere a propriedade **local** em recursos:

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
  
1. Para obter códigos de localização de região, você pode usar o cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Adicional Você também pode alterar outros parâmetros no *\<Resource-Group-name > arquivo. JSON* , dependendo dos seus requisitos:

    * **Espaço de endereço**: antes de salvar o arquivo, você pode alterar o espaço de endereço da rede virtual modificando **os recursos** > seção **addressSpace** e alterando a propriedade **addressPrefixes** :

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

    * **Sub-rede**: você pode alterar ou adicionar o nome da sub-rede e o espaço de endereço da sub-rede alterando a seção **sub-redes** do arquivo. Você pode alterar o nome da sub-rede alterando a propriedade **Name** . E você pode alterar o espaço de endereço de sub-rede alterando a propriedade **addressPrefix** :

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

        Para alterar o prefixo de endereço, edite o arquivo em dois locais: no código da seção anterior e na seção **tipo** do código a seguir. Altere a propriedade **addressPrefix** no código a seguir para corresponder à propriedade **addressPrefix** no código da seção anterior.

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

1. Salve o *\<nome do grupo de recursos > arquivo. JSON* .

1. Crie um grupo de recursos na região de destino para a rede virtual de destino a ser implantada usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implante o\<edited *Resource-Group-name > arquivo. JSON* no grupo de recursos que você criou na etapa anterior usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Excluir a rede virtual ou o grupo de recursos 

Depois de implantar a rede virtual, para iniciar ou descartar a rede virtual na região de destino, exclua o grupo de recursos que você criou na região de destino e a rede virtual movida será excluída. 

Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual, siga um destes procedimentos:

* Exclua o grupo de recursos usando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Exclua a rede virtual de origem usando [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma rede virtual de uma região para outra usando o PowerShell e, em seguida, limpou os recursos de origem desnecessários. Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:

- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover máquinas virtuais do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
