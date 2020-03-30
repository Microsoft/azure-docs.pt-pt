---
title: App gerida com Identidade Gerida
description: Configure Aplicação Gerida com Identidade Gerida para a ligação aos recursos existentes, gestão de recursos Azure e fornecimento de identidade operacional para Registo de Atividades.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651660"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicação gerida azure com identidade gerida

> [!NOTE]
> O suporte de identidade gerido para aplicações geridas encontra-se atualmente em pré-visualização. Utilize a versão api 2018-09-01 para utilizar a Identidade Gerida.

Aprenda a configurar uma Aplicação Gerida para conter uma Identidade Gerida. A Identidade Gerida pode ser usada para permitir ao cliente conceder à Aplicação Gerida o acesso a recursos adicionais existentes. A identidade é gerida pela plataforma Azure e não o obriga a fornecer ou rodar quaisquer segredos. Para mais informações sobre identidades geridas no Azure Ative Directory (AAD), consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

A sua aplicação pode ser concedida a dois tipos de identidades:

- Uma **identidade atribuída** ao sistema está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso Azure autónomo que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.

## <a name="how-to-use-managed-identity"></a>Como usar a Identidade Gerida

A Identidade Gerida permite muitos cenários para Aplicações Geridas. Alguns cenários comuns que podem ser resolvidos são:

- Implementação de uma Aplicação Gerida ligada aos recursos existentes do Azure. Um exemplo é a implementação de uma máquina virtual Azure (VM) dentro da Aplicação Gerida que está ligada a uma interface de [rede existente](../../virtual-network/virtual-network-network-interface-vm.md).
- Concessão da Aplicação Gerida e do acesso da editora aos recursos Azure fora do **grupo de recursos geridos.**
- Disponibilizando uma identidade operacional de Aplicações Geridas para Registo de Atividades e outros serviços dentro do Azure.

## <a name="adding-managed-identity"></a>Adicionar identidade gerida

A criação de uma Aplicação Gerida com uma Identidade Gerida requer um imóvel adicional a ser definido no recurso Azure. O exemplo seguinte mostra uma propriedade **de identidade** de amostra:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existem duas formas comuns de criar uma Aplicação Gerida com **identidade**: [CreateUIDefinition.json](./create-uidefinition-overview.md) e [modelos de Gestor de Recursos Azure](../templates/template-syntax.md). Para cenários simples de criação simples, a CreateUIDefinition deve ser usada para ativar a Identidade Gerida, porque proporciona uma experiência mais rica. No entanto, ao lidar com sistemas avançados ou complexos que requerem implementações automatizadas ou múltiplas de Aplicação Gerida, os modelos podem ser usados.

### <a name="using-createuidefinition"></a>Usando a CreateuiDefinition

