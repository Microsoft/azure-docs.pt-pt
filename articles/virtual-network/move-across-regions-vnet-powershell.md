---
title: Mover uma rede virtual Azure para outra região do Azure utilizando a Azure PowerShell
description: Mover uma rede virtual Azure de uma região de Azure para outra utilizando um modelo de Gestor de Recursos e Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: bc504034f8d4565dd365b8d92dc2b2e6eadc1dae
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223334"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Mover uma rede virtual Azure para outra região usando a Azure PowerShell

Existem vários cenários para mover uma rede virtual Azure existente de uma região para outra. Por exemplo, é possível que pretenda criar uma rede virtual com a mesma configuração para testes e disponibilidade que a sua rede virtual existente. Ou talvez queira mover uma rede virtual de produção para outra região como parte do seu plano de recuperação de desastres.

Pode utilizar um modelo de Gestor de Recursos Azure para completar a mudança da rede virtual para outra região. Fá-lo exportando a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre os modelos do Gestor de Recursos, consulte [os grupos de recursos de exportação para os modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a sua rede virtual está na região de Azure de onde pretende mover-se.

- Para exportar uma rede virtual e implementar um modelo para criar uma rede virtual noutra região, é necessário ter o papel de Contribuinte de Rede ou superior.

- Os olhos de rede virtuais não serão recriados, e falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, tem de remover quaisquer pares de rede virtuais. Em seguida, pode restaurá-los após o movimento de rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se a sua subscrição Azure permite criar redes virtuais na região alvo. Para ativar a quota necessária, contacte o suporte.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de redes virtuais para este processo. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Preparem-se para a mudança
Nesta secção, prepara-se a rede virtual para o movimento utilizando um modelo de Gestor de Recursos. Em seguida, move a rede virtual para a região alvo utilizando comandos Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para exportar a rede virtual e implementar a rede virtual alvo utilizando o PowerShell, faça o seguinte:

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e, em seguida, siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Obtenha o ID de recursos da rede virtual que pretende mover-se para a região alvo e, em seguida, colocá-lo numa variável utilizando [a Get-AzVirtualNetwork:](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exporte a rede virtual de origem para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. O ficheiro descarregado tem o mesmo nome que o grupo de recursos de onde o recurso foi exportado. Localize o ficheiro *\<resource-group-name> .json,* que exportou com o comando, e, em seguida, abra-o no seu editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Para editar o parâmetro do nome de rede virtual, altere a propriedade **padrãoValue** do nome de rede virtual de origem para o nome da sua rede virtual alvo. Certifique-se de que encerra o nome em aspas.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Para editar a região-alvo onde a rede virtual será movida, altere a propriedade de **localização** sob recursos:

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
  
1. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation AzUre](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Opcional) Também pode alterar outros parâmetros no ficheiro *\<resource-group-name> .json,* dependendo dos seus requisitos:

    * **Espaço de endereço**: Antes de guardar o ficheiro, pode alterar o espaço de endereço da rede virtual modificando a secção de endereço **de**  >  **recursosSpace** e alterando a propriedade **de endereçoPrefixo:**

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

    * **Sub-rede**: Pode alterar ou adicionar ao nome da sub-rede e ao espaço do endereço da sub-rede alterando a secção **de sub-redes** do ficheiro. Pode alterar o nome da sub-rede alterando a propriedade **do nome.** E pode alterar o espaço de endereço da sub-rede alterando a propriedade **addressPrefix:**

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

        Para alterar o prefixo do endereço, edite o ficheiro em dois locais: no código na secção anterior e na secção **tipo** do seguinte código. Alterar o **endereçoPrefixo** no seguinte código para corresponder à propriedade **addressPrefix** no código na secção anterior.

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

1. Guarde o ficheiro *\<resource-group-name> .json.*

1. Criar um grupo de recursos na região alvo para que a rede virtual-alvo seja implantada através da utilização [do New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implemente o ficheiro *\<resource-group-name> .json* editado para o grupo de recursos que criou no passo anterior, utilizando o [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzVirtualNetwork:](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Eliminar a rede virtual ou o grupo de recursos 

Depois de ter implantado a rede virtual, para recomeçar ou descartar a rede virtual na região alvo, elimine o grupo de recursos que criou na região alvo, e a rede virtual movida será eliminada. 

Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Limpeza

Para cometer as suas alterações e completar o movimento de rede virtual, faça qualquer uma das seguintes alterações:

* Eliminar o grupo de recursos utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Elimine a rede virtual de origem utilizando [a Remove-AzVirtualNetwork:](/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma rede virtual de uma região para outra usando PowerShell e, em seguida, limpou os recursos de origem não necessários. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:

- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover máquinas virtuais Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)