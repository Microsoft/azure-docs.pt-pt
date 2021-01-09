---
title: Interpretar dados de eventos
titleSuffix: Azure Digital Twins
description: Veja como interpretar diferentes tipos de eventos e as suas diferentes mensagens de notificação.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f2b971eae5d37e8fb0771e213075289af6c519
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045262"
---
# <a name="understand-event-data"></a>Compreender os dados do evento

Diferentes eventos em Azure Digital Twins produzem notificações , que permitem que a solução recue quando estão a acontecer **diferentes** ações. Estes são então [encaminhados](concepts-route-events.md) para diferentes locais dentro e fora da Azure Digital Twins que podem usar esta informação para tomar medidas.

Existem vários tipos de notificações que podem ser geradas, e as mensagens de notificação podem parecer diferentes dependendo do tipo de evento que as gerou. Este artigo dá detalhes sobre diferentes tipos de mensagens, e como podem ser.

Este gráfico mostra os diferentes tipos de notificação:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

Em geral, as notificações são compostas por duas partes: o cabeçalho e o corpo. 

### <a name="event-notification-headers"></a>Cabeçalhos de notificação de eventos

Os cabeçalhos de mensagens de notificação são representados com pares de valor-chave. Dependendo do protocolo utilizado (MQTT, AMQP ou HTTP), os cabeçalhos de mensagens serão serializados de forma diferente. Esta secção discute informações gerais de cabeçalho para mensagens de notificação, independentemente do protocolo específico e da serialização escolhidas.

