---
title: Mover uma rede virtual Azure para outra região do Azure utilizando o portal Azure.
description: Mover uma rede virtual Azure de uma região de Azure para outra utilizando um modelo de Gestor de Recursos e o portal Azure.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223313"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Mover uma rede virtual Azure para outra região utilizando o portal Azure

Existem vários cenários para mover uma rede virtual Azure existente de uma região para outra. Por exemplo, é possível que pretenda criar uma rede virtual com a mesma configuração para testes e disponibilidade que a sua rede virtual existente. Ou talvez queira mover uma rede virtual de produção para outra região como parte do seu plano de recuperação de desastres.

Pode utilizar um modelo de Gestor de Recursos Azure para completar a mudança da rede virtual para outra região. Fá-lo exportando a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre os modelos do Gestor de Recursos, consulte [Quickstart: Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a sua rede virtual está na região de Azure de onde pretende mover-se.

- Para exportar uma rede virtual e implementar um modelo para criar uma rede virtual noutra região, é necessário ter o papel de Contribuinte de Rede ou superior.

- Os olhos de rede virtuais não serão recriados, e falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, tem de remover quaisquer pares de rede virtuais. Em seguida, pode restaurá-los após o movimento de rede virtual.

- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se a sua subscrição Azure permite criar redes virtuais na região alvo. Para ativar a quota necessária, contacte o suporte.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de redes virtuais para este processo. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Preparem-se para a mudança
Nesta secção, prepara-se a rede virtual para o movimento utilizando um modelo de Gestor de Recursos. Em seguida, move-se a rede virtual para a região alvo utilizando o portal Azure.

Para exportar a rede virtual e implementar a rede virtual alvo utilizando o portal Azure, faça o seguinte:

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, selecione **Grupos de Recursos**.
1. Localize o grupo de recursos que contém a rede virtual de origem e, em seguida, selecione-o.
1. Selecione   >  **o modelo de exportação de** definições.
1. No painel **de modelo de exportação,** selecione **Implementar**.
1. Para abrir o *parameters.jsno* ficheiro no seu editor online, selecione os   >  **parâmetros de Edição** de Modelos .
1. Para editar o parâmetro do nome de rede virtual, altere a propriedade de **valor** em **parâmetros:**

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

1. No editor, altere o valor do nome da rede virtual de origem no editor para um nome que deseja para a rede virtual alvo. Certifique-se de que encerra o nome em aspas.

1. **Selecione Save** no editor.

1. Para abrir o *template.jsno* ficheiro no editor online, selecione o modelo de edição **de**  >  **modelos**.

1. No editor online, para editar a região alvo onde a rede virtual será movida, altere a propriedade de **localização** em **recursos**:

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

1. Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região, sem espaços (por exemplo, **central dos EUA central).**  =  

1. (Opcional) Também pode alterar outros parâmetros no modelo, dependendo dos seus requisitos:

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

    * **Sub-rede**: Pode alterar ou adicionar ao nome da sub-rede e ao espaço do endereço da sub-rede alterando a secção **de sub-redes** do modelo. Pode alterar o nome da sub-rede alterando a propriedade **do nome.** E pode alterar o espaço de endereço da sub-rede alterando a propriedade **addressPrefix:**

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

        Para alterar o prefixo de endereço no *template.jsficheiro,* edite-o em dois locais: no código na secção anterior e na secção **tipo** do seguinte código. Alterar o **endereçoPrefixo** no seguinte código para corresponder à propriedade **addressPrefix** no código na secção anterior.

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

1. No editor online, **selecione Save**.

1. Para escolher a subscrição onde será implantada a rede virtual alvo, selecione  >  **Subscrição** básica .

1. Para escolher o grupo de recursos onde será implantada a rede virtual alvo, selecione **o** grupo  >  **Basics Resource**. 

    Se precisar de criar um novo grupo de recursos para a rede virtual alvo, selecione **Criar novo**. Certifique-se de que o nome não é o mesmo que o nome do grupo de recursos de origem na rede virtual existente.

1. Verifique se **a**  >  **Localização** Básica está definida para o local alvo onde deseja que a rede virtual seja implantada.

1. Em **Definições**, verifique se o nome corresponde ao nome que inseriu anteriormente no editor de parâmetros.

1. Selecione a caixa de verificação **de Termos e Condições.**

1. Para implementar a rede virtual alvo, selecione **Comprar**.

## <a name="delete-the-target-virtual-network"></a>Eliminar a rede virtual alvo

Para descartar a rede virtual alvo, elimina o grupo de recursos que contém a rede virtual alvo. Para tal:
1. No painel do portal Azure, selecione o grupo de recursos.
1. No topo do painel de **visão** geral, selecione **Delete**.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar o movimento de rede virtual, elimina a rede virtual de origem ou o grupo de recursos. Para tal:
1. No painel de instrumentos do portal Azure, selecione a rede virtual ou o grupo de recursos.
1. Na parte superior de cada painel, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você mudou uma rede virtual Azure de uma região para outra usando o portal Azure e, em seguida, limpou os recursos de origem não necessários. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover máquinas virtuais Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)