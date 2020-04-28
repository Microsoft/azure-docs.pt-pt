---
title: Eventos schemas — Azure Event Grid IoT Edge [ Microsoft Docs
description: Schemas de eventos em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73242462"
---
# <a name="event-schemas"></a>Esquemas de eventos

O módulo Event Grid aceita e entrega eventos em formato JSON. Existem atualmente três esquemas que são apoiados pela Event Grid: -

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Pode configurar o esquema a que uma editora deve estar em conformidade durante a criação de tópicos. Se não especificado, não se aplica ao **EventGridSchema**. Eventos que não estejam em conformidade com o esquema esperado serão rejeitados.

Os assinantes também podem configurar o esquema no qual querem que os eventos sejam entregues. Se não especificado, o padrão é o esquema do tópico.
Atualmente, o esquema de entrega de assinantes tem de corresponder ao esquema de entrada do seu tópico. 

## <a name="eventgrid-schema"></a>Esquema EventGrid

EventGrid schema consiste num conjunto de propriedades necessárias às qual uma entidade editorial deve estar em conformidade. Cada editora tem de povoar os campos de alto nível.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Propriedades de esquemas EventGrid

Todos os eventos têm os seguintes dados de alto nível:

| Propriedade | Tipo | Necessário | Descrição |
| -------- | ---- | ----------- |-----------
| tópico | string | Não | Deve coincidir com o tópico em que é publicado. Event Grid povoa-o com o nome do tópico em que é publicado se não especificado. |
| Assunto | string | Sim | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Sim | Tipo de evento para esta fonte de evento, por exemplo, BlobCreated. |
| eventTime | string | Sim | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Não | Identificador único para o evento. |
| data | objeto | Não | Usado para capturar dados de eventos específicos para a entidade editorial. |
| dataVersion | string | Sim | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | Não | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

### <a name="example--eventgrid-schema-event"></a>Exemplo — EventoGrid schema event

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Esquema customEvent

Em esquemas personalizados, não existem propriedades obrigatórias que sejam aplicadas como o esquema EventGrid. A entidade editorial pode controlar totalmente o esquema do evento. Proporciona a máxima flexibilidade e permite cenários em que já existe um sistema baseado em eventos e gostaria de reutilizar os eventos existentes e/ou não querer ser ligado a um esquema específico.

### <a name="custom-schema-properties"></a>Propriedades de esquemas personalizados

Sem propriedades obrigatórias. Cabe à entidade editorial determinar a carga.

### <a name="example--custom-schema-event"></a>Exemplo — Evento Schema Personalizado

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Esquema cloudEvent

Além dos schemas acima, event grid apoia nativamente eventos no [esquema CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents é uma especificação aberta para descrever dados do evento. Simplifica a interoperabilidade fornecendo um esquema comum de eventos para a publicação e consumo de eventos. Faz parte do [CNCF](https://www.cncf.io/) e a versão atualmente disponível é de 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Propriedades de esquema sinuoso CloudEvent

Consulte a [especificação CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) sobre as propriedades obrigatórias do envelope.

### <a name="example--cloud-event"></a>Exemplo - evento em nuvem
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
