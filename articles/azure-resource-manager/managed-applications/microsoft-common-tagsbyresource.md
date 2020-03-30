---
title: Elemento UI TagsByResource
description: Descreve o elemento Microsoft.Common.TagsByResource UI para o portal Azure. Utilize para aplicar etiquetas num recurso durante a implantação.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652206"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource UI

Um controlo para associar [tags](../management/tag-resources.md) com os recursos numa implantação.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Observações

- Deve especificar-se pelo `resources` menos um item da matriz.
- Cada elemento `resources` deve ser um tipo de recurso totalmente qualificado. Estes elementos aparecem no dropdown do **Recurso** e são marcados pelo utilizador.
- A saída do controlo é formatada para uma fácil atribuição de valores de etiqueta num modelo de Gestor de Recursos Azure. Para receber a saída do controlo num modelo, inclua um parâmetro no seu modelo, como mostra o seguinte exemplo:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Para cada recurso que possa ser marcado, atribua a propriedade de etiquetas ao valor do parâmetro para esse tipo de recurso:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Utilize a função [se](../templates/template-functions-logical.md#if) ao aceder ao parâmetro tagsByResource. Permite-lhe atribuir um objeto vazio quando não são atribuídas etiquetas ao tipo de recurso dado.

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
