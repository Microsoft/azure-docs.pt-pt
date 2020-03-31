---
title: Elemento UI MultiStorageAccountCombo
description: Descreve o elemento Microsoft.Storage.MultiStorageAccountCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651881"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI

Um grupo de controlos para a criação de várias contas de armazenamento com nomes que começam com um prefixo comum.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Observações

- O valor `defaultValue.prefix` para é concatenado com um ou mais inteiros para gerar a sequência de nomes de conta de armazenamento. Por exemplo, `defaultValue.prefix` se for `count` **sa** e for **2**, então os nomes da conta de armazenamento **sa1** e **sa2** são gerados. Os nomes de conta de armazenamento gerados são validados automaticamente para a singularidade.
- Os nomes da conta de armazenamento `count`são gerados lexicograficamente com base em . Por exemplo, `count` se for 10, os nomes da conta de armazenamento terminam com inteiros de dois dígitos (01, 02, 03).
- O valor `defaultValue.prefix` predefinido é `defaultValue.type` **nulo,** e para é **Premium_LRS**.
- Qualquer tipo não `constraints.allowedTypes` especificado está escondido, e `constraints.excludedTypes` qualquer tipo não especificado é mostrado. `constraints.allowedTypes`e `constraints.excludedTypes` são ambos opcionais, mas não podem ser usados simultaneamente.
- Além de gerar nomes `count` de conta de armazenamento, é utilizado para definir o multiplicador apropriado para o elemento. Suporta um valor estático, como **2,** ou um `[steps('step1').storageAccountCount]`valor dinâmico de outro elemento, como . O valor predefinido é **de 1**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
