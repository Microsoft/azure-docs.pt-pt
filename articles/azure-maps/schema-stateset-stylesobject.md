---
title: StylesObject Schema guia de referência para Mapas Dinâmicos de Azure
description: Guia de referência para o dinâmico Azure Maps StylesObject schema e sintaxe.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903348"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject Schema guia de referência para mapas dinâmicos

> [!IMPORTANT]
> Os serviços do Azure Maps Creator estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 É `StylesObject` uma matriz que representa `StyleObject` estilos estadistas. Utilize o serviço Estado de [FuncionalidadeS Azure](/rest/api/maps/featurestate) Maps (Preview) para aplicar os seus estilos de stateet às funcionalidades de dados do mapa interno. Uma vez criado os seus estilos de stateet e os associando com funcionalidades de mapas interiores, pode usá-los para criar mapas interiores dinâmicos. Para obter mais informações sobre a criação de mapas dinâmicos no interior, consulte [implementar o estilo dinâmico para os mapas interiores do Criador.](indoor-map-dynamic-styling.md)

## <a name="styleobject"></a>StyleObject

A `StyleObject` é uma das seguintes regras de estilo:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

O JSON abaixo mostra o uso exemplo de cada um dos três tipos de estilo.  O `BooleanTypeStyleRule` é usado para determinar o estilo dinâmico para características cuja propriedade é verdadeira e `occupied` falsa.  O `NumericTypeStyleRule` é usado para determinar o estilo para características cuja propriedade se enquadra dentro de um determinado `temperature` alcance. Finalmente, o `StringTypeStyleRule` é usado para combinar estilos específicos com `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStylerule

 A `NumericTypeStyleRule` é um e consiste nas  [`StyleObject`](#styleobject) seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string | O  estado ou nome de propriedade dinâmica. A `keyName` deve ser único dentro da `StyleObject` matriz.| Yes |
| `type` | string | Valor é "numérico". | Yes |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Uma variedade de gamas de estilo numérico com cores associadas. Cada gama define uma cor que deve ser usada quando o valor do *estado* satisfaz a gama.| Yes |

### <a name="numberruleobject"></a>NúmeroRuleObject

A `NumberRuleObject` é constituída por um e um [`RangeObject`](#rangeobject) `color` imóvel. Se o valor do *estado* cair na gama, a sua cor para exibição será a cor especificada na `color` propriedade.

Se definir várias gamas de sobreposição, a cor escolhida será a cor definida na primeira gama que está satisfeita.

Na amostra JSON seguinte, ambas as gamas serão verdadeiras quando o valor do *estado* estiver entre 50-60. No entanto, a cor que será usada é porque é `#343deb` a primeira gama da lista que foi satisfeita.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | O [RangeObject](#rangeobject) define um conjunto de condições de alcance lógica, que, `true` se, alterar a cor do display do *estado* para a cor especificada na `color` propriedade. Se `range` não for especificado, então a cor definida na `color` propriedade será sempre usada.   | No |
| `color` | string | A cor a usar quando o valor do estado cai na gama. A `color` propriedade é uma cadeia JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255,0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL ("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Cores HTML predefinidas, como amarelo, e azul.</li></ul> | Yes |

### <a name="rangeobject"></a>RangeObject

O `RangeObject` define um valor de gama numérica de um [`NumberRuleObject`](#numberruleobject) . Para que o valor *do Estado* caia na gama, todas as condições definidas devem ser verdadeiras.

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `minimum` | double | Todo o número x que x ≥ `minimum` .| No |
| `maximum` | double | Todo o número x que x ≤ `maximum` . | No |
| `exclusiveMinimum` | double | Todo o número x que x > `exclusiveMinimum` .| No |
| `exclusiveMaximum` | double | Todo o número x que x < `exclusiveMaximum` .| No |

### <a name="example-of-numerictypestylerule"></a>Exemplo de NumericTypeStyleRule

O seguinte JSON ilustra um `NumericTypeStyleRule` *estado* chamado `temperature` . Neste exemplo, [`NumberRuleObject`](#numberruleobject) contém duas gamas de temperatura definidas e os seus estilos de cores associados. Se o intervalo de temperatura for de 50-69, o visor deve utilizar a cor `#343deb` .  Se o intervalo de temperatura for de 31-70, o visor deve utilizar a cor `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStylerule

A `StringTypeStyleRule` é um e consiste nas [`StyleObject`](#styleobject) seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O  estado ou nome de propriedade dinâmica.  A `keyName` deve ser único dentro da  `StyleObject` matriz.| Yes |
| `type` | string |Valor é "corda". | Yes |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Uma variedade de N número de valores *do estado.*| Yes |

### <a name="stringruleobject"></a>StringRuleObject

A `StringRuleObject` consiste em até N número de valores estatais que são os possíveis valores de corda da propriedade de uma característica. Se o valor da propriedade da funcionalidade não corresponder a nenhum dos valores definidos do estado, essa funcionalidade não terá um estilo dinâmico. Se forem dados valores de estado duplicados, o primeiro tem precedência.

O valor das cordas é sensível a maiôs.

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | A cor quando a cadeia de valor é stateValue1. | No |
| `stateValue2` | string | A cor quando a cadeia de valor é stateValue. | No |
| `stateValueN` | string | A cor quando a cadeia de valor é stateValueN. | No |

### <a name="example-of-stringtypestylerule"></a>Exemplo de StringTypeStyleRule

O JSON seguinte ilustra um `StringTypeStyleRule` que define estilos associados a tipos de reuniões específicos.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` é um e consiste nas [`StyleObject`](#styleobject) seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O  estado ou nome de propriedade dinâmica.  A `keyName` deve ser único dentro da `StyleObject`  matriz.| Yes |
| `type` | string |Valor é "boolean". | Yes |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| Um par booleano com cores `true` para valores e `false` valores *de estado.*| Yes |

### <a name="booleanruleobject"></a>BooleanRuleObject

Um `BooleanRuleObject` define cores `true` e `false` valores.

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `true` | string | A cor a usar quando o valor do *estado* é `true` . A `color` propriedade é uma cadeia JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255,0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL ("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Cores HTML predefinidas, como amarelo, e azul.</li></ul>| Yes |
| `false` | string | A cor a usar quando o valor do *estado* é `false` . | Yes |

### <a name="example-of-booleantypestylerule"></a>Exemplo de BooleanTypeStyleRule

O seguinte JSON ilustra um `BooleanTypeStyleRule` *estado* chamado `occupied` . O [`BooleanRuleObject`](#booleanruleobject) define cores para `true` valores e `false` valores.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```