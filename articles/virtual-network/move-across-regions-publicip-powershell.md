---
title: Move Azure Public IP para outra região de Azure usando azure PowerShell
description: Utilize o modelo do Gestor de Recursos Azure para mover o IP público azure de uma região azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 76924705ff801ce3be6a5c76f7ae276bdbf93def
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147883"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Move Azure Public IP para outra região usando azure PowerShell

Há vários cenários em que gostaria de mover os seus IPs públicos azure existentes de uma região para outra. Por exemplo, pode querer criar um IP público com a mesma configuração e sku para testes. Também pode querer mover um IP público para outra região como parte do planeamento de recuperação de desastres.

Os IPs públicos de Azure são específicos da região e não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente de um IP público.  Em seguida, pode encenar o recurso noutra região exportando o IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o IP público de Azure está na região de Azure, de onde pretende mover-se.

- Os IPs públicos de Azure não podem ser movidos entre regiões.  Terá de associar o novo IP público aos recursos na região alvo.

- Para exportar uma configuração ip pública e implementar um modelo para criar um IP público em outra região, você precisará do papel de Colaborador de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se a sua subscrição Azure permite criar IPs públicos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para apoiar a adição de IPs públicos para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o IP público para o movimento de configuração usando um modelo de Gestor de Recursos, e mover a configuração IP pública para a região alvo usando o Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implementar a partir de um script

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do IP público que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
## <a name="discard"></a>Eliminar 

Após a implantação, se desejar recomeçar ou descartar o IP público no alvo, elimine o grupo de recursos que foi criado no alvo e o IP público deslocado será eliminado.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para efazer as alterações e completar o movimento da rede virtual, elimine a rede virtual de origem ou o grupo de recursos, utilize [o Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [o Remove-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um IP Público Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
