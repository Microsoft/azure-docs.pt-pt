---
title: Filtragem avançada - Azure Event Grid IoT Edge [ Microsoft Docs
description: Filtragem avançada em Grelha de Eventos na Borda IoT.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72992564"
---
# <a name="advanced-filtering"></a>Filtragem avançada
A Grelha de Eventos permite especificar filtros em qualquer propriedade da carga útil json. Estes filtros são modelados como conjunto de `AND` condições, com cada condição exterior com condições internas `OR` opcionais. Para `AND` cada condição, especifice os seguintes valores:

* `OperatorType`- O tipo de comparação.
* `Key`- O caminho json para a propriedade em que aplicar o filtro.
* `Value`- O valor de referência contra o `Values` qual o filtro é executado (ou) - O conjunto de valores de referência contra os quais o filtro é executado.

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

## <a name="filtering-on-array-values"></a>Filtragem dos valores da matriz

A Grelha de Eventos não suporta a filtragem de uma série de valores hoje em dia. Se um evento de entrada tiver um valor de matriz para a chave do filtro avançado, a operação de correspondência falha. O evento de entrada acaba por não corresponder à subscrição do evento.

## <a name="and-or-not-semantics"></a>E-OR-NÃO semântica

Note que no exemplo json `AdvancedFilters` dado anteriormente, é uma matriz. Pense em `AdvancedFilter` cada elemento `AND` matriz como uma condição.

Para os operadores que suportam `NumberNotIn` `StringIn`múltiplos valores (tais como, `OR` `NumberIn`etc.), cada valor é tratado como uma condição. Assim, um `StringBeginsWith("a", "b", "c")` irá combinar qualquer valor `a` de `b` `c`cadeia que comece com ou .

> [!CAUTION]
> Os operadores `NumberNotIn` `StringNotIn` DE NÃO - e comportam-se `Values` como condições e condições sobre cada valor dado no terreno.
>
> Não o fazer fará do filtro um filtro Accept-All e derrotará o propósito da filtragem.

## <a name="floating-point-rounding-behavior"></a>Comportamento de arredondamento de ponto flutuante

A Grelha `decimal` de Eventos utiliza o tipo .NET para lidar com todos os valores numéricos. Os valores de número especificados na subscrição do evento JSON não estão sujeitos a comportamento de arredondamento de ponto flutuante.

## <a name="case-sensitivity-of-string-filters"></a>Sensibilidade ao caso dos filtros de cordas

Todas as comparações de cordas são insensíveis ao caso. Não há como mudar este comportamento hoje.

## <a name="allowed-advanced-filter-keys"></a>Chaves avançadas de filtro

A `Key` propriedade pode ser uma propriedade de alto nível bem conhecida, ou ser um caminho json com vários pontos, onde cada ponto significa pisar em um objeto json aninhado.

A Grelha de Eventos não `$` tem nenhum significado especial para o personagem na Chave, ao contrário da especificação JSONPath.

### <a name="event-grid-schema"></a>Esquema da grelha do evento

Para eventos no esquema da Grelha de Eventos:

* ID
* Tópico
* Assunto
* Tipo de evento
* Versão de Dados
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Esquema de evento personalizado

Não há restrições `Key` ao esquema de eventos personalizados, uma vez que a Grid de Eventos não impõe nenhum esquema de envelope na carga útil.

## <a name="numeric-single-value-filter-examples"></a>Exemplos numéricos de filtro de valor único

* Número maiorthan
* NumberGreaterThanOrEquals
* número inútil
* NumberlessThanOrEquals

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

## <a name="numeric-range-value-filter-examples"></a>Exemplos de filtro de valor de gama numérico

* Númeroin
* Notina número

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

* StringContains
* StringBeginsWith
* StringEndsWith
* Corda
* Nottina stringnotin

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

## <a name="boolean-single-value-filter-examples"></a>Boolean exemplos de filtro de valor único

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
