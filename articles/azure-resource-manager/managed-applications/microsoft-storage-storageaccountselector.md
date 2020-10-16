---
title: Elemento UI do UI do Departamento de Armazenamento
description: Descreve o elemento UI microsoft.storage.storageAccountSelector para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033304"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAçãoSelector Elemento UI

Um controlo para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Amostra de UI

O controlo mostra o valor predefinido.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

O controlo permite ao utilizador criar uma nova conta de armazenamento ou selecionar uma conta de armazenamento existente.

![Microsoft.Storage.StorageAcoctor novo](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

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

## <a name="sample-output"></a>Saída de exemplo

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
- O valor predefinido `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado `constraints.allowedTypes` é escondido, e qualquer tipo não especificado `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são ambos opcionais, mas não podem ser usados simultaneamente.
- Se `options.hideExisting` for **verdade,** o utilizador não pode escolher uma conta de armazenamento existente. O valor predefinido é **falso**.

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
