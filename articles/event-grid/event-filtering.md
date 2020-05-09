---
title: Filtragem de eventos para grelha de eventos azure
description: Descreve como filtrar eventos ao criar uma subscrição da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: spelluru
ms.openlocfilehash: 0f503b21d5a7d0fdfbee79354c198775789c0b91
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888784"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Compreender a filtragem de eventos para subscrições da Grelha de Eventos

Este artigo descreve as diferentes formas de filtrar quais os eventos enviados para o seu ponto final. Ao criar uma subscrição de evento, tem três opções para filtrar:

* Tipos de evento
* O assunto começa com ou termina com
* Campos e operadores avançados

## <a name="event-type-filtering"></a>Filtragem do tipo de evento

Por predefinição, todos os [tipos de eventos](event-schema.md) para a fonte do evento são enviados para o ponto final. Pode decidir enviar apenas certos tipos de eventos para o seu ponto final. Por exemplo, pode ser notificado de atualizações aos seus recursos, mas não notificado para outras operações, como supressões. Nesse caso, filtrar `Microsoft.Resources.ResourceWriteSuccess` pelo tipo de evento. Forneça uma matriz com os `All` tipos de eventos, ou especifique para obter todos os tipos de eventos para a fonte do evento.

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

Para uma simples filtragem por sujeito, especifique um valor inicial ou final para o sujeito. Por exemplo, pode especificar que `.txt` o assunto termina apenas com eventos relacionados com o upload de um ficheiro de texto para a conta de armazenamento. Ou, pode filtrar o `/blobServices/default/containers/testcontainer` sujeito começa por obter todos os eventos para esse contentor, mas não outros contentores na conta de armazenamento.

Ao publicar eventos para tópicos personalizados, crie temas para os seus eventos que facilitem aos assinantes saber se estão interessados no evento. Os assinantes usam a propriedade do sujeito para filtrar e route events. Considere adicionar o caminho para onde o evento aconteceu, para que os assinantes possam filtrar por segmentos desse caminho. O caminho permite aos assinantes filtrar eventos de forma estreita ou ampla. Se fornecer um caminho `/A/B/C` de três segmentos como no assunto, os assinantes podem filtrar pelo primeiro segmento `/A` para obter um vasto conjunto de eventos. Esses assinantes recebem eventos `/A/B/C` `/A/D/E`com temas como ou . Outros subscritores podem `/A/B` filtrar para obter um conjunto mais restrito de eventos.

A sintaxe JSON para filtragem por objeto é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, utilize a opção de filtragem avançada. Em filtragem avançada, especifica o:

* tipo de operador - O tipo de comparação.
* chave - Os dados do campo no caso que está a usar para filtrar. Pode ser um número, booleano, ou corda.
* valores - O valor ou valores a comparar com a chave.

Se especificar um único filtro com múltiplos valores, é executada uma operação **DE,** pelo que o valor do campo-chave deve ser um desses valores. Segue-se um exemplo:

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

Se especificar vários filtros diferentes, é executada uma operação **e operação E,** pelo que cada condição do filtro deve ser satisfeita. Segue-se um exemplo: 

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

* Número maiorthan
* NumberGreaterThanOrEquals
* número inútil
* NumberlessThanOrEquals
* Númeroin
* Notina número

O operador disponível para **booleans** é: 
- BoolEquals

Os operadores disponíveis para **cordas** são:

* StringContains
* StringBeginsWith
* StringEndsWith
* Corda
* Nottina stringnotin

Todas as comparações de cordas **não** são sensíveis ao caso.

### <a name="key"></a>Chave

Para eventos no esquema da Grelha de Eventos, utilize os seguintes valores para a chave:

* ID
* Tópico
* Assunto
* Tipo de evento
* Versão de Dados
* Dados do evento (como Data.key1)

Para eventos em Cloud Events schema, use os seguintes valores para a chave:

* Eventid
* Origem
* Tipo de evento
* EventTypeVersion
* Dados do evento (como Data.key1)

Para obter esquemas de entrada personalizados, utilize os campos de dados do evento (como Data.key1).

### <a name="values"></a>Valores

Os valores podem ser:

* número
* string
* boolean
* array

### <a name="limitations"></a>Limitações

A filtragem avançada tem as seguintes limitações:

* Cinco filtros avançados por subscrição da grelha de eventos
* 512 caracteres por valor de cadeia
* Cinco valores para **dentro** e **não para operadores**
* Chaves com ** `.` (ponto)** caráter neles. Por `http://schemas.microsoft.com/claims/authnclassreference` exemplo: `john.doe@contoso.com`ou . Atualmente, não há suporte para caracteres de fuga em teclas. 

A mesma tecla pode ser utilizada em mais de um filtro.

### <a name="examples"></a>Exemplos

### <a name="stringcontains"></a>StringContains

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

### <a name="stringin"></a>Corda

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

### <a name="stringnotin"></a>Nottina stringnotin

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

### <a name="numberin"></a>Númeroin

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

### <a name="numbernotin"></a>Notina número

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

### <a name="numberlessthan"></a>número inútil

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>Número maiorthan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberlessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

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

* Para aprender sobre a filtragem de eventos com powerShell e Azure CLI, consulte [os eventos de filtro para a Grelha de Eventos](how-to-filter-events.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
