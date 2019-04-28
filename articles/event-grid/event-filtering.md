---
title: Eventos de filtragem para o Azure Event Grid
description: Descreve como filtrar eventos durante a criação de uma subscrição do Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436176"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Compreender o evento de filtragem para subscrições do Event Grid

Este artigo descreve as diferentes formas de filtro que eventos são enviados para o ponto final. Ao criar uma subscrição de evento, tem três opções para filtragem:

* Tipos de eventos
* Assunto começa com ou termina com
* Campos avançados e operadores

## <a name="event-type-filtering"></a>Filtragem de tipo de evento

Por predefinição, todos os [tipos de evento](event-schema.md) a origem do evento são enviadas para o ponto final. Pode optar por enviar apenas determinados tipos de eventos para o ponto final. Por exemplo, pode obter notificado sobre as atualizações para seus recursos, mas não é notificado para outras operações, como as eliminações. Nesse caso, filtrar pelo `Microsoft.Resources.ResourceWriteSuccess` tipo de evento. Forneça uma matriz com os tipos de evento ou especifique `All` para obter todos os tipos de evento para a origem do evento.

A sintaxe JSON para filtrar por tipo de evento é:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtragem de assunto

Simples filtrar pelo requerente, especifique um valor inicial ou final para o assunto. Por exemplo, pode especificar o assunto termina com `.txt` para obter apenas os eventos relacionados com o carregamento de um ficheiro de texto para a conta de armazenamento. Em alternativa, pode filtrar o assunto começa com `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contentor, mas não outros contentores na conta de armazenamento.

Quando publica eventos tópicos personalizados, crie assuntos para seus eventos que tornam fácil para os assinantes para saber se elas têm interesse no evento. Os subscritores utilizar a propriedade de assunto para filtrar e encaminhar eventos. Considere adicionar o caminho para onde o evento ter acontecido, para que os assinantes podem filtrar por segmentos de caminho. O caminho permite que os subscritores de foco menor ou amplamente filtrar eventos. Se fornecer um caminho de três segmento como `/A/B/C` no assunto, os assinantes podem filtrar pelo primeiro segmento `/A` para obter um amplo conjunto de eventos. Os subscritores recebem eventos com os assuntos como `/A/B/C` ou `/A/D/E`. Outros subscritores podem filtrar por `/A/B` para obter um conjunto mais estreito de eventos.

A sintaxe JSON para filtrar por tipo de evento é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, utilize a opção de filtragem avançada. Na filtragem avançada, especificar o:

* tipo de operador - o tipo de comparação.
* chave – o campo nos dados de evento que está a utilizar para filtragem. Pode ser um número, booleano ou uma cadeia.
* valor ou valores - o valor ou valores a comparar com a chave.

A sintaxe JSON para a utilização de filtros avançados é:

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operador

Os operadores disponíveis para os números são:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

O operador disponível para booleanos é: BoolEquals

Os operadores disponíveis para cadeias de caracteres são:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Todas as comparações de seqüência de caracteres são insensitve caso.

### <a name="key"></a>Chave

Para eventos no esquema do Event Grid, utilize os seguintes valores para a chave:

* Id
* Tópico
* Subject
* EventType
* DataVersion
* Dados de eventos (como Data.key1)

Para eventos no esquema de eventos na Cloud, utilize os seguintes valores para a chave:

* EventId
* Origem
* EventType
* EventTypeVersion
* Dados de eventos (como Data.key1)

Para o esquema de entrada personalizada, utilize os campos de dados de eventos (como Data.key1).

### <a name="values"></a>Valores

Os valores podem ser:

* número
* string
* boolean
* array

### <a name="limitations"></a>Limitações

Filtragem avançada tem as seguintes limitações:

* Cinco filtros avançados por subscrição do event grid
* 512 carateres por valor de cadeia
* Cinco valores para **no** e **não está no** operadores
* A chave só pode ter um nível de aninhamento (como data.key1)
* Esquemas de eventos personalizados podem ser filtradas apenas em campos de nível superior

A mesma chave pode ser utilizada em mais do que um filtro.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a filtragem de eventos com o PowerShell e CLI do Azure, veja [filtrar eventos do Event Grid](how-to-filter-events.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
