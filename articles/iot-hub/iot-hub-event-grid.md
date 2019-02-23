---
title: O Hub IoT do Azure e o Event Grid | Documentos da Microsoft
description: Utilize o Azure Event Grid para acionar processos com base nas ações que ocorrem no IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673858"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir a eventos do IoT Hub com o Event Grid para realizar ações

O IoT Hub do Azure integra-se com o Azure Event Grid para que possa enviar notificações de eventos para outros serviços e acionar processos downstream. Configure seus aplicativos de negócios para escutar eventos do IoT Hub para que possa reagir a eventos críticos de forma fiável, dimensionável e segura. Por exemplo, crie uma aplicação que atualiza uma base de dados, cria um tíquete de trabalho e fornece uma notificação por e-mail sempre que é registado um novo dispositivo de IoT ao seu hub IoT. 

[O Azure Event Grid](../event-grid/overview.md) é um serviço de encaminhamento de eventos totalmente gerido que utiliza um publicar-subscrever o modelo. Grelha de eventos possui suporte interno para serviços do Azure como [as funções do Azure](../azure-functions/functions-overview.md) e [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)e pode enviar alertas de eventos para serviços não pertencente ao Azure através de webhooks. Para obter uma lista completa dos manipuladores de eventos que suporta o Event Grid, veja [uma introdução ao Azure Event Grid](../event-grid/overview.md). 

![Arquitetura do Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração do Event Grid está disponível para os hubs IoT localizados em regiões onde o Event Grid é suportada. Para obter a lista mais recente de regiões, consulte [uma introdução ao Azure Event Grid](../event-grid/overview.md). 

## <a name="event-types"></a>Tipos de eventos

IoT Hub publica os seguintes tipos de evento: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registado para um hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é eliminado de um hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é ligado à Internet de um hub IoT. |

Utilize o portal do Azure ou a CLI do Azure para configurar os eventos de publicar a partir de cada hub IoT. Por exemplo, experimente o tutorial [enviar notificações de e-mail sobre eventos do IoT Hub do Azure com o Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema de eventos

Eventos do IoT Hub contenham todas as informações que necessárias para responder a alterações no seu ciclo de vida do dispositivo. Pode identificar um evento de IoT Hub, verificando a propriedade eventType iniciado por **Microsoft.Devices**. Para obter mais informações sobre como utilizar as propriedades de eventos do Event Grid, consulte a [esquema de eventos do Event Grid](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Esquema de dispositivo ligada

O exemplo seguinte mostra o esquema de um evento de ligada do dispositivo: 

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

### <a name="device-created-schema"></a>Esquema de dispositivo criado

O exemplo seguinte mostra o esquema de um dispositivo que criou o evento: 

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

Para obter uma descrição detalhada de cada propriedade, veja [esquema de eventos do Azure Event Grid para o IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

Subscrições de eventos do IoT Hub podem filtrar eventos com base no nome de dispositivo e o tipo de evento. Qual se baseia assunto filtros em projetos do Event Grid **começa com** (prefixo) e **termina com** corresponde a (sufixo). O filtro utiliza um `AND` operador, para que os eventos com um assunto que corresponderem o prefixo e o sufixo são entregues ao subscritor. 

O assunto de eventos de IoT utiliza o formato:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para dispositivo ligado e o dispositivo desligado eventos

Para receber o dispositivo ligado e eventos de dispositivo desligado, tem de abrir a ligação de D2C ou C2D ligação para o seu dispositivo. Se o dispositivo estiver a utilizar o protocolo MQTT, o IoT Hub manterá C2D link abrir. Para AMQP, é possível abrir a ligação de C2D ao chamar o [receber API de Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

A ligação de D2C é aberta, se estiverem a enviar telemetria. Se a ligação do dispositivo é cintilação, que significa que o dispositivo liga e desliga com frequência, nós não enviará a cada Estado de ligação único, mas irá publicar o estado de ligação que é snapshotted a cada minuto. Em caso de indisponibilidade do IoT Hub, vamos publicar o estado de ligação do dispositivo assim que a falha está acima. Se o dispositivo se desliga durante esse período de inatividade, o evento desconectado do dispositivo será publicado em 10 minutos.

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicações que processam os eventos do IoT Hub devem seguir estas práticas sugeridas:

* Várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, pelo que não parta do princípio que os eventos são de uma origem específica. Verifique sempre o tópico de mensagem para se certificar de que trata do hub IoT que esperava. 

* Não parta do princípio que todos os eventos recebidos são os tipos que esperava. Verifique sempre o eventType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou após um atraso. Utilize o campo de etag para compreender se as suas informações sobre os objetos estão atualizadas.

## <a name="next-steps"></a>Passos Seguintes

* [Experimente o tutorial de eventos do IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Saiba como encomendar eventos de dispositivos ligados e desligados](iot-hub-how-to-order-connection-state-events.md)
* [Saiba mais sobre o Event Grid](../event-grid/overview.md)
* [Comparar as diferenças entre mensagens e encaminhamento eventos do IoT Hub](iot-hub-event-grid-routing-comparison.md)
