---
title: App gerida com Identidade Gerida
description: Configure a Aplicação Gerida com Identidade Gerida para ligação aos recursos existentes, gestão de recursos Azure e disponibilização de identidade operacional para Registo de Atividades.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82508139"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicação gerida azure com identidade gerida

> [!NOTE]
> O suporte de identidade gerido para aplicações geridas está atualmente em pré-visualização. Utilize a versão api de pré-visualização 2018-09-01 para utilizar a Identidade Gerida.

Saiba como configurar uma Aplicação Gerida para conter uma Identidade Gerida. A Identidade Gerida pode ser utilizada para permitir ao cliente conceder ao Cliente acesso à Aplicação Gerida a recursos adicionais existentes. A identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos. Para obter mais sobre identidades geridas no Azure Ative Directory (AAD), consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

O seu pedido pode ser concedido dois tipos de identidades:

- Uma **identidade atribuída ao sistema** está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso autónomo da Azure que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.

## <a name="how-to-use-managed-identity"></a>Como utilizar a Identidade Gerida

A Identidade Gerida permite muitos cenários para Aplicações Geridas. Alguns cenários comuns que podem ser resolvidos são:

- Implementação de uma Aplicação Gerida ligada aos recursos Azure existentes. Um exemplo é a implementação de uma máquina virtual Azure (VM) dentro da Aplicação Gerida que está ligada a uma [interface de rede existente.](../../virtual-network/virtual-network-network-interface-vm.md)
- Conceder à Aplicação Gerida e ao acesso dos editores aos recursos da Azure fora do **grupo de recursos geridos.**
- Fornecendo uma identidade operacional de Aplicações Geridas para Registo de Atividades e outros serviços dentro do Azure.

## <a name="adding-managed-identity"></a>Adicionar identidade gerida

A criação de uma Aplicação Gerida com uma Identidade Gerida requer uma propriedade adicional a ser definida no recurso Azure. O exemplo a seguir mostra uma propriedade **de identidade** de amostra:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existem duas formas comuns de criar uma Aplicação Gerida com **identidade:** [CreateUIDefinition.jse](./create-uidefinition-overview.md) [modelos de Gestor de Recursos Azure](../templates/template-syntax.md). Para cenários simples de criação individual, a CreateUIDefinition deve ser usada para permitir a Identidade Gerida, pois proporciona uma experiência mais rica. No entanto, ao lidar com sistemas avançados ou complexos que requerem implementações automatizadas ou múltiplas de aplicações geridas, os modelos podem ser utilizados.

### <a name="using-createuidefinition"></a>Utilização de CreateUIDefinition

