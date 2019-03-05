---
title: Entidades previamente concebidas DatetimeV2
titleSuffix: Azure
description: Este artigo tem datetimeV2 informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 6b4c3f7445d18ab1548fd63b1f4d12c5901cf949
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339526"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 entidade criados previamente para uma aplicação LUIS

O **datetimeV2** entidade pré-criados extrai valores de data e hora. Resolva estes valores num formato padronizado para programas de cliente consumir. Quando uma expressão tem uma data ou hora que não esteja concluída, o LUIS inclui _passado e valores futuras_ na resposta do ponto final. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém datetimeV2 para os objetivos do aplicativo. 

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerido a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repositório do GitHub

## <a name="example-json"></a>JSON de exemplo 
O exemplo seguinte resposta JSON tem um `datetimeV2` entidade com um subtipo de `datetime`. Para obter exemplos de outros tipos de entidades de datetimeV2, consulte [subtipos dos datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Descrições das propriedades JSON

|Nome da propriedade |Tipo de propriedade e descrição|
|---|---|
|Entidade|**cadeia de caracteres** -textos extraídos de expressão com o tipo de data, hora, intervalo de datas ou intervalo de tempo.|
|tipo|**cadeia de caracteres** - um do [subtipos dos datetimeV2](#subtypes-of-datetimev2)
|startIndex|**Int** -o índice na expressão em que começa a entidade.|
|endIndex|**Int** -o índice na expressão em que termina a entidade.|
|resolução|Tem um `values` matriz que tenha um, dois ou quatro [valores da resolução](#values-of-resolution).|
|end|O valor final de uma hora ou intervalo de datas, o mesmo formato que `value`. Utilizado apenas se `type` é `daterange`, `timerange`, ou `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Subtipos dos datetimeV2

O **datetimeV2** pré-criados de entidade tem os seguintes subtipos e são fornecidos exemplos de cada tabela que se segue:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Valores da resolução
* A matriz tem um elemento se a data ou hora em que a expressão é totalmente especificado e inequívoca.
* A matriz tem dois elementos, se o valor de datetimeV2 é ambíguo. Ambiguidade inclui a falta de um ano específico, hora ou intervalo de tempo. Ver [datas ambíguas](#ambiguous-dates) para obter exemplos. Quando o tempo é ambíguo para horas ou PM, ambos os valores estão incluídos.
* A matriz tem quatro elementos, se a expressão tiver dois elementos com a ambiguidade. Esta ambiguidade inclui elementos com:
  * Uma data ou um intervalo de datas é ambíguo como ano
  * Um tempo ou intervalo de tempo é ambíguo quanto às horas ou tarde Por exemplo, 3:00, 3 de Abril.

Cada elemento do `values` matriz pode ter os seguintes campos: 

|Nome da propriedade|Descrição de propriedade|
|--|--|
|Timex|hora, data ou intervalo de datas expressado no formato TIMEX que se segue a [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos de TIMEX3 usando a linguagem de TimeML de anotação. Esta anotação está descrita com o [diretrizes TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|tipo|O subtipo, que pode ser um dos seguintes itens: datetime, data, hora, daterange, timerange, datetimerange, duração, o conjunto.|
|valor|**Opcional.** Um objeto datetime em yyyy:MM:dd o formato (data), hh: mm: (hora) yyyy:MM:dd hh: mm: (datetime). Se `type` é `duration`, o valor é o número de segundos (duração) <br/> Utilizado apenas se `type` é `datetime` ou `date`, `time`, ou "duração.|

## <a name="valid-date-values"></a>Valores de data válido

O **datetimeV2** suporta datas entre os seguintes intervalos:

| Mín. | Máx. |
|----------|-------------|
| 1º de Janeiro de 1900   | 31 de Dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data pode ser no passado ou futuras, LUIS fornece os dois valores. Um exemplo é uma expressão que inclui o mês e data sem o ano.  

Por exemplo, dada a expressão "2nd de Maio":
* Se a data de hoje é 3 de Maio de 2017, o LUIS fornece "2017-05-02" e "2018-05-02" como valores. 
* Quando a data de hoje é de 1 de Maio de 2017, o LUIS fornece "2016-05-02" e "2017-05-02" como valores.

O exemplo seguinte mostra a resolução da entidade "2nd de Maio". Esta resolução parte do princípio de que a data de hoje é uma data entre 2 de Maio de 2017 e 1 de Maio de 2018.
Campos com `X` no `timex` campo são partes da data que não estão especificados explicitamente na expressão.

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução de intervalo de datas para numéricas de data

O `datetimeV2` entidade extrai os intervalos de data e hora. O `start` e `end` fields, especifique o início e no fim do intervalo. Para a expressão "2nd de Maio para 5 de Maio", LUIS fornece **daterange** valores para o ano atual e o ano seguinte. Na `timex` campo, o `XXXX` valores indicam a ambiguidade do ano. `P3D` indica o período de tempo é longo de três dias.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exemplos de resolução de intervalo de datas para o dia da semana

O exemplo seguinte mostra como utiliza o LUIS **datetimeV2** para resolver a expressão "Terça-feira à quinta-feira". Neste exemplo, a data atual for 19 de Junho. LUIS inclui **daterange** valores para os dois intervalos de datas que são precedidas e siga a data atual.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Tempo ambíguo
A matriz de valores tem dois elementos de tempo se o tempo ou intervalo de tempo é ambíguo. Quando existe um período de tempo ambíguo, valores de ter as duas da manhã e. horas.

## <a name="time-range-resolution-example"></a>Exemplo de resolução de intervalo de tempo

O exemplo seguinte mostra como utiliza o LUIS **datetimeV2** para resolver a expressão que tem um intervalo de tempo.

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="deprecated-prebuilt-datetime"></a>Datetime pré-criados preterido

O `datetime` criados previamente entidade é preterida e substituída por **datetimeV2**. 

Para substituir `datetime` com `datetimeV2` na sua aplicação LUIS, conclua os seguintes passos:

1. Abra o **entidades** painel da interface da web do LUIS. 
2. Eliminar a **datetime** entidade pré-criados.
3. Clique em **adicionar entidade pré-criados**
4. Selecione **datetimeV2** e clique em **guardar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [dimensão](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) entidades, e [número](luis-reference-prebuilt-number.md). 

