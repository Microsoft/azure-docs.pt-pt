---
title: Esquemas de eventos — Azure Event Grid IoT Edge / Microsoft Docs
description: Esquemas de eventos em Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171572"
---
# <a name="event-schemas"></a>Esquemas de eventos

O módulo Event Grid aceita e entrega eventos em formato JSON. Existem atualmente três esquemas que são suportados pela Grade de Eventos: -

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Pode configurar o esquema a que um editor deve estar em conformidade durante a criação de tópicos. Se não for especificado, é padrão para **EventGridSchema**. Eventos que não se conformam com o esquema esperado serão rejeitados.

Os subscritores também podem configurar o esquema em que pretendem que os eventos sejam entregues. Se não for especificado, o padrão é o esquema do tópico.
Atualmente, o esquema de entrega de assinantes tem de corresponder ao esquema de entrada do seu tópico. 

## <a name="eventgrid-schema"></a>Esquema eventGrid

O esquema eventGrid consiste num conjunto de propriedades necessárias às quais uma entidade editorial deve estar em conformidade. Cada editor tem que preencher os campos de alto nível.

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

### <a name="eventgrid-schema-properties"></a>Propriedades de esquema EventGrid

Todos os eventos têm os seguintes dados de alto nível:

| Propriedade | Tipo | Necessário | Descrição |
| -------- | ---- | ----------- |-----------
| tópico | cadeia (de carateres) | No | Deve coincidir com o tópico em que é publicado. O Event Grid povoa-o com o nome do tópico sobre o qual é publicado se não for especificado. |
| Assunto | string | Sim | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Sim | Tipo de evento para esta fonte de evento, por exemplo, BlobCreated. |
| eventTime | string | Sim | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | cadeia (de carateres) | No | Identificador único para o evento. |
| dados | objeto | Não | Usado para capturar dados de eventos específicos da entidade editorial. |
| dataVersion | string | Sim | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | cadeia (de carateres) | No | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

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

## <a name="customevent-schema"></a>Esquema de evento personalizado

No esquema personalizado, não existem propriedades obrigatórias que sejam aplicadas como o esquema EventGrid. A entidade editorial pode controlar totalmente o esquema do evento. Proporciona a máxima flexibilidade e permite cenários em que você tem um sistema baseado em eventos já em vigor e gostaria de reutilizar eventos existentes e/ou não quer ser ligado a um esquema específico.

### <a name="custom-schema-properties"></a>Propriedades de esquema personalizado

Sem propriedades obrigatórias. Cabe à entidade editorial determinar a carga útil.

### <a name="example--custom-schema-event"></a>Exemplo — Evento De Schema Personalizado

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

Além dos esquemas acima referidos, a Event Grid suporta de forma nativa eventos no [esquema do CloudEvents JSON.](https://github.com/cloudevents/spec/blob/master/json-format.md) CloudEvents é uma especificação aberta para descrever dados de eventos. Simplifica a interoperabilidade, fornecendo um esquema comum de eventos para a publicação e consumo de eventos. Faz parte do [CNCF](https://www.cncf.io/) e a versão atualmente disponível é 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Propriedades de esquema CloudEvent

Consulte a [especificação CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) sobre as propriedades obrigatórias do envelope.

### <a name="example--cloud-event"></a>Exemplo — evento na nuvem
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
