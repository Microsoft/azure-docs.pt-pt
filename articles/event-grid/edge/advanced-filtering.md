---
title: Filtragem avançada - Azure Event Grid IoT Edge / Microsoft Docs
description: Filtragem avançada em Grade de Eventos na Borda IoT.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001053"
---
# <a name="advanced-filtering"></a>Filtragem avançada
A Grade de Eventos permite especificar filtros em qualquer propriedade na carga útil json. Estes filtros são modelados como conjunto de `AND` condições, com cada condição exterior com condições interiores `OR` opcionais. Para cada `AND` condição, especifique os seguintes valores:

* `OperatorType` - O tipo de comparação.
* `Key` - O caminho json para a propriedade para aplicar o filtro.
* `Value` - O valor de referência contra o qual o filtro é executado (ou) `Values` - O conjunto de valores de referência contra os quais o filtro é executado.

## <a name="json-syntax"></a>Sintaxe JSON

A sintaxe JSON para um filtro avançado é a seguinte:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtragem nos valores da matriz

A Grade de Eventos não suporta a filtragem de uma série de valores hoje em dia. Se um evento de entrada tiver um valor de matriz para a chave do filtro avançado, a operação de correspondência falha. O evento de entrada acaba por não corresponder à subscrição do evento.

## <a name="and-or-not-semantics"></a>E-OR-NÃO semântica

Note que no exemplo json dado anteriormente, `AdvancedFilters` é uma matriz. Pense em cada `AdvancedFilter` elemento de matriz como uma `AND` condição.

Para os operadores que suportam múltiplos valores (tais `NumberIn` `NumberNotIn` como, `StringIn` , , etc.), cada valor é tratado como uma `OR` condição. Assim, um `StringBeginsWith("a", "b", "c")` irá corresponder a qualquer valor de corda que comece com ou . `a` `b` `c` .

> [!CAUTION]
> Os operadores NOT - `NumberNotIn` e `StringNotIn` comportam-se como condições e em cada valor dado no `Values` campo.
>
> Não fazê-lo fará do filtro um filtro Accept-All e derrotará o propósito da filtragem.

## <a name="floating-point-rounding-behavior"></a>Comportamento de arredondamento de ponto flutuante

A Grelha de Eventos utiliza o `decimal` tipo .NET para manusear todos os valores numéricos. Os valores de número especificados na subscrição do evento JSON não estão sujeitos a comportamento de arredondamento de ponto flutuante.

## <a name="case-sensitivity-of-string-filters"></a>Sensibilidade ao caso dos filtros de cordas

Todas as comparações de cordas são insensíveis a casos. Não há como mudar este comportamento hoje.

## <a name="allowed-advanced-filter-keys"></a>Chaves de filtro avançadas permitidas

A `Key` propriedade pode ser uma propriedade de alto nível bem conhecida, ou ser um caminho json com vários pontos, onde cada ponto significa entrar em um objeto de json aninhado.

A Grade de Eventos não tem nenhum significado especial para o `$` personagem na Chave, ao contrário da especificação JSONPath.

### <a name="event-grid-schema"></a>Esquema de grelha de evento

Para eventos no esquema da Grelha de Eventos:

* ID
* Tópico
* Assunto
* EventType
* Versão de Dados
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Esquema de evento personalizado

Não há restrições no esquema de `Key` eventos personalizados, uma vez que a Grade de Eventos não impõe qualquer esquema de envelope na carga útil.

## <a name="numeric-single-value-filter-examples"></a>Exemplos de filtro de valor único numérico

* NúmeroGreaterThan
* NúmeroGreaterThanOrEquals
* NúmeroSLessTh
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Exemplos de filtro de valor numérico

* NúmeroIn
* NúmeroNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Exemplos de filtro de valor de gama de cordas

* CordasContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Exemplos de filtro de valor único booleano

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
