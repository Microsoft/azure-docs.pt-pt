---
title: Elemento de interface do Usuário de MultiStorageAccountCombo do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Storage.MultiStorageAccountCombo para o portal do Azure.
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
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252433"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Elemento de interface do Usuário de Microsoft.Storage.MultiStorageAccountCombo
Um grupo de controles para a criação de várias contas de armazenamento com nomes que começam com um prefixo comum.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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

## <a name="remarks"></a>Observações
- O valor para `defaultValue.prefix` é concatenado com números inteiros de um ou mais para gerar a sequência de nomes de conta de armazenamento. Por exemplo, se `defaultValue.prefix` é **sa** e `count` é **2**, em seguida, os nomes da conta de armazenamento **sa1** e **sa2** são gerados. Nomes de conta de armazenamento geradas automaticamente são validados exclusividade.
- Os nomes de conta de armazenamento são gerados lexicographically com base no `count`. Por exemplo, se `count` é 10, em seguida, os nomes de conta de armazenamento terminam com dois dígitos inteiros (01, 02, 03).
- O valor predefinido para `defaultValue.prefix` é **nulo**e para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo não especificado nas `constraints.allowedTypes` está oculta e qualquer tipo não especificado no `constraints.excludedTypes` é mostrado. `constraints.allowedTypes` e `constraints.excludedTypes` são ambos opcionais, mas não pode ser utilizada em simultâneo.
- Além de gerar nomes de contas de armazenamento, `count` é usado para definir o multiplicador apropriado para o elemento. Ele oferece suporte a um valor estático, como **2**, ou um valor dinâmico a partir de outro elemento, como `[steps('step1').storageAccountCount]`. O valor predefinido é **1**.

## <a name="sample-output"></a>Resultado da amostra

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
