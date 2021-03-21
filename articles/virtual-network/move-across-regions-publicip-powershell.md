---
title: Mover a configuração IP pública do Azure para outra região do Azure utilizando a Azure PowerShell
description: Utilize o modelo Azure Resource Manager para mover a configuração IP pública do Azure de uma região Azure para outra usando a Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938696"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Mover a configuração IP pública do Azure para outra região usando a Azure PowerShell

Existem vários cenários em que você gostaria de mover as suas configurações de IP público azure existentes de uma região para outra. Por exemplo, pode querer criar um IP público com a mesma configuração e sku para testes. Você também pode querer mover uma configuração ip pública para outra região como parte do planeamento de recuperação de desastres.

**Os IPs públicos do Azure são específicos da região e não podem ser movidos de uma região para outra.** No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente de um IP público.  Em seguida, pode encenar o recurso noutra região exportando o IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [grupos de recursos de exportação para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o Azure Public IP se encontra na região de Azure, de onde pretende mover-se.

- Os IPs públicos de Azure não podem ser movidos entre regiões.  Terá de associar o novo IP público aos recursos na região alvo.

- Para exportar uma configuração IP pública e implementar um modelo para criar um IP público noutra região, você precisará do papel de Contribuinte de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se a sua subscrição Azure permite criar IPs públicos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de IPs públicos para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os passos seguintes mostram como preparar o IP público para o movimento de configuração usando um modelo de Gestor de Recursos e mover a configuração IP pública para a região alvo usando Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exporte o modelo e implemente a partir de um script

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recursos do IP público que pretende mover-se para a região alvo e colocá-lo numa variável usando [Get-AzPublicIPAddress:](/powershell/module/az.network/get-azpublicipaddress)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um ficheiro .json para o diretório onde executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado em homenagem ao grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado **\<resource-group-name> .json** e abra-o num editor à sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

         Para obter mais informações sobre as diferenças entre ips públicos básicos e padrão sku, consulte [Criar, alterar ou eliminar um endereço IP público](./virtual-network-public-ip-address.md).

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

        Para obter mais informações sobre os métodos de atribuição e os valores de tempo limite inativo, consulte [Criar, alterar ou eliminar um endereço IP público](./virtual-network-public-ip-address.md).


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
## <a name="discard"></a>Eliminar 

Após a implementação, se pretender recomeçar ou descartar o ip público no alvo, elimine o grupo de recursos criado no alvo e o IP público movido será eliminado.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento da rede virtual, elimine a rede virtual de origem ou o grupo de recursos, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um IP público do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
