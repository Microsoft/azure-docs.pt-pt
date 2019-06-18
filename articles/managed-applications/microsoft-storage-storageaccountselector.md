---
title: Elemento de interface do Usuário de StorageAccountSelector do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Storage.StorageAccountSelector para o portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104871"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento de interface do Usuário de Microsoft.Storage.StorageAccountSelector
Um controlo para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do Usuário

O controlo mostra o valor predefinido.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

O controle permite que o utilizador para criar uma nova conta de armazenamento ou selecione uma conta de armazenamento existente.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se for especificado, `defaultValue.name` é validado automaticamente exclusividade. Se o nome da conta de armazenamento não for exclusivo, o utilizador tem de especificar um nome diferente ou escolha uma conta de armazenamento existente.
- O valor predefinido para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado nas `constraints.allowedTypes` está oculta e qualquer tipo não especificado no `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são ambos opcionais, mas não pode ser utilizada em simultâneo.
- Se `options.hideExisting` é **true**, o utilizador não é possível escolher uma conta de armazenamento existente. O valor predefinido é **false**.

## <a name="sample-output"></a>Resultado da amostra

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
