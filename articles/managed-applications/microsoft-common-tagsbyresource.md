---
title: Elemento de interface do usuário TagsByResource do Azure
description: Descreve o elemento de interface do usuário Microsoft. Common. TagsByResource para portal do Azure. Use para aplicar marcas a um recurso durante a implantação.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933145"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Elemento de interface do usuário Microsoft. Common. TagsByResource

Um controle para associar [marcas](../azure-resource-manager/resource-group-using-tags.md) com os recursos em uma implantação.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Menu suspenso Microsoft. Common.](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

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

- Pelo menos um item na matriz de `resources` deve ser especificado.
- Cada elemento em `resources` deve ser um tipo de recurso totalmente qualificado. Esses elementos aparecem na lista suspensa de **recursos** e são taggable pelo usuário.
- A saída do controle é formatada para facilitar a atribuição de valores de marca em um modelo de Azure Resource Manager. Para receber a saída do controle em um modelo, inclua um parâmetro em seu modelo, conforme mostrado no exemplo a seguir:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Para cada recurso que pode ser marcado, atribua a propriedade Tags ao valor do parâmetro para esse tipo de recurso:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Use a função [If](../azure-resource-manager/resource-group-template-functions-logical.md#if) ao acessar o parâmetro tagsByResource. Ele permite que você atribua um objeto vazio quando nenhuma marca é atribuída ao tipo de recurso fornecido.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
