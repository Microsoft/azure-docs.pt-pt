---
title: Azure IoT Hub e Grade de Eventos ! Microsoft Docs
description: Use a grelha de eventos Azure para desencadear processos baseados em ações que ocorrem no IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: af1e47c61977d0bc5d03f8cdb87393ed2014e736
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072310"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do IoT Hub usando a Grade de Eventos para desencadear ações

O Hub IoT do Azure integra-se no Azure Event Grid para que possa enviar notificações de eventos para outros serviços e acionar processos a jusante. Configure as aplicações empresariais para escutar eventos do Hub IoT para que possa reagir a eventos críticos de forma fiável, dimensionável e segura.Por exemplo, crie uma aplicação que atualiza uma base de dados, cria um pedido de trabalho e envia uma notificação por e-mail sempre que um novo dispositivo IoT é registado no hub IoT.

[A azure Event Grid](../event-grid/overview.md) é um serviço de encaminhamento de eventos totalmente gerido que utiliza um modelo de subscrição de publicação. A Event Grid tem suporte integrado para serviços Azure, como [Azure Functions](../azure-functions/functions-overview.md) e [Azure Logic Apps,](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para serviços não-Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que a Grade de Eventos suporta, consulte [uma introdução à Grelha de Eventos Azure](../event-grid/overview.md).

![Arquitetura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está disponível para centros IoT localizados nas regiões onde a Grade de Eventos é suportada. Para obter a mais recente lista de regiões, consulte uma introdução à Grelha de [Eventos Azure.](../event-grid/overview.md)

## <a name="event-types"></a>Tipos de evento

O IoT Hub publica os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo está registado num hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é eliminado de um hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desligado de um hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria do dispositivo é enviada para um hub IoT |

Utilize o portal Azure ou o Azure CLI para configurar quais os eventos a publicar a partir de cada hub IoT. Por exemplo, experimente o tutorial Enviar por email o [artigo Notificações sobre eventos Azure IoT Hub utilizando Aplicações Lógicas.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

## <a name="event-schema"></a>Esquema de eventos

Os eventos do IoT Hub contêm toda a informação necessária para responder às alterações no ciclo de vida do seu dispositivo. Pode identificar um evento do Hub IoT ao verificar se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como utilizar as propriedades do evento Event Grid, consulte o [esquema de eventos da Grade de Eventos.](../event-grid/event-schema.md)

### <a name="device-connected-schema"></a>Esquema ligado ao dispositivo

O exemplo a seguir mostra o esquema de um evento ligado ao dispositivo:

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

A mensagem de telemetria do dispositivo deve estar num formato JSON válido com o conteúdo Definido para **aplicação/json** e conteúdoEncoding definido para **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagem . Ambas as propriedades são insensíveis. Se a codificação de conteúdo não estiver definida, o IoT Hub escreverá as mensagens no formato codificado base 64.

Pode enriquecer eventos de telemetria do dispositivo antes de serem publicados na Grelha de Eventos selecionando o ponto final como Grade de Eventos. Para mais informações, consulte [a visão geral do "The Message Enriquements".](iot-hub-message-enrichments-overview.md)

O exemplo a seguir mostra o esquema de um evento de telemetria do dispositivo:

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

O exemplo a seguir mostra o esquema de um dispositivo criado:

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


> [!WARNING]
> *Os dados gémeos associados* a um evento de criação de dispositivos são uma configuração padrão e *não devem* ser confiados para propriedades reais `authenticationType` e outras do dispositivo num dispositivo recém-criado. Para `authenticationType` e outras propriedades do dispositivo num dispositivo recém-criado, utilize a API do Gestor de Registos fornecida em Azure IoT SDKs.

Para obter uma descrição detalhada de cada propriedade, consulte [o esquema de eventos da Azure Event Grid para ioT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As subscrições de eventos IoT Hub podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

A Grelha de Eventos permite [a filtragem](../event-grid/event-filtering.md) em tipos de eventos, sujeitos e conteúdos de dados. Ao criar a subscrição da Grade de Eventos, pode optar por subscrever eventos IoT selecionados. Filtros de objetos em trabalhos de Grade de Eventos **baseados** em **partidas** (prefixo) e termina com (sufixo). O filtro utiliza um `AND` operador, pelo que os eventos com um sujeito que corresponda tanto ao prefixo como ao sufixo são entregues ao assinante.

O tema ioT Events utiliza o formato:

```json
devices/{deviceId}
```

O Event Grid também permite a filtragem de atributos de cada evento, incluindo o conteúdo de dados. Isto permite-lhe escolher quais os eventos que são entregues com base em conteúdos da mensagem de telemetria. Consulte [a filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para ver exemplos. Para filtrar o corpo da mensagem de telemetria, deve definir o conteúdoType para **aplicação/json** e conteúdoEncoding para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagem . Ambas as propriedades são insensíveis.

Para eventos não telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem da Grelha de Evento pode ser utilizada ao criar a subscrição. Para eventos de telemetria, além da filtragem na Grade de Eventos, os utilizadores também podem filtrar os gémeos do dispositivo, as propriedades da mensagem e o corpo através da consulta de encaminhamento de mensagens. 

Quando subscreve eventos de telemetria através da Grade de Eventos, o IoT Hub cria uma rota de mensagem padrão para enviar mensagens de dispositivo tipo fonte de dados para a Grade de Eventos. Para obter mais informações sobre o encaminhamento de mensagens, consulte [o encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md). Esta rota será visível no portal no âmbito do IoT Hub > Message Roting. Apenas uma rota para a Grade de Eventos é criada independentemente do número de subscrições EG criadas para eventos de telemetria. Assim, se precisar de várias subscrições com filtros diferentes, pode utilizar o operador de OR nestas consultas na mesma rota. A criação e eliminação da rota é controlada através da subscrição de eventos de telemetria via Event Grid. Não é possível criar ou eliminar uma rota para a Grade de Eventos utilizando o Encaminhamento de Mensagens IoT Hub.

Para filtrar mensagens antes de os dados de telemetria serem enviados, pode atualizar a sua [consulta de encaminhamento](iot-hub-devguide-routing-query-syntax.md). Note que a consulta de encaminhamento só pode ser aplicada ao corpo da mensagem se o corpo for JSON. Também deve definir o conteúdoType para **aplicação/json** e conteúdoEncoding para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações dos eventos de dispositivos ligados e desligados

Para receber eventos estatais de ligação do dispositivo, um dispositivo deve fazer uma operação 'D2C Enviar Telemetria' ou uma operação 'C2D Receive Message' com iot Hub. No entanto, note que se um dispositivo estiver a utilizar o protocolo AMQP para se ligar ao Iot Hub, recomenda-se que façam uma operação de "Mensagem de Receção C2D", caso contrário, as suas notificações estatais de ligação poderão ser adiadas por alguns minutos. Se o seu dispositivo estiver a utilizar o protocolo MQTT, o IoT Hub manterá a ligação C2D aberta. Para AMQP, pode abrir o link C2D ligando para a API receive Async para IoT Hub C# SDK, ou [cliente do dispositivo para AMQP](iot-hub-amqp-support.md#device-client).

A ligação D2C está aberta se estiver a enviar telemetria. 

Se a ligação do dispositivo estiver a piscar, o que significa que o dispositivo liga e desliga frequentemente, não enviaremos todos os estados de ligação, mas publicaremos o estado de ligação atual tomado numa imagem periódica, até que o cintilar continue. Receber o mesmo evento de estado de ligação com diferentes números de sequência ou diferentes eventos estatais de ligação significa que houve uma alteração no estado de ligação do dispositivo.

## <a name="tips-for-consuming-events"></a>Sugestões para consumir eventos

As aplicações que processam eventos do Hub IoT devem seguir estas práticas sugeridas:

* Várias subscrições podem ser configuradas para direcionar eventos para o mesmo processador de eventos, por isso, não parta do princípio de que os eventos são de determinada origem. Verifique sempre o tópico da mensagem para garantir que é proveniente do hub IoT esperado.

* Não assuma que todos os eventos que recebe são dos tipos esperados. Verifique sempre o eventType antes de processar a mensagem.

* As mensagens podem chegar desordenadas ou após um atraso. Utilize o campo etag para perceber se as suas informações sobre objetos estão atualizadas para eventos criados ou apagados do dispositivo.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o tutorial de eventos IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como encomendar eventos de dispositivos ligados e desligados](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)

* [Compare as diferenças entre eventos e mensagens IoT Hub de encaminhamento](iot-hub-event-grid-routing-comparison.md)

* [Saiba como usar eventos de telemetria IoT para implementar análises espaciais IoT usando Azure Maps](../azure-maps/tutorial-iot-hub-maps.md)
