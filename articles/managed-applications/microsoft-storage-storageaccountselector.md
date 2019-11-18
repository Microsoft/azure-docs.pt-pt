---
title: Elemento de interface do usuário StorageAccountSelector do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Storage. StorageAccountSelector para portal do Azure.
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
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151453"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento de interface do usuário Microsoft. Storage. StorageAccountSelector

Um controle para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O controle mostra o valor padrão.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

O controle permite que o usuário crie uma nova conta de armazenamento ou selecione uma conta de armazenamento existente.

![Microsoft. Storage. StorageAccountSelector novo](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Se especificado, `defaultValue.name` será validado automaticamente para exclusividade. Se o nome da conta de armazenamento não for exclusivo, o usuário deverá especificar um nome diferente ou escolher uma conta de armazenamento existente.
- O valor padrão para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado em `constraints.allowedTypes` é ocultado e qualquer tipo não especificado em `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são opcionais, mas não podem ser usados simultaneamente.
- Se `options.hideExisting` for **true**, o usuário não poderá escolher uma conta de armazenamento existente. O valor predefinido é **false**.

## <a name="next-steps"></a>Passos seguintes
* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