Algumas notificações estão em conformidade com a norma [CloudEvents.](https://cloudevents.io/) A conformidade com o CloudEvents é a seguinte.
* As notificações emitidas pelos dispositivos continuam a seguir as especificações existentes para notificações
* As notificações processadas e emitidas pelo IoT Hub continuam a seguir as especificações existentes para notificação, exceto quando o IoT Hub opta por apoiar os Eventos CloudEvents, como através da Grade de Eventos
* Notificações emitidas por [gémeos digitais](concepts-twins-graph.md) com [um modelo](concepts-models.md) em conformidade com CloudEvents
* Notificações processadas e emitidas pela Azure Digital Twins estão em conformidade com cloudEvents

Os serviços têm de adicionar um número de sequência em todas as notificações para indicar a sua encomenda, ou manter a sua própria encomenda de outra forma. 

As notificações emitidas pela Azure Digital Twins para a Event Grid serão automaticamente formatadas para o esquema cloudEvents ou para o esquema EventGridEvent, dependendo do tipo de esquema definido no tópico da grelha de eventos. 

Os atributos de extensão nos cabeçalhos serão adicionados como propriedades no esquema da Grelha de Eventos dentro da carga útil. 

### <a name="event-notification-bodies"></a>Organismos de notificação de eventos

Os corpos das mensagens de notificação são descritos aqui no JSON. Dependendo da serialização desejada para o corpo da mensagem (como com JSON, CBOR, Protobuf, etc.), o corpo da mensagem pode ser serializado de forma diferente.

O conjunto de campos que o corpo contém varia com diferentes tipos de notificação. Aqui estão dois corpos de mensagem de amostra, para ter uma ideia de como geralmente são e podem incluir.

Mensagem de telemetria:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Mensagem de notificação do ciclo de vida:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>Detalhe de formato de mensagem para diferentes tipos de eventos

Esta secção entra em mais detalhes sobre os diferentes tipos de notificações emitidas pela IoT Hub e pela Azure Digital Twins (ou outros serviços Azure IoT). Você vai ler sobre as coisas que desencadeiam cada tipo de notificação, e o conjunto de campos incluídos com cada tipo de organismo de notificação.

### <a name="digital-twin-life-cycle-notifications"></a>Notificações digitais do ciclo de vida gémeos

Todos os [gémeos digitais](concepts-twins-graph.md) emitem notificações, independentemente de representarem ou não [dispositivos IoT Hub em Azure Digital Twins.](how-to-ingest-iot-hub-data.md) Isto **deve-se** às notificações do ciclo de vida , que são sobre o próprio gémeo digital.

As notificações do ciclo de vida são desencadeadas quando:
* Um gémeo digital é criado
* Um gémeo digital é eliminado

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de ciclo de vida.

| Nome | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um balcão mantido pelo serviço. `source` + `id` é único para cada evento distinto. |
| `source` | Nome do hub IoT ou exemplo de Azure Digital Twins, como *myhub.azure-devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>A mensagem está em conformidade com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID do gémeo digital |
| `time` | Tempo de tempo para quando a operação ocorreu no gémeo |
| `traceparent` | Um contexto de traço W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O corpo é o gémeo digital afetado, representado em formato JSON. O esquema para isto é *Digital Twins Resource 7.1*.

Para eventos de criação, a carga útil reflete o estado do gémeo após a criação do recurso, pelo que deve incluir todos os elementos gerados pelo sistema como uma `GET` chamada.

Aqui está um exemplo de um corpo para um dispositivo [IoT Plug and Play (PnP),](../iot-pnp/overview-iot-plug-and-play.md) com componentes e sem propriedades de alto nível. Devem ser omitidas propriedades que não façam sentido para dispositivos (como propriedades reportadas).

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Aqui está outro exemplo de um gémeo digital. Este baseia-se num [modelo](concepts-models.md), e não suporta componentes:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Notificações de mudança de relacionamento digital

**As notificações de mudança de relacionamento** são desencadeadas quando qualquer relação de um gémeo digital é criada, atualizada ou eliminada. 

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de mudança de borda.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um balcão mantido pelo serviço. `source` + `id` é único para cada evento distinto |
| `source` | Nome do exemplo das Gémeas Digitais Azure, como *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>A mensagem está em conformidade com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | ID da relação, como `<twinID>/relationships/<relationshipID>` |
| `time` | Tempo de tempo para quando a operação ocorreu na relação |
| `traceparent` | Um contexto de traço W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O corpo é a carga útil de uma relação, também em formato JSON. Utiliza o mesmo formato que um `GET` pedido de relacionamento através da [API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

"Atualizar uma relação" significa que as propriedades da relação mudaram. 

Aqui está um exemplo de uma notificação de relacionamento de atualização para atualizar uma propriedade:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Pois, `Relationship.Delete` o corpo é o mesmo que o `GET` pedido, e recebe o último estado antes da eliminação.

Aqui está um exemplo de uma notificação de relacionamento de criar ou excluir:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>Notificações digitais de mudança de gémeos

**As notificações digitais de mudança dupla** são desencadeadas quando um gémeo digital está a ser atualizado, como:
* Quando os valores de propriedade ou metadados mudam.
* Quando os metadados digitais de gémeos ou componentes mudam. Um exemplo deste cenário é mudar o modelo de um gémeo digital.

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação digital de mudança de gémeos.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um balcão mantido pelo serviço. `source` + `id` é único para cada evento distinto |
| `source` | Nome do hub IoT ou exemplo de Azure Digital Twins, como *myhub.azure-devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1.0*<br>A mensagem está em conformidade com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID do gémeo digital |
| `time` | Timetamp para quando a operação ocorreu no gémeo digital |
| `traceparent` | Um contexto de traço W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O organismo para a `Twin.Update` notificação é um documento do JSON Patch que contém a atualização para o gémeo digital.

Por exemplo, digamos que um gémeo digital foi atualizado usando o seguinte patch.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

A notificação correspondente (se executada sincronizadamente pelo serviço, como a Azure Digital Twins atualizando um gémeo digital) teria um corpo como:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>Passos seguintes

Veja como criar pontos finais e rotas para entregar eventos:
* [*Como fazer: Gerir pontos finais e rotas*](how-to-manage-routes-apis-cli.md)

Ou, saiba mais sobre as opções Azure Digital Twins APIs e SDK:
* [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md)