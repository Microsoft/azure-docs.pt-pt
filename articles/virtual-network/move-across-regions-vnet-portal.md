---
title: Mova a rede virtual do Azure para outra região do Azure usando o portal do Azure.
description: Use Azure Resource Manager modelo para mover a rede virtual do Azure de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 2a1ee358a6c97b721ec6f0da3eb70269239b0737
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077675"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Mover a rede virtual do Azure para outra região usando o portal do Azure

Há vários cenários em que você deseja mover suas VNETs (redes virtuais) do Azure existentes de uma região para outra. Por exemplo, talvez você queira criar uma rede virtual com a mesma configuração para teste e disponibilidade de sua rede virtual existente. Você também pode querer mover uma rede virtual de produção para outra região como parte do planejamento de recuperação de desastre.

Você pode usar um modelo de Azure Resource Manager para concluir a movimentação da rede virtual para outra região. Você faz isso exportando a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região.  Para obter mais informações sobre o Gerenciador de recursos e modelos [, consulte início rápido: Criar e implantar modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se a rede virtual do Azure está na região do Azure da qual você deseja mover.

- Para exportar uma rede virtual e implantar um modelo para criar uma rede virtual em outra região, você precisará da função de colaborador de rede ou superior.

- Os emparelhamentos de rede virtual não serão recriados e ocorrerão falhas se ainda estiverem presentes no modelo.  Você precisará remover os pares de rede virtual antes de exportar o modelo e, em seguida, restabelecer os pares após a movimentação da rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se sua assinatura do Azure permite que você crie redes virtuais na região de destino usada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de redes virtuais para esse processo.  Confira [assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar a rede virtual para a movimentação usando um modelo do Resource Manager e mover a rede virtual para a região de destino usando o Portal.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportar o modelo e implantar por meio do portal

1. Faça logon no [portal do Azure](http://portal.azure.com) > **grupos de recursos**.
2. Localize o grupo de recursos que contém a rede virtual de origem e clique nele.
3. Selecione **configurações** > de >**modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo** > **Editar parâmetros** para abrir o arquivo **Parameters. JSON** no editor online.
6. Para editar o parâmetro do nome da rede virtual, altere a propriedade **Value** em **parâmetros**:
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Altere o valor de nome de rede virtual de origem no editor para um nome de sua escolha para a VNET de destino. Certifique-se de colocar o nome entre aspas.

8.  Clique em **salvar** no editor.

9.  Clique em **modelo** > **Editar modelo** para abrir o arquivo **Template. JSON** no editor online. 

10. Para editar a região de destino em que a VNET será movida, altere a propriedade **local** em **recursos** no editor online:

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
 
11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.
 
12. Você também pode alterar outros parâmetros no modelo se escolher e forem opcionais, dependendo dos seus requisitos:
    
    * **Espaço de endereço** -o espaço de endereço da VNET pode ser alterado antes de salvar modificando a seção **recursos** > de**addressSpace** e alterando a propriedade **addressPrefixes** no arquivo **Template. JSON** :

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

    * **Sub** -rede-o nome da sub-rede e o espaço de endereço de sub-rede podem ser alterados ou adicionados ao modificando a seção **sub-redes** do arquivo **Template. JSON** . O nome da sub-rede pode ser alterado alterando a propriedade **Name** . O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **addressPrefix** no arquivo **Template. JSON** :

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

         No arquivo **Template. JSON** , para alterar o prefixo do endereço, ele deve ser editado em dois locais, na seção listada acima e na seção **tipo** listada abaixo.  Altere a propriedade **addressPrefix** para corresponder à que está acima:

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

13. Clique em **salvar** no editor online.

14. Clique em**assinatura** **básica** > para escolher a assinatura na qual a VNET de destino será implantada.

15. Clique em**grupo de recursos** **básico** > para escolher o grupo de recursos em que a VNET de destino será implantada.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para a VNET de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem da VNET existente. 

16. Verifique se o**local** **básico** > está definido como o local de destino onde você deseja que a VNET seja implantada.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar a rede virtual de destino.

## <a name="discard"></a>Dispensar 

Se você quiser descartar a rede virtual de destino, exclua o grupo de recursos que contém a rede virtual de destino.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos. Para fazer isso, selecione a rede virtual ou o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma rede virtual do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
