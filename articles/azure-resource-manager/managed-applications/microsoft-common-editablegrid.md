---
title: Elemento editável da UI Degrid
description: Descreve o elemento UI Microsoft.Common.EditableGrid para o portal Azure. Permite que os utilizadores recolham entrada tabular.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88893815"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft.Common.EditableGrid UI elemento

Um controlo para recolher entradas tabulares. Todos os campos dentro da grelha são editáveis e o número de linhas pode variar.

## <a name="ui-sample"></a>Amostra de UI

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft.Common.EditableGrid":::

## <a name="schema"></a>Esquema

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Observações

- Os únicos controlos válidos dentro da matriz de colunas são o [TextBox](microsoft-common-textbox.md), [OptionsGroup](microsoft-common-optionsgroup.md)e [DropDown](microsoft-common-dropdown.md).
- A `$rowIndex` variável só é válida em expressões contidas nas crianças das colunas da grelha. É um número inteiro que representa o índice relativo da linha do elemento e a contagem começa em um e incrementa por um. Como mostrado na secção do `"columns":` esquema, o `$rowIndex` é usado para validação.
- Quando as validações são realizadas usando a `$rowIndex` variável, é possível obter o valor da linha atual combinando os `last()` comandos e `take()` comandos.

  Por exemplo:

  `last(take(<reference_to_grid>, $rowIndex))`

- A `label` propriedade não aparece como parte do controlo, mas é exibida no resumo final do separador.
- A `ariaLabel` propriedade é o rótulo de acessibilidade para a grelha. Especifique texto útil para utilizadores que usam leitores de ecrã.
- A `constraints.width` propriedade é usada para definir a largura total da grelha. As opções são _Full,_ _Medium,_ _Small._ O padrão é _Completo_.
- A `width` propriedade em crianças de colunas determina a largura da coluna. As larguras são especificadas utilizando unidades fracionadas como _3fr,_ com o espaço total a ser atribuído a colunas proporcionais às suas unidades. Se não for especificada largura de coluna, o padrão é _de 1fr_.

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
