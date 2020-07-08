---
title: Elemento UI do UI do Departamento de Armazenamento
description: Descreve o elemento UI microsoft.storage.storageAccountSelector para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651894"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAçãoSelector Elemento UI

Um controlo para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Amostra de UI

O controlo mostra o valor predefinido.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

O controlo permite ao utilizador criar uma nova conta de armazenamento ou selecionar uma conta de armazenamento existente.

![Microsoft.Storage.StorageAcoctor novo](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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
- O valor predefinido `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado `constraints.allowedTypes` é escondido, e qualquer tipo não especificado `constraints.excludedTypes` é mostrado. `constraints.allowedTypes`e `constraints.excludedTypes` são ambos opcionais, mas não podem ser usados simultaneamente.
- Se `options.hideExisting` for **verdade,** o utilizador não pode escolher uma conta de armazenamento existente. O valor predefinido é **falso**.

## <a name="next-steps"></a>Próximos passos
* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
