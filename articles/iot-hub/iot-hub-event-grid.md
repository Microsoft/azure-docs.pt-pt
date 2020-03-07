---
title: Azure IoT Hub e Grid de Eventos Microsoft Docs
description: Utilize a Grelha de Eventos Azure para desencadear processos baseados em ações que ocorrem no IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359709"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do IoT Hub usando a Grelha de Eventos para desencadear ações

O Azure IoT Hub integra-se com a Azure Event Grid para que possa enviar notificações de eventos para outros serviços e desencadear processos a jusante. Configure as suas aplicações de negócio para ouvir eventos IoT Hub para que possa reagir a eventos críticos de forma fiável, escalável e segura. Por exemplo, construir uma aplicação que atualize uma base de dados, crie um bilhete de trabalho e entregue uma notificação por e-mail sempre que um novo dispositivo IoT é registado no seu hub IoT.

[A Azure Event Grid](../event-grid/overview.md) é um serviço de encaminhamento de eventos totalmente gerido que utiliza um modelo de subscrição de publicações. A Event Grid tem suporte integrado para serviços Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Azure Logic Apps,](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para serviços não Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que a Event Grid suporta, consulte [Uma introdução à Grelha de Eventos Azure](../event-grid/overview.md).

![Arquitetura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Rede de Eventos está disponível para centros IoT localizados nas regiões onde a Rede de Eventos é apoiada. Para a mais recente lista de regiões, consulte [Uma introdução à Grelha de Eventos Azure.](../event-grid/overview.md)

## <a name="event-types"></a>Tipos de eventos

O IoT Hub publica os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo está registado num hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é apagado de um hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desligado de um hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria do dispositivo é enviada para um hub IoT |

Utilize o portal Azure ou o Azure CLI para configurar quais os eventos a publicar a partir de cada hub IoT. Por exemplo, experimente o tutorial [Enviar notificações por e-mail sobre eventos do Hub Azure IoT usando Aplicações Lógicas](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema de eventos

Os eventos IoT Hub contêm toda a informação necessária para responder às alterações no ciclo de vida do seu dispositivo. Pode identificar um evento IoT Hub verificando se a propriedade eventType começa com **microsoft.Devices**. Para mais informações sobre como utilizar as propriedades do evento Grid, consulte o [event schema](../event-grid/event-schema.md)do evento Event Grid .

### <a name="device-connected-schema"></a>Esquema ligado ao dispositivo

O exemplo que se segue mostra o esquema de um evento ligado ao dispositivo:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Esquema de telemetria do dispositivo

A mensagem de telemetria do dispositivo deve estar num formato JSON válido com o conjunto de conteúdoType para **aplicação/json** e contentEncoding definido para **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens . Ambas as propriedades são insensíveis. Se a codificação de conteúdo não estiver definida, o IoT Hub escreverá as mensagens no formato codificado base 64.

Pode enriquecer eventos de telemetria do dispositivo antes de serem publicados na Event Grid, selecionando o ponto final como Rede de Eventos. Para mais informações, consulte a visão geral dos enriquecimentos de [mensagens.](iot-hub-message-enrichments-overview.md)

O exemplo que se segue mostra o esquema de um evento de telemetria do dispositivo:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Dispositivo criado esquema

O exemplo que se segue mostra o esquema de um evento criado por dispositivos:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Para obter uma descrição detalhada de cada propriedade, consulte o esquema do [evento Azure Event Grid para ioT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As subscrições de eventos IoT Hub podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

A Grelha de Eventos permite [a filtragem](../event-grid/event-filtering.md) de tipos de eventos, assuntos e conteúdo de dados. Ao criar a subscrição da Grelha de Eventos, pode optar por subscrever eventos IoT selecionados. Os filtros de objeto em trabalho supor grelha de eventos baseados em **Begins With** (prefixo) e termina **com fósforos** (sufixo). O filtro utiliza um operador `AND`, pelo que os eventos com um sujeito que correspondam tanto ao prefixo como ao sufixo são entregues ao assinante.

O tema da IoT Events utiliza o formato:

```json
devices/{deviceId}
```

A Grelha de Eventos também permite filtrar os atributos de cada evento, incluindo o conteúdo dos dados. Isto permite-lhe escolher quais os eventos que são entregues com base em conteúdos da mensagem de telemetria. Por favor, consulte [a filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para ver exemplos. Para filtrar o corpo da mensagem de telemetria, deve definir o conteúdoType para **aplicação/json** e codificação de conteúdo para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens . Ambas as propriedades são insensíveis.

Para eventos de não telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem da Rede de Eventos pode ser utilizada na criação da subscrição. Para eventos de telemetria, além da filtragem em Event Grid, os utilizadores também podem filtrar em gémeos do dispositivo, propriedades de mensagens e corpo através da consulta de encaminhamento de mensagens. 

Ao subscrever eventos de telemetria via Event Grid, o IoT Hub cria uma rota de mensagem padrão para enviar mensagens de dispositivo de tipo fonte de dados para a Rede de Eventos. Para obter mais informações sobre o encaminhamento de mensagens, consulte o [encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md). Esta rota será visível no portal sob o IoT Hub > Message Routing. Apenas uma rota para event grid é criada independentemente do número de subscrições de EG criadas para eventos de telemetria. Assim, se precisar de várias subscrições com filtros diferentes, pode utilizar o operador de OR nestas consultas na mesma rota. A criação e eliminação da rota é controlada através da subscrição de eventos de telemetria via Event Grid. Não é possível criar ou eliminar uma rota para a Grelha de Eventos utilizando o Encaminhamento de Mensagens IoT Hub.

Para filtrar mensagens antes de enviar dados de telemetria, pode atualizar a sua consulta de [encaminhamento](iot-hub-devguide-routing-query-syntax.md). Note que a consulta de encaminhamento só pode ser aplicada ao corpo da mensagem se o corpo for JSON. Deve também definir o conteúdoType para **aplicação/json** e codificação de conteúdos para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para eventos ligados ao dispositivo e dispositivo desligados

Para receber eventos estatais de ligação ao dispositivo, um dispositivo deve fazer uma operação 'D2C Send Telemettry' ou uma operação 'C2D Receive Message' com o Iot Hub. No entanto, note que se um dispositivo estiver a utilizar o protocolo AMQP para se ligar ao Iot Hub, recomenda-se que faça uma operação 'C2D Receive Message', caso contrário as notificações do estado de ligação podem ser adiadas por poucos minutos. Se o seu dispositivo estiver a utilizar o protocolo MQTT, o IoT Hub manterá o link C2D aberto. Para amqp, pode abrir o link C2D ligando para a [API Receive Async,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)para IoT Hub C# SDK, ou cliente de dispositivo para [AMQP](iot-hub-amqp-support.md#device-client).

A ligação D2C está aberta se estiver a enviar telemetria. 

Se a ligação do dispositivo estiver a piscar, o que significa que o dispositivo se conecta e se desliga frequentemente, não enviaremos todos os estados de ligação, mas publicaremos o estado de ligação atual tomado numa imagem periódica, até que o cintilar continue. Receber o mesmo evento de estado de ligação com diferentes números de sequência ou diferentes eventos de estado de conexão significam que houve uma alteração no estado de ligação do dispositivo.

## <a name="tips-for-consuming-events"></a>Dicas para consumir eventos

As aplicações que lidam com eventos do IoT Hub devem seguir estas práticas sugeridas:

* Várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, por isso não assuma que os eventos são de uma determinada fonte. Verifique sempre o tópico da mensagem para garantir que vem do centro ioT que espera.

* Não assuma que todos os eventos que recebe são os tipos que espera. Verifique sempre o eventoType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou depois de um atraso. Utilize o campo de etag para perceber se as suas informações sobre objetos estão atualizadas para eventos criados ou dispositivos apagados.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o tutorial de eventos IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como encomendar eventos de dispositivos ligados e desligados](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a Grelha de Eventos](../event-grid/overview.md)

* [Compare as diferenças entre o encaminhamento de eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)

* [Saiba como usar eventos de telemetria IoT para implementar análiseespacial ioT usando o Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
