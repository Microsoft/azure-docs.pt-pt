---
title: Elemento de UI dropdown
description: Descreve o elemento UI Microsoft.Common.DropDown para o portal Azure. Utilize para selecionar as opções disponíveis ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004498"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI elemento

Um controlo de seleção com uma lista de abandono. Pode permitir a seleção de apenas um item ou vários itens. Também pode opcionalmente incluir uma descrição com os itens.

## <a name="ui-sample"></a>Amostra de UI

O elemento DropDown tem diferentes opções que determinam a sua aparência no portal.

Quando apenas um item é permitido para seleção, o controlo aparece como:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft.Common.DropDown seleção única":::

Quando as descrições são incluídas, o controlo aparece como:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft.Common.DropDown seleção única com descrições":::

Quando o multi-selecção está ativado, o controlo adiciona uma **seleção de todas as** opções e caixas de verificação para selecionar mais de um item:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft.Common.DropDown multi-selecção":::

As descrições podem ser incluídas com vários selecionadores ativados.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft.Common.DropDown multi-selecione com descrições":::

Quando a filtragem está ativada, o controlo inclui uma caixa de texto para adicionar o valor de filtragem.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft.Common.DropDown multi-selecione com descrições":::

## <a name="schema"></a>Esquema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"two"
```

## <a name="remarks"></a>Observações

- Utilize `multiselect` para especificar se os utilizadores podem selecionar mais do que um item.
- Por predefinição, `selectAll` é `true` quando o multi-select está ativado.
- A `filter` propriedade permite que os utilizadores procurem dentro de uma longa lista de opções.
- O rótulo para `constraints.allowedValues` é o texto do visor de um item, e o seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor predefinido deve ser um rótulo presente em `constraints.allowedValues` . Se não for especificado, o primeiro item `constraints.allowedValues` é selecionado. O valor por defeito é **nulo.**
- `constraints.allowedValues`deve ter pelo menos um item.
- Para imitar um valor que não está a ser necessário, adicione um item com uma etiqueta e valor de `""` (corda vazia) a `constraints.allowedValues` .
- A `defaultDescription` propriedade é usada para itens que não têm uma descrição.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
