---
title: Move Azure network security group (NSG) para outra região do Azure usando azure PowerShell
description: Utilize o modelo do Gestor de Recursos Azure para mover o grupo de segurança da rede Azure de uma região do Azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641473"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Move Azure network security group (NSG) para outra região usando Azure PowerShell

Há vários cenários em que você gostaria de mover os seus NSGs existentes de uma região para outra. Por exemplo, pode querer criar um NSG com as mesmas regras de configuração e segurança para testes. Você também pode querer mover um NSG para outra região como parte do planeamento de recuperação de desastres.

Os grupos de segurança azure não podem ser transferidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar as regras de configuração e segurança existentes de um NSG.  Em seguida, pode encenar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o grupo de segurança da rede Azure está na região de Azure, de onde pretende mover-se.

- Os grupos de segurança da rede Azure não podem ser deslocados entre regiões.  Terá de associar o novo NSG aos recursos na região alvo.

- Para exportar uma configuração DE NSG e implementar um modelo para criar um NSG em outra região, você precisará do papel de Colaborador de Rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, IPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar NSGs na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de NSGs para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o grupo de segurança da rede para o movimento de configuração e regra de segurança usando um modelo de Gestor de Recursos, e mover as regras de configuração e segurança do NSG para a região alvo usando o Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implementar a partir de um script

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do NSG que pretende mover-se para a região alvo e colocá-lo numa variável utilizando [o Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a fonte NSG para um ficheiro .json no diretório onde executa o comando [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. O ficheiro descarregado será nomeado após o grupo de recursos de onde o recurso foi exportado.  Localize o ficheiro que foi exportado do comando chamado ** \<recurso-nome de grupo de recursos>.json e abra-o** num editor à sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome NSG, altere o padrão de **propriedadeValor** do nome NSG de origem para o nome do seu alvo NSG, certifique-se de que o nome está em aspas:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Para editar a região alvo onde serão movidas as regras de configuração e segurança do NSG, altere a propriedade de **localização** sob **recursos:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Para obter códigos de localização da região, pode utilizar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Também pode alterar outros parâmetros no ** \<nome de grupo de recursos>.json,** se quiser, e é opcional dependendo dos seus requisitos:

    * **Regras de segurança** - Pode editar quais as regras que são implementadas no NSG alvo adicionando ou removendo regras à secção de regras de **segurançaNo** ** \<ficheiro>.json** de nome de grupo de recursos:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Para completar a adição ou a remoção das regras no NSG-alvo, deve também editar os tipos de regras personalizados no final do ficheiro ** \<>.json** de nome de grupo de recursos no formato do exemplo abaixo:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Guarde o ** \<ficheiro>.json de nome de grupo de recursos.**

10. Criar um grupo de recursos na região-alvo para que o NSG alvo seja implantado utilizando o [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implemente o ficheiro ** \<>.json** de nome de grupo de recursos editado para o grupo de recursos criado na fase anterior utilizando a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região alvo, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [o Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Eliminar 

Após a implementação, se desejar recomeçar ou descartar o NSG no alvo, elimine o grupo de recursos que foi criado no alvo e o NSG deslocado será eliminado.  Para remover o grupo de recursos, utilize [o Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpeza

Para efazer as alterações e completar o movimento do NSG, elimine a fonte NSG ou o grupo de recursos, utilize [remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um grupo de segurança da rede Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
