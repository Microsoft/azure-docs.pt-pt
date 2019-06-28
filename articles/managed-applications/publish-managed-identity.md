---
title: Aplicação gerida do Azure com a identidade gerida
description: Saiba como configurar uma aplicação gerida com uma identidade gerida. Identidade gerida pode ser utilizada para implementar aplicações geridas ligado a recursos existentes, conceda as aplicações geridas para gerir recursos do Azure fora do grupo de recursos gerido e fornecer uma identidade operacional das aplicações geridas para o registo de atividades e outros serviços no Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: be141e208016784b689262394798012c2212ba5b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312239"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicação gerida do Azure com a identidade gerida

> [!NOTE]
> Suporte de identidade gerida para aplicações geridas está atualmente em pré-visualização. Utilize a versão de api de 2018-09-01-pré-visualização para utilizar a identidade gerida.

Saiba como configurar uma aplicação gerida para conter uma identidade gerida. Identidade gerida pode ser utilizada para permitir que o cliente conceder o acesso de aplicação gerida para recursos adicionais de existentes. A identidade é gerida pela plataforma do Azure e não necessita de aprovisionar ou girar quaisquer segredos. Para mais informações sobre identidades geridas no Azure Active Directory (AAD), consulte [geridos identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode ser concedido a dois tipos de identidades:

- R **atribuído ao sistema de identidade** está associada ao seu aplicativo e é eliminado se a aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída de sistema.
- R **atribuído ao utilizador identidade** é autónomo recursos do Azure que podem ser atribuídos à sua aplicação. Uma aplicação pode ter várias identidades atribuído ao utilizador.

## <a name="how-to-use-managed-identity"></a>Como utilizar a identidade gerido

Identidade gerida permite vários cenários para aplicações geridas. Seguem-se alguns cenários comuns que podem ser resolvidos:

- Implantando um aplicativo geridos ligados aos recursos do Azure existentes. Um exemplo é implementar uma máquina virtual do Azure (VM) na aplicação gerida que está ligado a uma [interface de rede existente](../virtual-network/virtual-network-network-interface-vm.md).
- Conceder o acesso de aplicação gerida e o publisher para recursos do Azure fora da **grupo de recursos gerido**.
- Proporcionar uma identidade operacional das aplicações geridas para o registo de atividades e outros serviços no Azure.

## <a name="adding-managed-identity"></a>Adicionar identidade gerida

Criando um aplicativo gerenciado com uma identidade gerida necessita de uma propriedade adicional para ser definida no recurso do Azure. O exemplo seguinte mostra um exemplo **identidade** propriedade:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existem duas formas comuns para criar uma aplicação gerida com **identidade**: [Createuidefinition](./create-uidefinition-overview.md) e [modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Para único simple criar cenários, CreateUIDefinition deve ser utilizado para ativar a identidade gerido, porque fornece uma experiência mais rica. No entanto, ao lidar com avançado ou complexos automatizada de sistemas que necessitam ou várias implementações de aplicações geridas, modelos podem ser utilizadas.

### <a name="using-createuidefinition"></a>Usando CreateUIDefinition

Uma aplicação gerida pode ser configurada com a identidade gerida através da [Createuidefinition](./create-uidefinition-overview.md). Na [secção de saídas](./create-uidefinition-overview.md#outputs), a chave `managedIdentity` pode ser utilizado para substituir a propriedade de identidade do modelo do aplicativo gerenciado. O abaixo de exemplo permitirá **atribuído ao sistema** identidade sobre a aplicação gerida. Objetos mais complexos de identidade podem ser constituídos por utilizando os elementos de CreateUIDefinition pedir o consumidor de entradas de dados. Estas entradas podem ser utilizadas para construir aplicações geridas com **atribuído ao utilizador identidade**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando utilizar CreateUIDefinition para a identidade gerido

Seguem-se algumas recomendações sobre quando usar CreateUIDefinition para ativar a identidade gerida em aplicações geridas.

- A criação de aplicações geridas vai através do portal do Azure ou do marketplace.
- A identidade gerida requer intervenção do consumidor complexos.
- A identidade gerida é necessária após a criação da aplicação gerida.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Um CreateUIDefinition básico que permite a identidade de SystemAssigned para a aplicação gerida.

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

Um CreateUIDefinition básico que usa um **atribuído ao utilizador identidade** recursos como entrada e permite que a identidade de UserAssigned para a aplicação gerida.

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

Createuidefinition acima gera uma experiência de utilizador de criar com uma caixa de texto para um consumidor introduzir os **atribuído ao utilizador identidade** ID de recurso do Azure. A experiência gerada teria o seguinte aspeto:

![Identidade de utilizador atribuída de exemplo CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizando modelos Azure Resource Manager

> [!NOTE]
> Modelos de aplicação gerida do Marketplace são gerados automaticamente para os clientes através do portal do Azure criam a experiência.
> Nestes cenários, o `managedIdentity` chave de saída na CreateUIDefinition deve ser usado para ativada a identidade.

Também pode ser ativada a identidade gerida através de modelos Azure Resource Manager. O abaixo de exemplo permitirá **atribuído ao sistema** identidade sobre a aplicação gerida. Objetos mais complexos de identidade podem ser formados utilizando parâmetros de modelo do Azure Resource Manager para fornecer entradas. Estas entradas podem ser utilizadas para construir aplicações geridas com **atribuído ao utilizador identidade**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando utilizar os modelos Azure Resource Manager para a identidade gerido

Seguem-se algumas recomendações sobre quando usar os modelos Azure Resource Manager para ativar a identidade gerida em aplicações geridas.

- Aplicações geridas podem ser implementadas de por meio de programação com base num modelo.
- Atribuições de função personalizada para a identidade gerida são necessários para aprovisionar a aplicação gerida.
- A aplicação gerida não é necessário o fluxo de criação de portal e do marketplace do Azure.

#### <a name="systemassigned-template"></a>Modelo de SystemAssigned

Um modelo básico do Azure Resource Manager que implementa uma aplicação gerida com **atribuído ao sistema** identidade.

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

### <a name="userassigned-template"></a>Modelo de UserAssigned

Um modelo básico do Azure Resource Manager que implementa uma aplicação gerida com um **atribuído ao utilizador identidade**.

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

## <a name="granting-access-to-azure-resources"></a>Conceder acesso aos recursos do Azure

Depois de uma aplicação gerida é concedida uma identidade, ele pode ser concedido acesso aos recursos do azure existentes. Este processo pode ser feito por meio da interface do controlo (IAM) de acesso no portal do Azure. O nome da aplicação gerida ou **atribuído ao utilizador identidade** pode ser pesquisado para adicionar uma atribuição de função.

![Adicionar atribuição de função para a aplicação gerida](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Ligar os recursos do Azure existentes

> [!NOTE]
> R **atribuído ao utilizador identidade** tem de ser [configurado](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes de implementar a aplicação gerida. Além disso, a implementação de recurso ligado das aplicações geridas só é suportada para o **marketplace** tipo.

Identidade gerida também pode ser utilizada para implementar uma aplicação gerida que necessite de acesso a recursos existentes durante a implementação. Quando a aplicação gerida é aprovisionada pelo cliente, **identidiades atribuídas** podem ser adicionados para fornecer autorizações adicionais para o **mainTemplate** implementação.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Criação CreateUIDefinition com um recurso ligado

Quando ligar a implementação da aplicação gerida a recursos existentes, tanto o recurso do Azure existente e um **atribuído ao utilizador identidade** com a função aplicável atribuição esse recurso tem de ser fornecida.

 Um exemplo CreateUIDefinition requer duas entradas: um ID de recurso de interface de rede e um id de recurso de identidade atribuída ao utilizador.

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

Este Createuidefinition gera uma experiência de utilizador de criar que tem dois campos. O primeiro campo permite ao utilizador introduzir o ID de recurso do Azure para o recurso a ser ligado a implementação da aplicação gerida. A segunda é para um consumidor introduzir os **atribuído ao utilizador identidade** ID de recurso do Azure, que tem acesso ao recurso do Azure ligado. A experiência gerada teria o seguinte aspeto:

![CreateUIDefinition com duas entradas de exemplo: ID de recurso e um ID de recurso de identidade atribuída ao utilizador uma interface de rede](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Criação mainTemplate com um recurso ligado

Além de atualizar o CreateUIDefinition, o modelo principal também precisa de ser atualizado para aceitar o com êxito no ID de recurso ligado. O modelo principal pode ser atualizado para aceitar a saída nova, adicionando um novo parâmetro. Uma vez que o `managedIdentity` saída substitui o valor no modelo de aplicação gerida gerado, mas não é passada para o modelo principal e não devem ser incluída na secção de parâmetros.

Um modelo de principal de exemplo que define o perfil de rede a uma interface de rede existentes fornecida pela CreateUIDefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumir a aplicação gerida com um recurso ligado

Depois de criar o pacote de aplicação gerida, a aplicação gerida pode ser consumida por meio do portal do Azure. Antes de ele pode ser consumido, existem vários passos de pré-requisitos.

- Tem de ser criada uma instância do recurso do Azure ligado necessária.
- O **atribuído ao utilizador identidade** tem de ser [criada e tendo em conta as atribuições de funções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para o recurso associado.
- O existente ligado o ID de recurso e o **atribuído ao utilizador identidade** ID são fornecidos para a CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Acessando o token de identidade gerido

O token da aplicação gerida pode agora ser acedido através do `listTokens` api a partir do inquilino do publicador. Um exemplo de solicitação a seguinte aparência:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1
```

Como pode ser uma resposta de exemplo:

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Como configurar uma aplicação gerida com um provedor personalizado](./custom-providers-overview.md)
