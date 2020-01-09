---
title: Elemento de interface do usuário MultiStorageAccountCombo
description: Descreve o elemento de interface do usuário Microsoft. Storage. MultiStorageAccountCombo para portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651881"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Elemento de interface do usuário Microsoft. Storage. MultiStorageAccountCombo

Um grupo de controles para criar várias contas de armazenamento com nomes que começam com um prefixo comum.

## <a name="ui-sample"></a>Exemplo de interface do usuário

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

- O valor de `defaultValue.prefix` é concatenado com um ou mais inteiros para gerar a sequência de nomes de conta de armazenamento. Por exemplo, se `defaultValue.prefix` for **SA** e `count` for **2**, os nomes de conta de armazenamento **SA1** e **SA2** serão gerados. Os nomes de conta de armazenamento gerados são validados para exclusividade automaticamente.
- Os nomes de conta de armazenamento são gerados modo lexicográfico com base em `count`. Por exemplo, se `count` for 10, os nomes de conta de armazenamento terminarão com inteiros de dois dígitos (01, 02, 03).
- O valor padrão para `defaultValue.prefix` é **NULL**e para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado em `constraints.allowedTypes` é ocultado e qualquer tipo não especificado em `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são opcionais, mas não podem ser usados simultaneamente.
- Além de gerar nomes de conta de armazenamento, `count` é usado para definir o multiplicador apropriado para o elemento. Ele dá suporte a um valor estático, como **2**, ou a um valor dinâmico de outro elemento, como `[steps('step1').storageAccountCount]`. O valor padrão é **1**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
