---
title: MultiStorageAccountDI elemento UI
description: Descreve o elemento Microsoft.Storage.MultiStorageAccountCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: a8172b63039d2d247f30fca4099254cb8fca068e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073425"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountDI

Um grupo de controlos para criar várias contas de armazenamento com nomes que começam com um prefixo comum.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft-storage-multistorageaccountcombo.png)

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

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Observações

- O valor `defaultValue.prefix` é concatentado com um ou mais inteiros para gerar a sequência de nomes de conta de armazenamento. Por exemplo, se `defaultValue.prefix` for **sa** e `count` é **2**, então são gerados nomes de conta de armazenamento **sa1** e **sa2.** Os nomes das contas de armazenamento gerados são validados automaticamente para singularidade.
- Os nomes das contas de armazenamento são gerados lexicograficamente com base em `count` . Por exemplo, se `count` for 10, os nomes da conta de armazenamento terminam com inteiros de dois dígitos (01, 02, 03).
- O valor por defeito `defaultValue.prefix` é **nulo,** e para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado `constraints.allowedTypes` é escondido, e qualquer tipo não especificado `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são ambos opcionais, mas não podem ser usados simultaneamente.
- Além de gerar nomes de conta de armazenamento, `count` é usado para definir o multiplicador apropriado para o elemento. Suporta um valor estático, como **2,** ou um valor dinâmico de outro elemento, como `[steps('step1').storageAccountCount]` . O valor predefinido é **1**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
