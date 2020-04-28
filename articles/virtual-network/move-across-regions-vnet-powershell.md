---
title: Mova uma rede virtual Azure para outra região do Azure usando o Azure PowerShell
description: Mova uma rede virtual Azure de uma região azure para outra usando um modelo de Gestor de Recursos e Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646715"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Mova uma rede virtual Azure para outra região usando o Azure PowerShell

Existem vários cenários para mover uma rede virtual Azure existente de uma região para outra. Por exemplo, pode querer criar uma rede virtual com a mesma configuração para testes e disponibilidade que a sua rede virtual existente. Ou talvez queira mover uma rede virtual de produção para outra região como parte do seu planeamento de recuperação de desastres.

Você pode usar um modelo de Gestor de Recursos Azure para completar a mudança da rede virtual para outra região. Faz-o exportando a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre os modelos do Gestor de Recursos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a sua rede virtual está na região de Azure da qual pretende mudar-se.

- Para exportar uma rede virtual e implementar um modelo para criar uma rede virtual noutra região, é necessário ter o papel de Colaborador de Rede ou superior.

- Os pares de rede virtuais não serão recriados, e falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, tem de remover quaisquer pares de rede virtual. Em seguida, pode restabelece-los após o movimento da rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se a sua subscrição Azure permite criar redes virtuais na região alvo. Para ativar a quota necessária, contacte o suporte.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para apoiar a adição de redes virtuais para este processo. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Prepare-se para a mudança
Nesta secção, prepara a rede virtual para o movimento utilizando um modelo de Gestor de Recursos. Em seguida, move a rede virtual para a região alvo utilizando comandos Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para exportar a rede virtual e implantar a rede virtual alvo utilizando o PowerShell, faça o seguinte:

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e, em seguida, siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Obtenha o ID de recurso da rede virtual que pretende mover-se para a região alvo e, em seguida, colocá-lo numa variável utilizando [get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exporte a rede virtual de origem para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. O ficheiro descarregado tem o mesmo nome que o grupo de recursos de onde o recurso foi exportado. Localize o * \<ficheiro>.json* de nome de grupo de recursos, que exportou com o comando, e abra-o no seu editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Para editar o parâmetro do nome de rede virtual, altere a propriedade **padrãoValue** do nome de rede virtual fonte para o nome da sua rede virtual alvo. Certifique-se de que encerra o nome em aspas.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Para editar a região alvo onde a rede virtual será deslocada, altere a propriedade de **localização** sob recursos:

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
  
1. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Opcional) Também pode alterar outros parâmetros no * \<ficheiro>.json* de nome de grupo de recursos, dependendo dos seus requisitos:

    * **Espaço de endereço**: Antes de guardar o ficheiro, pode alterar o espaço de endereço da rede virtual modificando a secção de **endereços de** > **recursosSpace** e alterando a propriedade **de endereçoPrefixos:**

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

    * **Sub-rede**: Pode alterar ou adicionar ao nome da sub-rede e ao espaço de endereço da sub-rede alterando a secção de **sub-redes** do ficheiro. Pode alterar o nome da subnet alterando a propriedade do **nome.** E pode alterar o espaço de endereço da subnet alterando a propriedade **addressPrefix:**

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

        Para alterar o prefixo de endereço, edite o ficheiro em dois locais: no código na secção anterior e na secção do **tipo** do seguinte código. Altere a propriedade **de endereçoPrefix** no seguinte código para combinar com a propriedade **addressPrefix** no código na secção anterior.

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

1. Guarde o * \<ficheiro>.json de nome de grupo de recursos.*

1. Criar um grupo de recursos na região alvo para a rede virtual alvo a ser implantada utilizando o [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implemente o ficheiro * \<>.json* de nome de grupo de recursos editado para o grupo de recursos que criou no passo anterior utilizando a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Eliminar a rede virtual ou o grupo de recursos 

Depois de ter implantado a rede virtual, para recomeçar ou descartar a rede virtual na região alvo, eliminar o grupo de recursos que criou na região alvo, e a rede virtual móvel será eliminada. 

Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Limpeza

Para comprometer as suas alterações e completar o movimento de rede virtual, faça qualquer uma das seguintes:

* Elimine o grupo de recursos utilizando o [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Elimine a rede virtual de origem utilizando [remove-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você mudou uma rede virtual de uma região para outra usando powerShell e, em seguida, limpou os recursos de origem desnecessários. Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:

- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mova as máquinas virtuais Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
