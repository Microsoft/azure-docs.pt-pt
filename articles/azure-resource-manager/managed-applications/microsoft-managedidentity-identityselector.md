---
title: Elemento UI do Seletor de Identidade
description: Descreve o elemento Microsoft.ManagedIdentity.IdentitySelector UI para o portal Azure. Usar para atribuir identidades geridas a um recurso.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77087547"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI

Um controlo para a atribuição [de identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) para um recurso numa implantação.

## <a name="ui-sample"></a>Amostra de UI

O controlo consiste nos seguintes elementos:

![Microsoft.ManagedIdentity.IdentitySelector primeiro passo](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Quando o utilizador seleciona **Adicionar,** abre-se o seguinte formulário. O utilizador pode selecionar uma ou mais identidades atribuídas ao utilizador para o recurso.

![Microsoft.ManagedIdentity.IdentitySelector segundo passo](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

As identidades selecionadas são exibidas na tabela. O utilizador pode adicionar ou apagar itens desta tabela.

![Microsoft.ManagedIdentity.IdentitySelector terceiro passo](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

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

## <a name="sample-output"></a>Resultado da amostra

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

- Utilize **o padrãoValue.systemAssignedIdentity** para definir um valor inicial para o controlo de opções de identidade atribuídos pelo sistema. O valor padrão está **desligado.** São permitidos os seguintes valores:
  - **On** – Um sistema de identidade atribuído é atribuído ao recurso.
  - **Off** – Um sistema de identidade atribuído não é atribuído ao recurso.
  - **OnOnly** – Um sistema de identidade atribuído é atribuído ao recurso. Os utilizadores não podem editar este valor durante a implementação.
  - **OffOnly** – Um sistema de identidade atribuído não é atribuído ao recurso. Os utilizadores não podem editar este valor durante a implementação.

- Se **as opções.hideSystemAssignedIdentity** for **em realidade,** o UI para configurar a identidade atribuída não é apresentado. O valor predefinido desta opção é **falso**.
- Se **as opções.hideUserAssignedIdentity** estiver definida como **verdadeira,** o UI para configurar a identidade atribuída ao utilizador não é apresentado. O recurso não é atribuído a um utilizador que lhe seja atribuída identidade. O valor predefinido desta opção é **falso**.

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).