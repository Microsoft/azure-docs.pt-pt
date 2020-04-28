---
title: Elemento UI do StorageAccountSelector
description: Descreve o elemento Microsoft.Storage.StorageAccountSelector UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651894"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.Storage.StorageAccountSelector UI

Um controlo para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Amostra de UI

O controlo mostra o valor padrão.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

O controlo permite ao utilizador criar uma nova conta de armazenamento ou selecionar uma conta de armazenamento existente.

![Microsoft.Storage.Storage.StorageAccountSelector novo](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

## <a name="sample-output"></a>Resultado da amostra

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Observações

- Se especificado, `defaultValue.name` é validado automaticamente para singularidade. Se o nome da conta de armazenamento não for único, o utilizador deve especificar um nome diferente ou escolher uma conta de armazenamento existente.
- O valor `defaultValue.type` predefinido é **Premium_LRS**.
- Qualquer tipo não `constraints.allowedTypes` especificado está escondido, e `constraints.excludedTypes` qualquer tipo não especificado é mostrado. `constraints.allowedTypes`e `constraints.excludedTypes` são ambos opcionais, mas não podem ser usados simultaneamente.
- Se `options.hideExisting` for **verdade,** o utilizador não pode escolher uma conta de armazenamento existente. O valor predefinido é **falso**.

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
