---
title: Filtragem de eventos para a grade de eventos do Azure
description: Descreve como filtrar eventos ao criar uma assinatura da grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390185"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Entender a filtragem de eventos para assinaturas de grade de eventos

Este artigo descreve as diferentes maneiras de filtrar quais eventos são enviados para o ponto de extremidade. Ao criar uma assinatura de evento, você tem três opções de filtragem:

* Tipos de eventos
* O assunto começa com ou termina com
* Campos e operadores avançados

## <a name="event-type-filtering"></a>Filtragem de tipo de evento

Por padrão, todos os [tipos de evento](event-schema.md) para a origem do evento são enviados para o ponto de extremidade. Você pode optar por enviar apenas determinados tipos de evento para o ponto de extremidade. Por exemplo, você pode ser notificado sobre atualizações para seus recursos, mas não notificado para outras operações como exclusões. Nesse caso, filtre pelo tipo de `Microsoft.Resources.ResourceWriteSuccess` evento. Forneça uma matriz com os tipos de evento ou especifique `All` para obter todos os tipos de evento para a origem do evento.

A sintaxe JSON para filtragem por tipo de evento é:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtragem de assunto

Para filtragem simples por assunto, especifique um valor inicial ou final para o assunto. Por exemplo, você pode especificar que o assunto termine `.txt` com para obter apenas eventos relacionados ao carregamento de um arquivo de texto na conta de armazenamento. Ou, você pode filtrar a entidade começa com `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contêiner, mas não para outros contêineres na conta de armazenamento.

Ao publicar eventos em tópicos personalizados, crie assuntos para seus eventos, o que torna mais fácil para os assinantes saber se eles estão interessados no evento. Os assinantes usam a propriedade Subject para filtrar e rotear eventos. Considere adicionar o caminho para onde o evento ocorreu, para que os assinantes possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtrem eventos de forma estreita ou ampla. Se você fornecer um caminho de três segmentos `/A/B/C` como no assunto, os assinantes poderão filtrar pelo primeiro `/A` segmento para obter um amplo conjunto de eventos. Esses assinantes recebem eventos com assuntos `/A/B/C` como `/A/D/E`ou. Outros assinantes podem filtrar `/A/B` por para obter um conjunto mais estreito de eventos.

A sintaxe JSON para filtragem por assunto é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, use a opção de filtragem avançada. Na filtragem avançada, você especifica o:

* tipo de operador-o tipo de comparação.
* chave – o campo nos dados de evento que você está usando para filtragem. Pode ser um número, booliano ou cadeia de caracteres.
* valor ou valores-o valor ou valores para comparar com a chave.

Se você especificar um único filtro com vários valores, uma operação **ou** será executada, portanto, o valor do campo de chave deverá ser um desses valores. Segue-se um exemplo:

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

Se você especificar vários filtros diferentes, uma operação and será executada, portanto, cada condição **de** filtro deverá ser atendida. Segue-se um exemplo: 

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

### <a name="operator"></a>Operator

Os operadores disponíveis para números são:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* Em
* NumberNotIn

O operador disponível para boolianos é: BoolEquals

Os operadores disponíveis para cadeias de caracteres são:

* StringContains
* StringBeginsWith
* StringEndsWith
* Cadeia de caracteres
* StringNotIn

Todas as comparações de cadeia de caracteres são case-insensitve.

### <a name="key"></a>Chave

Para eventos no esquema da grade de eventos, use os seguintes valores para a chave:

* id
* Tópico
* Subject
* EventType
* DataVersion
* Dados de evento (como data. key1)

Para eventos no esquema de eventos de nuvem, use os seguintes valores para a chave:

* EventId
* Source
* EventType
* EventTypeVersion
* Dados de evento (como data. key1)

Para o esquema de entrada personalizado, use os campos de dados de evento (como data. key1).

### <a name="values"></a>Valores

Os valores podem ser:

* number
* Cadeia de caracteres
* boolean
* array

### <a name="limitations"></a>Limitações

A filtragem avançada tem as seguintes limitações:

* Cinco filtros avançados por assinatura de grade de eventos
* 512 caracteres por valor de cadeia de caracteres
* Cinco valores para os operadores **in** e **Not in**

A mesma chave pode ser usada em mais de um filtro.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como filtrar eventos com o PowerShell e CLI do Azure, consulte [filtrar eventos para a grade de eventos](how-to-filter-events.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
