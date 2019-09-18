---
title: Mover o NSG (grupo de segurança de rede) do Azure para outra região do Azure usando o portal do Azure
description: Use Azure Resource Manager modelo para mover o grupo de segurança de rede do Azure de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0dd460f7ed829bf82c285b80e59778dacd882404
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059323"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Mover o NSG (grupo de segurança de rede) do Azure para outra região usando o portal do Azure

Há vários cenários em que você deseja mover seu NSGs existente de uma região para outra. Por exemplo, talvez você queira criar um NSG com as mesmas regras de configuração e segurança para teste. Você também pode querer mover um NSG para outra região como parte do planejamento de recuperação de desastre.

Os grupos de segurança do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar as regras de segurança e configuração existentes de um NSG.  Em seguida, você pode preparar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo na nova região.  Para obter mais informações sobre o Resource Manager e modelos [, consulte início rápido: Crie e implante modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o grupo de segurança de rede do Azure está na região do Azure da qual você deseja mover.

- Os grupos de segurança de rede do Azure não podem ser movidos entre regiões.  Você precisará associar o novo NSG aos recursos na região de destino.

- Para exportar uma configuração do NSG e implantar um modelo para criar um NSG em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie NSGs na região de destino que é usada. Contacte o suporte para ativar a quota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de NSGs para esse processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o grupo de segurança de rede para a configuração e a regra de segurança mover usando um modelo do Resource Manager e mover as regras de configuração e segurança do NSG para a região de destino usando o Portal.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportar o modelo e implantar por meio do portal

1. Faça logon no [portal do Azure](http://portal.azure.com) > **grupos de recursos**.
2. Localize o grupo de recursos que contém o NSG de origem e clique nele.
3. Selecione **configurações** > de >**modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo** > **Editar parâmetros** para abrir o arquivo **Parameters. JSON** no editor online.
6. Para editar o parâmetro do nome NSG, altere a propriedade **Value** em **parâmetros**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Altere o valor NSG de origem no editor para um nome de sua escolha para o NSG de destino. Certifique-se de colocar o nome entre aspas.

8.  Clique em **salvar** no editor.

9.  Clique em **modelo** > **Editar modelo** para abrir o arquivo **Template. JSON** no editor online.

10. Para editar a região de destino em que as regras de configuração e segurança do NSG serão movidas, altere a propriedade **local** em **recursos** no editor online:

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
           ]

    ```
  
11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.
    
12. Você também pode alterar outros parâmetros no modelo se escolher e forem opcionais, dependendo dos seus requisitos:

    * **Regras de segurança** – você pode editar quais regras são implantadas no NSG de destino adicionando ou removendo regras para a seção **securityRules** no arquivo **Template. JSON** :
    
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
                    },
                ]
            }
        ```

      Para concluir a adição ou a remoção das regras no NSG de destino, você também deve editar os tipos de regra personalizada no final do arquivo **Template. JSON** no formato do exemplo abaixo:

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

13. Clique em **salvar** no editor online.

14. Clique em**assinatura** **básica** > para escolher a assinatura na qual o NSG de destino será implantado.

15. Clique em**grupo de recursos** **básico** > para escolher o grupo de recursos no qual o NSG de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o NSG de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do NSG existente. 

16. Verifique se o**local** **básico** > está definido como o local de destino onde você deseja que o NSG seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar o grupo de segurança de rede de destino.

## <a name="discard"></a>Dispensar 

Se você quiser descartar o NSG de destino, exclua o grupo de recursos que contém o NSG de destino.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos. Para fazer isso, selecione o grupo de segurança de rede ou grupo de recursos no painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu um grupo de segurança de rede do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
