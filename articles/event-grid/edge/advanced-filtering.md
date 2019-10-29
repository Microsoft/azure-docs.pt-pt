---
title: Filtragem avançada – grade de eventos do Azure IoT Edge | Microsoft Docs
description: Filtragem avançada na grade de eventos em IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992564"
---
# <a name="advanced-filtering"></a>Filtragem avançada
A grade de eventos permite especificar filtros em qualquer propriedade no conteúdo JSON. Esses filtros são modelados como conjunto de condições de `AND`, sendo que cada condição externa tem condições de `OR` internas opcionais. Para cada condição de `AND`, especifique os seguintes valores:

* `OperatorType`-o tipo de comparação.
* `Key`-o caminho JSON para a propriedade na qual aplicar o filtro.
* `Value`-o valor de referência no qual o filtro é executado (ou) `Values`-o conjunto de valores de referência no qual o filtro é executado.

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

## <a name="filtering-on-array-values"></a>Filtrando em valores de matriz

A grade de eventos não dá suporte à filtragem em uma matriz de valores atualmente. Se um evento de entrada tiver um valor de matriz para a chave do filtro avançado, a operação de correspondência falhará. O evento de entrada termina não correspondendo à assinatura do evento.

## <a name="and-or-not-semantics"></a>E-ou-não semântica

Observe que no exemplo de JSON fornecido anteriormente, `AdvancedFilters` é uma matriz. Imagine cada elemento de matriz de `AdvancedFilter` como uma condição de `AND`.

Para os operadores que dão suporte a vários valores (como `NumberIn`, `NumberNotIn`, `StringIn`, etc.), cada valor é tratado como uma condição de `OR`. Portanto, um `StringBeginsWith("a", "b", "c")` corresponderá a qualquer valor de cadeia de caracteres que comece com `a` ou `b` ou `c`.

> [!CAUTION]
> Os operadores NOT-`NumberNotIn` e `StringNotIn` se comportam como e condições em cada valor fornecido no campo `Values`.
>
> Não fazer isso fará com que o filtro seja aceito – todos os filtros e derrotar a finalidade da filtragem.

## <a name="floating-point-rounding-behavior"></a>Comportamento de arredondamento de ponto flutuante

A grade de eventos usa o tipo `decimal` .NET para lidar com todos os valores numéricos. Os valores numéricos especificados no JSON de assinatura de evento não estão sujeitos ao comportamento de arredondamento de ponto flutuante.

## <a name="case-sensitivity-of-string-filters"></a>Distinção entre maiúsculas e minúsculas de filtros de cadeia

Todas as comparações de cadeia de caracteres não diferenciam maiúsculas de minúsculas. Não há como alterar esse comportamento hoje mesmo.

## <a name="allowed-advanced-filter-keys"></a>Chaves de filtro avançado permitidas

A propriedade `Key` pode ser uma propriedade de nível superior conhecida, ou ser um caminho JSON com vários pontos, em que cada ponto representa a depuração em um objeto JSON aninhado.

A grade de eventos não tem nenhum significado especial para o `$` caractere na chave, diferentemente da especificação JSONPath.

### <a name="event-grid-schema"></a>Esquema de grade de eventos

Para eventos no esquema da grade de eventos:

* ID
* Tópico
* Requerente
* EventType
* versão de
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Esquema de evento personalizado

Não há nenhuma restrição sobre o `Key` no esquema de evento personalizado, pois a grade de eventos não impõe nenhum esquema de envelope na carga.

## <a name="numeric-single-value-filter-examples"></a>Exemplos numéricos de filtro de valor único

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
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

## <a name="numeric-range-value-filter-examples"></a>Exemplos de filtro de valor de intervalo numérico

* Em
* NumberNotIn

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

## <a name="string-range-value-filter-examples"></a>Exemplos de filtro de valor de intervalo de cadeia de caracteres

* StringContains
* StringBeginsWith
* StringEndsWith
* Cadeia de caracteres
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

## <a name="boolean-single-value-filter-examples"></a>Exemplos de filtro de valor único booliano

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