Uma Aplicação Gerida pode ser configurada com identidade gerida através do [CreateUIDefinition.json](./create-uidefinition-overview.md). Na [secção de saídas,](./create-uidefinition-overview.md#outputs)a chave `managedIdentity` pode ser usada para anular a propriedade de identidade do modelo de Aplicação Gerida. O fole da amostra permitirá a identidade atribuída ao **sistema** na Aplicação Gerida. Objetos de identidade mais complexos podem ser formados utilizando elementos CreateUIDefinition para pedir ao consumidor inputs. Estas inputs podem ser utilizadas para construir Aplicações Geridas com **identidade atribuída**ao utilizador .

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando utilizar a CreateUIDefinition para identidade gerida

Abaixo estão algumas recomendações sobre quando usar CreateUIDefinition para permitir identidade gerida em aplicações geridas.

- A criação de Aplicação Gerida passa pelo portal ou mercado do Azure.
- A Identidade Gerida requer uma entrada complexa do consumidor.
- A Identidade Gerida é necessária na criação da Aplicação Gerida.

#### <a name="systemassigned-createuidefinition"></a>SistemaAstada Definição DeUI

Uma Definição Basic CreateUI que permite a identidade Do Sistema Astada para a Aplicação Gerida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Uma Definição Basic CreateUIQue que toma como entrada um recurso de **identidade atribuído** ao utilizador e permite a identidade UserAssigned para a Aplicação Gerida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

O CreateUIDefinition.json acima gera uma experiência de criação de utilizador que tem uma caixa de texto para um consumidor introduzir o ID de recurso azure de identidade atribuído ao **utilizador.** A experiência gerada seria como:

![Amostra de identidade atribuída ao utilizador CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

> [!NOTE]
> Os modelos de aplicação gerida sumivelmente são gerados automaticamente para os clientes que passam pelo portal Azure criar experiência.
> Para estes cenários, a `managedIdentity` chave de saída da CreateUIDefinition deve ser utilizada para a identidade ativada.

A Identidade Gerida também pode ser ativada através de modelos de Gestor de Recursos Azure. O fole da amostra permitirá a identidade atribuída ao **sistema** na Aplicação Gerida. Objetos de identidade mais complexos podem ser formados usando parâmetros de modelo do Gestor de Recursos Azure para fornecer inputs. Estas inputs podem ser utilizadas para construir Aplicações Geridas com **identidade atribuída**ao utilizador .

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usar modelos de Gestor de Recursos Azure para identidade gerida

Abaixo estão algumas recomendações sobre quando usar modelos de Gestor de Recursos Azure para permitir identidade gerida em aplicações geridas.

- As Aplicações Geridas podem ser implantadas programáticamente com base num modelo.
- São necessárias atribuições de funções personalizadas para a Identidade Gerida para fornecer a Aplicação Gerida.
- A Aplicação Gerida não necessita do portal Azure e do fluxo de criação de mercado.

#### <a name="systemassigned-template"></a>Modelo atribuído pelo sistema

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

### <a name="userassigned-template"></a>Modelo UserAssigned

Um modelo básico de Gestor de Recursos Azure que implementa uma Aplicação Gerida com uma **identidade atribuída ao utilizador**.

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

## <a name="granting-access-to-azure-resources"></a>Concessão de acesso aos recursos azure

Uma vez que uma Aplicação Gerida seja concedida uma identidade, pode ser-lhe concedido acesso aos recursos azuis existentes. Este processo pode ser feito através da interface de controlo de acesso (IAM) no portal Azure. O nome da Aplicação Gerida ou **identidade atribuída ao utilizador** pode ser pesquisado para adicionar uma atribuição de funções.

![Adicionar atribuição de funções para Aplicação Gerida](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Ligação dos recursos azure existentes

> [!NOTE]
> Uma **identidade atribuída** ao utilizador deve ser [configurada](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes de implementar a Aplicação Gerida. Além disso, a implantação de recursos ligados de Aplicações Geridas só é suportada para o tipo de **mercado.**

A Identidade Gerida também pode ser usada para implementar uma Aplicação Gerida que requer acesso aos recursos existentes durante a sua implantação. Quando a Aplicação Gerida é aprovisionada pelo cliente, as **identidades atribuídas** ao utilizador podem ser adicionadas para fornecer autorizações adicionais à implementação **do modelo principal.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Autoria da CreateUIDefinition com um recurso ligado

Ao associar a implementação da Aplicação Gerida aos recursos existentes, deve ser fornecido tanto o recurso Azure existente como uma **identidade atribuída ao utilizador** com a atribuição de funções aplicável nesse recurso.

 Uma amostra CreateUIDefinição que requer duas inputs: um ID de recurso de interface de rede e um id de recurso de identidade atribuído pelo utilizador.

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

Este CreateUIDefinition.json gera uma experiência de utilizador que tem dois campos. O primeiro campo permite ao utilizador introduzir o ID de recurso Azure para o recurso que está ligado à implementação da Aplicação Gerida. A segunda é que um consumidor introduza o ID de recurso Azure de identidade atribuído ao **utilizador,** que tem acesso ao recurso Azure ligado. A experiência gerada seria como:

![Amostra CreateUIDefinição com duas inputs: um ID de recurso de interface de rede e um ID de recurso de identidade atribuído ao utilizador](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Autor do modelo principal com um recurso ligado

Além de atualizar a CreateUIDefinition, o modelo principal também precisa de ser atualizado para aceitar o id de recurso ligado. O modelo principal pode ser atualizado para aceitar a nova saída adicionando um novo parâmetro. Uma `managedIdentity` vez que a saída sobrepõe o valor no modelo de aplicação gerida gerada, não é passado para o modelo principal e não deve ser incluído na secção de parâmetros.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumir a Aplicação Gerida com recurso ligado

Uma vez criado o pacote de Aplicação Gerida, a Aplicação Gerida pode ser consumida através do portal Azure. Antes de poder ser consumido, existem vários passos pré-requisitos.

- Deve ser criada uma instância do recurso Azure ligado necessário.
- A **identidade atribuída** ao utilizador deve ser criada e dada atribuição de [funções](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ao recurso ligado.
- O ID de recurso ligado existente e o ID de **identidade atribuído** ao utilizador são fornecidos à CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Acesso ao símbolo de identidade gerida

O símbolo da Aplicação Gerida pode agora `listTokens` ser acedido através da api do inquilino da editora. Um pedido de exemplo pode parecer:

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

Parâmetro | Necessário | Descrição
---|---|---
autorizaçãoAudience | *Não* | O APP ID URI do recurso alvo. É também `aud` a reivindicação (do público) do símbolo emitido. O valor padrãohttps://management.azure.com/é "
userAssignedIdentidades | *Não* | A lista de identidades geridas pelo utilizador para recuperar um símbolo para. Se não especificado, `listTokens` devolverá o símbolo para a identidade gerida atribuída pelo sistema.


Uma resposta da amostra pode parecer:

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

A resposta conterá uma série de `value` fichas sob a propriedade:

Parâmetro | Descrição
---|---
access_token | O sinal de acesso solicitado.
expires_in | O número de segundos em que o sinal de acesso será válido.
expires_on | O tempo de espera quando o sinal de acesso expira. Isto é representado como o número de segundos da época.
not_before | O tempo de espera quando o sinal de acesso faz efeito. Isto é representado como o número de segundos da época.
autorizaçãoAudience | O `aud` (público) o sinal de acesso foi pedido. Isto é o mesmo que `listTokens` foi fornecido no pedido.
resourceId | O ID de recurso Azure para o símbolo emitido. Este é ou o ID de aplicação gerido ou o ID de identidade atribuído pelo utilizador.
token_type | O tipo de símbolo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como configurar uma Aplicação Gerida com um Fornecedor Personalizado](../custom-providers/overview.md)
