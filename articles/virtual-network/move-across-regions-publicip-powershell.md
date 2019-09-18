---
title: Mover o IP público do Azure para outra região do Azure usando Azure PowerShell
description: Use Azure Resource Manager modelo para mover o IP público do Azure de uma região do Azure para outra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: d18dfa7ebed3aefbf6fdb3ffdb6fdd2cf2160cb4
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71038930"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Mover o IP público do Azure para outra região usando Azure PowerShell

Há vários cenários em que você deseja mover seus IPs públicos do Azure existentes de uma região para outra. Por exemplo, talvez você queira criar um IP público com a mesma configuração e SKU para teste. Você também pode querer mover um IP público para outra região como parte do planejamento de recuperação de desastre.

Os IPs públicos do Azure são específicos da região e não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar a configuração existente de um IP público.  Em seguida, você pode preparar o recurso em outra região exportando o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo na nova região.  Para obter mais informações sobre o Gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o IP público do Azure está na região do Azure da qual você deseja mover.

- Os IPs públicos do Azure não podem ser movidos entre regiões.  Você precisará associar o novo IP público aos recursos na região de destino.

- Para exportar uma configuração de IP público e implantar um modelo para criar um IP público em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie IPs públicos na região de destino usada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de IPs públicos para esse processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o IP público para a configuração mover usando um modelo do Resource Manager e mover a configuração de IP público para a região de destino usando Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar a partir de um script

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha a ID de recurso do IP público que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um arquivo. JSON no diretório em que você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando chamado  **\<Resource-Group-Name >. JSON** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do IP público, altere a propriedade **DefaultValue** do nome do IP público de origem para o nome do seu IP público de destino, verifique se o nome está entre aspas:
    
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

6. Para editar a região de destino em que o IP público será movido, altere a propriedade **local** em recursos:

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
  
7. Para obter códigos de localização de região, você pode usar o cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Você também pode alterar outros parâmetros no modelo se escolher e forem opcionais, dependendo dos seus requisitos:

    * **SKU** -você pode alterar a SKU do IP público na configuração de Standard para básico ou básico para Standard alterando a propriedade de**nome** do **SKU** > no arquivo de  **\<nome do grupo de recursos >. JSON** :

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

         Para obter mais informações sobre as diferenças entre IPS públicos do SKU básico e Standard, consulte [criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método de alocação de IP público** e **tempo limite de ociosidade** -você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **dinâmico** para **estático** ou **estático** para **dinâmico** . O tempo limite de ociosidade pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para o valor desejado.  O padrão é **4**:

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

        Para obter mais informações sobre os métodos de alocação e os valores de tempo limite de ociosidade, consulte [criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Salve o  **\<arquivo-Group-Name >. JSON** .

10. Crie um grupo de recursos na região de destino para o IP público de destino a ser implantado usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implante o arquivo editado  **\<-Group-Name >. JSON** no grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Dispensar 

Após a implantação, se você quiser iniciar ou descartar o IP público no destino, exclua o grupo de recursos que foi criado no destino e o IP público movido será excluído.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você moveu um IP público do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
