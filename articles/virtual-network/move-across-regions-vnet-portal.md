---
title: Mova uma rede virtual Azure para outra região do Azure utilizando o portal Azure.
description: Mova uma rede virtual Azure de uma região azure para outra utilizando um modelo de Gestor de Recursos e o portal Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640793"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Mover uma rede virtual Azure para outra região utilizando o portal Azure

Existem vários cenários para mover uma rede virtual Azure existente de uma região para outra. Por exemplo, pode querer criar uma rede virtual com a mesma configuração para testes e disponibilidade que a sua rede virtual existente. Ou talvez queira mover uma rede virtual de produção para outra região como parte do seu planeamento de recuperação de desastres.

Você pode usar um modelo de Gestor de Recursos Azure para completar a mudança da rede virtual para outra região. Faz-o exportando a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre os modelos do Gestor de Recursos, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure .


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a sua rede virtual está na região de Azure da qual pretende mudar-se.

- Para exportar uma rede virtual e implementar um modelo para criar uma rede virtual noutra região, é necessário ter o papel de Colaborador de Rede ou superior.

- Os pares de rede virtuais não serão recriados, e falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, tem de remover quaisquer pares de rede virtual. Em seguida, pode restabelece-los após o movimento da rede virtual.

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se a sua subscrição Azure permite criar redes virtuais na região alvo. Para ativar a quota necessária, contacte o suporte.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para apoiar a adição de redes virtuais para este processo. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Prepare-se para a mudança
Nesta secção, prepara a rede virtual para o movimento utilizando um modelo de Gestor de Recursos. Em seguida, move a rede virtual para a região alvo utilizando o portal Azure.

Para exportar a rede virtual e implantar a rede virtual alvo utilizando o portal Azure, faça o seguinte:

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, selecione **Grupos de Recursos**.
1. Localize o grupo de recursos que contém a rede virtual de origem e, em seguida, selecione-o.
1. Selecione modelo**de exportação** **de configurações** > .
1. No painel do **modelo de exportação,** selecione **Deploy**.
1. Para abrir o ficheiro *parâmetros.json* no seu editor online, selecione parâmetros de**Edição**de **Modelos** > .
1. Para editar o parâmetro do nome da rede virtual, altere a propriedade de **valor** em **parâmetros:**

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

1. No editor, altere o valor de nome de rede virtual de origem no editor para um nome que deseja para a rede virtual alvo. Certifique-se de que encerra o nome em aspas.

1. Selecione **Guardar** no editor.

1. Para abrir o ficheiro *template.json* no editor on-line, selecione modelo de**Edição**de **Modelo** > .

1. No editor online, para editar a região alvo onde a rede virtual será deslocada, alterar a propriedade de **localização** sob **recursos:**

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

1. Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/). O código para uma região é o nome da região, sem espaços (por exemplo,**central** **dos EUA** = central).

1. (Opcional) Também pode alterar outros parâmetros no modelo, dependendo dos seus requisitos:

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

    * **Sub-rede**: Pode alterar ou adicionar ao nome da sub-rede e ao espaço de endereço da sub-rede alterando a secção de **sub-redes** do modelo. Pode alterar o nome da subnet alterando a propriedade do **nome.** E pode alterar o espaço de endereço da subnet alterando a propriedade **addressPrefix:**

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

        Para alterar o prefixo de endereço no ficheiro *template.json,* edite-o em dois locais: no código na secção anterior e na secção do **tipo** do seguinte código. Altere a propriedade **de endereçoPrefix** no seguinte código para combinar com a propriedade **addressPrefix** no código na secção anterior.

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

1. No editor online, selecione **Save**.

1. Para escolher a subscrição onde a rede virtual alvo será implementada, selecione**Subscrição** **Basics** > .

1. Para escolher o grupo de recursos onde a rede virtual alvo será implantada, selecione **Basics** > **Resource group**. 

    Se precisar de criar um novo grupo de recursos para a rede virtual alvo, selecione **Criar novos**. Certifique-se de que o nome não é o mesmo que o nome do grupo de recursos de origem na rede virtual existente.

1. Verifique se a**Localização** **Básica** > está definida para o local alvo onde pretende que a rede virtual seja implantada.

1. Em **Definições,** verifique se o nome corresponde ao nome que inseriu anteriormente no editor de parâmetros.

1. Selecione a caixa de verificação **De Condições e Condições.**

1. Para implementar a rede virtual alvo, **selecione Comprar**.

## <a name="delete-the-target-virtual-network"></a>Eliminar a rede virtual alvo

Para descartar a rede virtual alvo, elimina o grupo de recursos que contém a rede virtual alvo. Para tal:
1. No painel do portal Azure, selecione o grupo de recursos.
1. Na parte superior do painel **de visão geral,** selecione **Delete**.

## <a name="clean-up"></a>Limpeza

Para efetuar as alterações e completar o movimento de rede virtual, elimina a rede virtual de origem ou grupo de recursos. Para tal:
1. No painel do portal Azure, selecione a rede virtual ou o grupo de recursos.
1. Na parte superior de cada painel, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou uma rede virtual Azure de uma região para outra usando o portal Azure e depois limpou os recursos de origem desnecessários. Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mova as máquinas virtuais Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
