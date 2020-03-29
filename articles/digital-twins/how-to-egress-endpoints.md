---
title: Egress e pontos finais - Azure Digital Twins Microsoft Docs
description: Aprenda a criar e a obter pontos finais de eventos em Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511574"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Egress e pontos finais em Gémeos Digitais Azure

Os *pontos finais* da Azure Digital Twins representam uma mensagem ou corretor de eventos dentro da subscrição do Azure de um utilizador. Eventos e mensagens podem ser enviados para os tópicos Azure Event Hubs, Azure Event Grid e Azure Service Bus.

Os eventos são encaminhados para pontos finais de acordo com as preferências predefinidas de encaminhamento. Os utilizadores especificam quais os tipos de *eventos* que cada ponto final pode receber.

Para saber mais sobre eventos, encaminhamento e tipos de eventos, consulte [eventos e mensagens de encaminhamento em Gémeos Digitais Azure](./concepts-events-routing.md).

## <a name="events"></a>Eventos

Os eventos são enviados por objetos IoT (como dispositivos e sensores) para processamento por mensagens Azure e corretores de eventos. Os eventos são definidos pela seguinte referência de esquema de [evento sinuoso do evento Azure Event Grid](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| ID | string | Identificador único para o evento. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| data | objeto | Dados do evento específicos do fornecedor de recursos. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é reelegível. O Event Grid fornece este valor. |

Para mais informações sobre o event grid schema:

- Reveja a [referência schema](../event-grid/event-schema.md)do evento Azure Event Grid .
- Leia a [referência azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Tipos de evento

Os tipos de eventos classificam a natureza do evento e são definidos no campo **eventType.** Os tipos de eventos disponíveis são dados pela seguinte lista:

- Operação Topologia
- UdfCustom
- SensorChange
- SpaceChange
- Mensagem de dispositivo

Os formatos de evento para cada tipo de evento são descritos nas seguintes subsecções.

### <a name="topologyoperation"></a>Operação Topologia

**TopologiaOperação** aplica-se a alterações de gráfico. A propriedade **do sujeito** especifica o tipo de objeto afetado. Os seguintes tipos de objetos podem desencadear este evento:

- Dispositivo
- DeviceBlobMetadata
- DispositivoExtendedProperty
- ExtendedPropertyKey
- Tipo alargado
- KeyStore
- Relatório
- Definição de papéis
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Espaço
- SpaceBlobMetadata
- SpaceExtendedProperty
- Recurso Espacial
- Atribuição de Funções Espaciais
- Sistema
- Utilizador
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** é um evento enviado por uma função definida pelo utilizador (UDF).
  
> [!IMPORTANT]  
> Este evento deve ser explicitamente enviado da própria UDF.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="sensorchange"></a>SensorChange

**SensorChange** é uma atualização do estado de um sensor com base em alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** é uma atualização para o estado de um espaço com base em alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="devicemessage"></a>Mensagem de dispositivo

Ao utilizar o **DeviceMessage,** pode especificar uma ligação **EventHub** à qual os eventos de telemetria crus podem ser encaminhados também a partir de Gémeos Digitais Azure.

> [!NOTE]
> - **O DeviceMessage** só é combinável com **eventHub**. Não é possível combinar o **DeviceMessage** com nenhum dos outros tipos de eventos.
> - Pode especificar apenas um ponto final da combinação do tipo **EventHub** ou **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurar pontos finais

A gestão do endpoint é exercida através da API Endpoints.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Os seguintes exemplos demonstram como configurar os pontos finais suportados.

>[!IMPORTANT]
> Preste atenção ao **eventoTipos** atributos. Define quais os tipos de eventos que são tratados pelo ponto final e, assim, determina o seu encaminhamento.

Um pedido http post autenticado contra:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Rota para eventos de ônibus de serviço tipo **SensorChange,** **SpaceChange**, e **TopologiaOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O espaço de nome do seu ponto final |
    | YOUR_PRIMARY_KEY | A cadeia de ligação primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A corda de ligação secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rota para eventos de grelha de eventos tipo **SensorChange,** **SpaceChange**e **TopologiaOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_PRIMARY_KEY | A cadeia de ligação primária usada para autenticar|
    | YOUR_SECONDARY_KEY | A corda de ligação secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rota para eventos hubs tipo **sensorChange,** **SpaceChange**e **TopologiaOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O espaço de nome do seu ponto final |
    | YOUR_PRIMARY_KEY | A cadeia de ligação primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A corda de ligação secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do seu centro de eventos |

- Rota para eventos do tipo evento **DispositivoMessage**. A inclusão `EntityPath` na **conexãoString** é obrigatória:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O espaço de nome do seu ponto final |
    | YOUR_PRIMARY_KEY | A cadeia de ligação primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A corda de ligação secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do seu centro de eventos |

> [!NOTE]  
> Após a criação de um novo ponto final, pode levar até 5 a 10 minutos para começar a receber eventos no ponto final.

## <a name="primary-and-secondary-connection-keys"></a>Chaves de ligação primária e secundária

Quando uma chave de ligação primária se torna não autorizada, o sistema tenta automaticamente a chave de ligação secundária. Isto fornece uma cópia de segurança e permite a possibilidade de autenticar graciosamente e atualizar a chave primária através da API Endpoints.

Se as teclas de ligação primária e secundária não forem autorizadas, o sistema entra num tempo exponencial de espera de back-off de até 30 minutos. Os acontecimentos são deixados em cada tempo de espera desencadeado.

Sempre que o sistema estiver em estado de espera, atualizar as teclas de ligação através da API endpoints pode demorar até 30 minutos a fazer efeito.

## <a name="unreachable-endpoints"></a>Pontos finais inacessíveis

Quando um ponto final se torna inacessível, o sistema entra num tempo exponencial de espera de até 30 minutos. Os acontecimentos são deixados em cada tempo de espera desencadeado.

## <a name="next-steps"></a>Passos seguintes

- Aprenda [a usar as Gémeas Digitais Azure Swagger.](how-to-use-swagger.md)

- Saiba mais sobre [eventos de encaminhamento e mensagens](concepts-events-routing.md) em Azure Digital Twins.