Uma Aplicação Gerida pode ser configurada com Identidade Gerida através do [CreateUIDefinition.jsem](./create-uidefinition-overview.md). Na [secção de saídas,](./create-uidefinition-overview.md#outputs)a chave `managedIdentity` pode ser usada para anular a propriedade identitária do modelo de Aplicação Gerida. O fole de amostra permitirá a identidade **atribuída ao sistema** na Aplicação Gerida. Objetos de identidade mais complexos podem ser formados usando elementos CreateUIDefinition para pedir ao consumidor entradas. Estas entradas podem ser utilizadas para construir Aplicações Geridas com **identidade atribuída ao utilizador.**

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando utilizar a CriaUIDefinition para Identidade Gerida

Abaixo estão algumas recomendações sobre quando usar o CreateUIDefinition para permitir a identidade gerida em aplicações geridas.

- A criação de Aplicações Geridas passa pelo portal ou mercado Azure.
- A Identidade Gerida requer uma entrada complexa do consumidor.
- A Identidade Gerida é necessária na criação da Aplicação Gerida.

#### <a name="managed-identity-createuidefinition-control"></a>Controlo de identidade gerida CreateUIDefinition

A CreateUIDefinition suporta um controlo de [identidade gerido](./microsoft-managedidentity-identityselector.md)incorporado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![Identidade gerida CriaUIDefinição](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

> [!NOTE]
> Os modelos de Aplicação Geridos no Mercado são gerados automaticamente para os clientes que passam pelo portal Azure criar experiência.
> Para estes cenários, a `managedIdentity` chave de saída da CreateUIDefinition deve ser utilizada para a identidade ativada.

A Identidade Gerida também pode ser ativada através de modelos do Gestor de Recursos Azure. O fole de amostra permitirá a identidade **atribuída ao sistema** na Aplicação Gerida. Objetos de identidade mais complexos podem ser formados usando parâmetros do modelo do Gestor de Recursos Azure para fornecer entradas. Estas entradas podem ser utilizadas para construir Aplicações Geridas com **identidade atribuída ao utilizador.**

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando utilizar modelos de Gestor de Recursos Azure para identidade gerida

Abaixo estão algumas recomendações sobre quando usar modelos do Gestor de Recursos Azure para permitir a identidade gerida em aplicações geridas.

- As Aplicações Geridas podem ser implantadas programáticamente com base num modelo.
- As atribuições de funções personalizadas para a Identidade Gerida são necessárias para fornecer a Aplicação Gerida.
- A Aplicação Gerida não necessita do portal Azure e do fluxo de criação de mercado.

#### <a name="systemassigned-template"></a>Modelo de assinatura do sistema

Um modelo básico de Gestor de Recursos Azure que implementa uma Aplicação Gerida com identidade **atribuída ao sistema.**

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Modelo de assinatura do utilizador

Um modelo básico de Gestor de Recursos Azure que implementa uma Aplicação Gerida com uma **identidade atribuída ao utilizador.**

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Concessão de acesso aos recursos da Azure

Uma vez que um Pedido Gerido é concedido uma identidade, pode ser concedido acesso aos recursos Azure existentes. Este processo pode ser feito através da interface Access control (IAM) no portal Azure. O nome da Aplicação Gerida ou **identidade atribuída ao utilizador** pode ser pesquisado para adicionar uma atribuição de funções.

![Adicionar atribuição de função para aplicação gerida](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Ligação dos recursos Azure existentes

> [!NOTE]
> Uma **identidade atribuída ao utilizador** deve ser [configurada](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes de implementar a Aplicação Gerida. Além disso, a implementação de recursos ligados de Aplicações Geridas só é suportada para o tipo **de mercado.**

A Identidade Gerida também pode ser usada para implementar uma Aplicação Gerida que requer acesso aos recursos existentes durante a sua implantação. Quando a Aplicação Gerida é a forneciada pelo cliente, **as identidades atribuídas ao utilizador** podem ser adicionadas para fornecer autorizações adicionais à implantação principal **doTemplate.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Autoria da Criação deDefinição com um recurso ligado

Ao associar a implementação da Aplicação Gerida aos recursos existentes, deve ser fornecido tanto o recurso Azure existente como uma **identidade atribuída ao utilizador** com a atribuição de funções aplicável nesse recurso.

 Uma amostra CreateUIDefinition que requer duas entradas: um ID de recurso de interface de rede e um ID de recursos de identidade atribuídos pelo utilizador.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Esta CreateUIDefinition.jsgera uma experiência de utilizador criar que tem dois campos. O primeiro campo permite ao utilizador introduzir no ID de recurso Azure para o recurso que está ligado à implementação da Aplicação Gerida. A segunda é que um consumidor introduza o ID de recurso Azure **atribuído ao utilizador,** que tem acesso ao recurso Azure ligado. A experiência gerada seria como:

![Amostra CreateUIDefinition com duas entradas: um ID de recurso de interface de rede e um ID de recursos de identidade atribuídos pelo utilizador](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Autoria do mainTemplate com um recurso ligado

Além de atualizar a CriaUIDefinition, o modelo principal também precisa de ser atualizado para aceitar o passado no ID de recursos ligados. O modelo principal pode ser atualizado para aceitar a nova saída adicionando um novo parâmetro. Uma vez que a `managedIdentity` saída sobrepõe o valor no modelo de aplicação gerida gerada, não é passado para o modelo principal e não deve ser incluído na secção de parâmetros.

Um modelo principal de amostra que define o perfil de rede para uma interface de rede existente fornecida pela CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumir a Aplicação Gerida com um recurso ligado

Uma vez criado o pacote Aplicação Gerida, a Aplicação Gerida pode ser consumida através do portal Azure. Antes de poder ser consumido, existem vários passos pré-requisitos.

- Deve ser criado um caso do recurso Azure associado necessário.
- A **identidade atribuída ao utilizador** deve ser criada e atribuída uma [função](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ao recurso ligado.
- O ID de recursos ligados existente e o ID **de identidade atribuído** ao utilizador são fornecidos à CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Aceder ao token de identidade gerida

O símbolo da Aplicação Gerida pode agora ser acedido através da `listTokens` API do arrendatário editor. Um pedido de exemplo pode parecer:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Solicitar parâmetros corporais:

Parâmetro | Obrigatório | Descrição
---|---|---
autorizaçãoAudiência | *Não* | O ID URI da aplicação do recurso-alvo. É também a `aud` reivindicação (do público) do símbolo emitido. O valor padrão é https://management.azure.com/ "
nomeações para assediadas do utilizador | *Não* | A lista de identidades geridas atribuídas pelo utilizador para recuperar um símbolo para. Se não for especificado, `listTokens` devolverá o símbolo para a identidade gerida atribuída pelo sistema.


Uma resposta de amostra pode parecer:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

A resposta conterá uma variedade de fichas sob a `value` propriedade:

Parâmetro | Descrição
---|---
access_token | O sinal de acesso solicitado.
expires_in | O número de segundos de acesso será válido.
expires_on | O tempo de tempo quando a ficha de acesso expira. Isto é representado como o número de segundos da época.
not_before | O tempo em que o token de acesso entra em vigor. Isto é representado como o número de segundos da época.
autorizaçãoAudiência | O `aud` (público) o símbolo de acesso foi solicitado. Isto é o mesmo que foi fornecido no `listTokens` pedido.
resourceId | O ID de recurso Azure para o token emitido. Este é o ID de aplicação gerido ou o ID de identidade atribuído pelo utilizador.
token_type | O tipo de símbolo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como configurar uma Aplicação Gerida com um Fornecedor Personalizado](../custom-providers/overview.md)
