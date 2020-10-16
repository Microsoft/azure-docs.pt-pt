---
title: TagsByResource UI elemento
description: Descreve o elemento UI microsoft.common.TagsByResource para o portal Azure. Utilize para aplicar etiquetas a um recurso durante a implantação.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063927"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource UI elemento

Um controlo para associar [etiquetas](../management/tag-resources.md) com os recursos numa implantação.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

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

## <a name="sample-output"></a>Saída de exemplo

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

- Pelo menos um item na `resources` matriz deve ser especificado.
- Cada elemento `resources` deve ser um tipo de recurso totalmente qualificado. Estes elementos aparecem na redução do **recurso** e são taggable pelo utilizador.
- A saída do controlo é formatada para uma fácil atribuição de valores de etiqueta num modelo de Gestor de Recursos Azure. Para receber a saída do controlo num modelo, inclua um parâmetro no seu modelo, como mostrado no exemplo seguinte:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Para cada recurso que possa ser marcado, atribua a propriedade tags ao valor do parâmetro para esse tipo de recurso:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Utilize a função [se](../templates/template-functions-logical.md#if) tiver acesso ao parâmetro TagsByResource. Permite-lhe atribuir um objeto vazio quando não são atribuídas tags ao tipo de recurso.

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
