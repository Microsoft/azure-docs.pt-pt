---
title: Filtragem de eventos para Azure Event Grid
description: Descreve como filtrar eventos ao criar uma subscrição da Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 837209d4197c271598155776b8d171a705e1f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120097"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Compreender a filtragem do evento para subscrições de Grade de Eventos

Este artigo descreve as diferentes formas de filtrar quais os eventos enviados para o seu ponto final. Ao criar uma subscrição de eventos, tem três opções para filtrar:

* Tipos de evento
* O assunto começa com ou termina com
* Campos e operadores avançados

## <a name="event-type-filtering"></a>Filtragem do tipo de evento

Por predefinição, todos os [tipos de eventos](event-schema.md) para a fonte do evento são enviados para o ponto final. Pode decidir enviar apenas certos tipos de eventos para o seu ponto final. Por exemplo, pode ser notificado das atualizações dos seus recursos, mas não notificado para outras operações, como supressões. Nesse caso, filtre pelo tipo de `Microsoft.Resources.ResourceWriteSuccess` evento. Forneça uma matriz com os tipos de eventos, ou especifique `All` para obter todos os tipos de eventos para a fonte do evento.

A sintaxe JSON para filtragem por tipo de evento é:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtragem do sujeito

Para uma filtragem simples por sujeito, especifique um valor inicial ou final para o sujeito. Por exemplo, pode especificar que o assunto termina apenas com `.txt` eventos relacionados com o upload de um ficheiro de texto para a conta de armazenamento. Ou, pode filtrar o assunto começa com `/blobServices/default/containers/testcontainer` a obter todos os eventos para aquele recipiente, mas não outros recipientes na conta de armazenamento.

Ao publicar eventos para tópicos personalizados, crie temas para os seus eventos que facilitem aos subscritores saber se estão interessados no evento. Os assinantes usam a propriedade do assunto para filtrar e encaminhar eventos. Considere adicionar o caminho para onde o evento aconteceu, para que os subscritores possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtram eventos de forma estreita ou ampla. Se fornecer um caminho de três segmentos como `/A/B/C` no assunto, os subscritores podem filtrar pelo primeiro segmento `/A` para obter um vasto conjunto de eventos. Esses assinantes obtêm eventos com temas como `/A/B/C` ou `/A/D/E` . Outros subscritores podem filtrar `/A/B` para obter um conjunto mais restrito de eventos.

A sintaxe JSON para filtragem por sujeito é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, utilize a opção de filtragem avançada. Na filtragem avançada, especifica::

* tipo de operador - O tipo de comparação.
* tecla - O campo nos dados do evento que está a utilizar para filtragem. Pode ser um número, booleano, ou corda.
* valores - O valor ou valores para comparar com a chave.

Se especificar um único filtro com vários valores, é efetuada uma operação **OR,** pelo que o valor do campo-chave deve ser um destes valores. Segue-se um exemplo:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se especificar vários filtros diferentes, é efetuada uma operação **E,** pelo que cada condição do filtro deve ser satisfeita. Segue-se um exemplo: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operadores

Os operadores disponíveis para **números** são:

* NúmeroGreaterThan
* NúmeroGreaterThanOrEquals
* NúmeroSLessTh
* NumberLessThanOrEquals
* NúmeroIn
* NúmeroNotIn

O operador disponível para **booleans** é: 
- BoolEquals

Os operadores disponíveis para **cordas** são:

* CordasContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Todas as comparações de cordas **não** são sensíveis a casos.

### <a name="key"></a>Chave

Para eventos no esquema da Grelha de Eventos, utilize os seguintes valores para a chave:

* ID
* Tópico
* Assunto
* EventType
* Versão de Dados
* Dados do evento (como Data.key1)

Para eventos no esquema de Eventos em Nuvem, utilize os seguintes valores para a chave:

* EventId
* Origem
* EventType
* EventTypeVersion
* Dados do evento (como Data.key1)

Para esquemas de entrada personalizados, utilize os campos de dados do evento (como o Data.key1).

### <a name="values"></a>Valores

Os valores podem ser:

* número
* string
* boolean
* matriz

### <a name="limitations"></a>Limitações

A filtragem avançada tem as seguintes limitações:

* 5 filtros avançados e 25 valores de filtro em todos os filtros por subscrição da grelha de evento
* 512 caracteres por valor de corda
* Cinco valores para **dentro** e **não nos** operadores
* Chaves com ** `.` (ponto)** caráter neles. Por exemplo: `http://schemas.microsoft.com/claims/authnclassreference` ou `john.doe@contoso.com` . . Atualmente, não há suporte para caracteres de fuga em teclas. 

A mesma chave pode ser utilizada em mais de um filtro.

### <a name="examples"></a>Exemplos

### <a name="stringcontains"></a>CordasContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NúmeroIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NúmeroNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NúmeroSLessTh

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NúmeroGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NúmeroGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Passos seguintes

* Para conhecer os eventos de filtragem com PowerShell e Azure CLI, consulte [os eventos filter para a Grade de Eventos.](how-to-filter-events.md)
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
