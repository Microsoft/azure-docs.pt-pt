---
title: Elemento UI do uI do identitário de identidade
description: Descreve o elemento UI da Microsoft.ManagedIdentity.IdentitySelector UI para o portal Azure. Utilize para atribuir identidades geridas a um recurso.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063371"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI elemento

Um controlo para atribuir [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) para um recurso numa implantação.

## <a name="ui-sample"></a>Amostra de UI

O controlo consiste nos seguintes elementos:

![Microsoft.ManagedIdentity.IdentitySelector primeiro passo](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Quando o utilizador seleciona **Adicionar,** abre-se o seguinte formulário. O utilizador pode selecionar uma ou mais identidades atribuídas pelo utilizador para o recurso.

![Microsoft.ManagedIdentity.IdentitySelector segundo passo](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

As identidades selecionadas são apresentadas na tabela. O utilizador pode adicionar ou eliminar itens desta tabela.

![Microsoft.ManagedIdentity.IdentitySelector terceiro passo](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Observações

- Utilize **defaultValue.systemAssignedIdentity** para definir um valor inicial para o controlo de opções de identidade atribuídas pelo sistema. O valor **predefinido** está desligado . São permitidos os seguintes valores:
  - **Em** – Uma identidade atribuída ao sistema é atribuída ao recurso.
  - **Off** – Um sistema atribuído à identidade não é atribuído ao recurso.
  - **OnOnly** – Uma identidade atribuída ao sistema é atribuída ao recurso. Os utilizadores não podem editar este valor durante a implementação.
  - **OffOnly** – Uma identidade atribuída ao sistema não é atribuída ao recurso. Os utilizadores não podem editar este valor durante a implementação.

- Se **as opções.hideSystemAssignedIdentity** estiver definida como **verdadeira,** a UI para configurar a identidade atribuída ao sistema não é apresentada. O valor predefinido para esta opção é **falso.**
- Se **opções.hideUserAssignedIdentity** estiver definida como **verdadeira,** o UI para configurar a identidade atribuída pelo utilizador não é apresentado. O recurso não é atribuído a um utilizador de identidade atribuída. O valor predefinido para esta opção é **falso.**

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).