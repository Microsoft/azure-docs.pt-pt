---
title: StylesObject para Mapas Dinâmicos de Azure
description: Guia de referência para o esquema JSON e sintaxe para o StylesObject usado na criação em mapas Azure dinâmicos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b085fbc6e330d38b59fce0c494f672b00c712b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85120528"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject Schema guia de referência para Mapas Dinâmicos

Este artigo é um guia de referência para o esquema json e sintaxe para o `StylesObject` . É `StylesObject` uma matriz que representa `StyleObject` estilos estadistas. Utilize o [serviço Estado de Recurso Azure](https://docs.microsoft.com/rest/api/maps/featurestate) Maps Creator para aplicar os seus estilos de stateet às funcionalidades de dados do mapa interior. Uma vez criado os seus estilos de stateet e os associando com funcionalidades de mapas interiores, pode usá-los para criar mapas interiores dinâmicos. Para obter mais informações sobre a criação de mapas dinâmicos no interior, consulte [implementar o estilo dinâmico para os mapas interiores do Criador.](indoor-map-dynamic-styling.md)

## <a name="styleobject"></a>StyleObject

A `StyleObject` é como um ou um [`BooleanTypeStyleRule`](#booleantypestylerule) [`NumericTypeStyleRule`](#numerictypestylerule) .

O JSON abaixo mostra um `BooleanTypeStyleRule` nome `occupied` e um `NumericTypeStyleRule` `temperature` nome.

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
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStylerule

 A `NumericTypeStyleRule` é um e consiste nas  [`StyleObject`](#styleobject) seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string | O *state* estado ou nome de propriedade dinâmica. A `keyName` deve ser único dentro da `StyleObject` matriz.| Sim |
| `type` | string | Valor é "numérico". | Sim |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Uma variedade de gamas de estilo numérico com cores associadas. Cada gama define uma cor que deve ser usada quando o valor do *estado* satisfaz a gama.| Sim |

### <a name="numberruleobject"></a>NúmeroRuleObject

A `NumberRuleObject` é constituída por um e um [`RangeObject`](#rangeobject) `color` imóvel. Se o valor do *estado* cair na gama, a sua cor para exibição será a cor especificada na `color` propriedade.

Se definir várias gamas de sobreposição, a cor escolhida será a cor que é definida na primeira gama que está satisfeita.

Na amostra JSON seguinte, ambas as gamas serão verdadeiras quando o valor do *estado* estiver entre 50-60. No entanto, a cor que será usada é `#343deb` porque é a primeira gama da lista que foi satisfeita.

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
| `range` | [RangeObject](#rangeobject) | O [RangeObject](#rangeobject) define um conjunto de condições de alcance lógica, que, `true` se, alterar a cor do display do *estado* para a cor especificada na `color` propriedade. Se `range` não for especificado, então a cor definida na `color` propriedade será sempre usada.   | Não |
| `color` | string | A cor a usar quando o valor do estado cai na gama. A `color` propriedade é uma cadeia JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255,0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL ("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Cores HTML predefinidas, como amarelo, e azul.</li></ul> | Sim |

### <a name="rangeobject"></a>RangeObject

O `RangeObject` define um valor de gama numérica de um [`NumberRuleObject`](#numberruleobject) . Para que o valor *do Estado* caia na gama, todas as condições definidas devem ser verdadeiras. 

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `minimum` | double | Todo o número x que x ≥ `minimum` .| Não |
| `maximum` | double | Todo o número x que x ≤ `maximum` . | Não |
| `exclusiveMinimum` | double | Todo o número x que x > `exclusiveMinimum` .| Não |
| `exclusiveMaximum` | double | Todo o número x que x < `exclusiveMaximum` .| Não |

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

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` é um e consiste nas [`StyleObject`](#styleobject) seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O *state* estado ou nome de propriedade dinâmica.  A `keyName` deve ser único dentro da matriz de estilo.| Sim |
| `type` | string |Valor é "boolean". | Sim |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| Um par booleano com cores `true` para valores e `false` valores *de estado.*| Sim |

### <a name="booleanruleobject"></a>BooleanRuleObject

Um `BooleanRuleObject` define cores `true` e `false` valores.

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `true` | string | A cor a usar quando o valor do *estado* é `true` . A `color` propriedade é uma cadeia JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255,0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL ("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Cores HTML predefinidas, como amarelo, e azul.</li></ul>| Sim |
| `false` | string | A cor a usar quando o valor do *estado* é `false` . | Sim |

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
