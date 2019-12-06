---
title: Hub IoT do Azure e grade de eventos | Microsoft Docs
description: Use a grade de eventos do Azure para disparar processos com base em ações que ocorrem no Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851705"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do Hub IoT usando a grade de eventos para disparar ações

O Hub IoT do Azure integra-se à grade de eventos do Azure para que você possa enviar notificações de eventos para outros serviços e disparar processos downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira confiável, escalonável e segura. Por exemplo, crie um aplicativo que atualize um banco de dados, crie um tíquete de trabalho e entregue uma notificação por email toda vez que um novo dispositivo IoT for registrado no Hub IoT.

A [grade de eventos do Azure](../event-grid/overview.md) é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. A grade de eventos tem suporte interno para serviços do Azure, como [Azure Functions](../azure-functions/functions-overview.md) e [aplicativos lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md), e pode entregar alertas de eventos a serviços não Azure usando WebHooks. Para obter uma lista completa dos manipuladores de eventos aos quais a grade de eventos dá suporte, consulte [uma introdução à grade de eventos do Azure](../event-grid/overview.md).

![Arquitetura da grade de eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da grade de eventos está disponível para os hubs IoT localizados nas regiões em que há suporte para a grade de eventos. Para obter a lista mais recente de regiões, consulte [uma introdução à grade de eventos do Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipos de evento

O Hub IoT publica os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado em um hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está conectado a um hub IoT. |
| Microsoft. Devices. DeviceDisconnected | Publicado quando um dispositivo é desconectado de um hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria do dispositivo é enviada a um hub IoT |

Use o portal do Azure ou CLI do Azure para configurar quais eventos publicar de cada Hub IoT. Para obter um exemplo, experimente o tutorial [enviar notificações por email sobre eventos do Hub IOT do Azure usando aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema de eventos

Os eventos do Hub IoT contêm todas as informações necessárias para responder às alterações no ciclo de vida do seu dispositivo. Você pode identificar um evento do Hub IoT verificando se a propriedade eventType começa com **Microsoft. Devices**. Para obter mais informações sobre como usar propriedades de evento da grade de eventos, consulte o [esquema de evento da grade de eventos](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Esquema conectado ao dispositivo

O exemplo a seguir mostra o esquema de um evento conectado ao dispositivo:

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

A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o contentType definido como **Application/JSON** e contentEncoding definido como **UTF-8** nas [Propriedades do sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas. Se a codificação de conteúdo não estiver definida, o Hub IoT gravará as mensagens no formato codificado 64 base.

Você pode enriquecer os eventos de telemetria do dispositivo antes que eles sejam publicados na grade de eventos selecionando o ponto de extremidade como grade de eventos. Para obter mais informações, consulte [visão geral de aprimoramentos de mensagem](iot-hub-message-enrichments-overview.md).

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

### <a name="device-created-schema"></a>Esquema criado pelo dispositivo

O exemplo a seguir mostra o esquema de um evento criado pelo dispositivo:

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

Para obter uma descrição detalhada de cada propriedade, consulte [esquema de evento da grade de eventos do Azure para o Hub IOT](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As assinaturas de evento do Hub IoT podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

A grade de eventos permite [Filtrar](../event-grid/event-filtering.md) tipos de eventos, assuntos e conteúdo de dados. Ao criar a assinatura da grade de eventos, você pode optar por assinar os eventos de IoT selecionados. Os filtros de assunto na grade de eventos funcionam com base em **começa com** (prefixo) e **termina com** as correspondências (sufixo). O filtro usa um operador de `AND`, de modo que os eventos com um assunto que correspondam tanto ao prefixo quanto ao sufixo sejam entregues ao Assinante.

O assunto dos eventos de IoT usa o formato:

```json
devices/{deviceId}
```

A grade de eventos também permite a filtragem de atributos de cada evento, incluindo o conteúdo de dados. Isso permite que você escolha quais eventos são entregues com base no conteúdo da mensagem de telemetria. Consulte [filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para exibir exemplos. Para filtrar o corpo da mensagem de telemetria, você deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas [Propriedades do sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas.

Para eventos que não são de telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem da grade de eventos pode ser usada ao criar a assinatura. Para eventos de telemetria, além da filtragem na grade de eventos, os usuários também podem filtrar no dispositivo gêmeos, propriedades de mensagem e corpo por meio da consulta de roteamento de mensagens. 

Quando você assina eventos de telemetria por meio da grade de eventos, o Hub IoT cria uma rota de mensagem padrão para enviar mensagens de dispositivo do tipo fonte de dados para a grade de eventos. Para obter mais informações sobre o roteamento de mensagens, consulte [Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md). Essa rota ficará visível no portal no Hub IoT > roteamento de mensagens. Somente uma rota para a grade de eventos é criada, independentemente do número de assinaturas de exemplo criadas para eventos de telemetria. Portanto, se você precisar de várias assinaturas com filtros diferentes, poderá usar o operador OR nessas consultas na mesma rota. A criação e a exclusão da rota são controladas por meio da assinatura de eventos de telemetria por meio da grade de eventos. Você não pode criar ou excluir uma rota para a grade de eventos usando o roteamento de mensagens do Hub IoT.

Para filtrar mensagens antes que os dados de telemetria sejam enviados, você pode atualizar sua [consulta de roteamento](iot-hub-devguide-routing-query-syntax.md). Observe que a consulta de roteamento pode ser aplicada ao corpo da mensagem somente se o corpo for JSON. Você também deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para o dispositivo conectado e eventos desconectados do dispositivo

Para receber eventos de estado de conexão do dispositivo, um dispositivo deve fazer uma operação "C2D Receive telemetria" ou "mensagem de recebimento" com o Hub IOT. No entanto, observe que, se um dispositivo estiver usando o protocolo AMQP para se conectar ao Hub IOT, é recomendável que eles façam uma operação "C2D receber mensagem", caso contrário, suas notificações de estado de conexão poderão ser atrasadas em alguns minutos. Se o dispositivo estiver usando o protocolo MQTT, o Hub IoT manterá o link C2D aberto. Para AMQP, você pode abrir o link C2D chamando a [API assíncrona de recebimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), para o C# SDK do Hub IOT ou [para o cliente do dispositivo para AMQP](iot-hub-amqp-support.md#device-client).

O link do D2C será aberto se você estiver enviando telemetria. 

Se a conexão do dispositivo estiver piscando, o que significa que o dispositivo se conecta e desconecta com frequência, não enviaremos todos os Estados de conexão única, mas publicaremos o estado de conexão atual feito em um instantâneo periódico, até que a cintilação continue. O recebimento do mesmo evento de estado de conexão com números de sequência diferentes ou eventos de estado de conexão diferentes significam que houve uma alteração no estado de conexão do dispositivo.

## <a name="tips-for-consuming-events"></a>Dicas para consumir eventos

Os aplicativos que lidam com eventos do Hub IoT devem seguir estas práticas sugeridas:

* Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, portanto, não presuma que os eventos sejam de uma fonte específica. Sempre verifique o tópico da mensagem para garantir que ele venha do Hub IoT esperado.

* Não presuma que todos os eventos recebidos são os tipos esperados. Sempre verifique o eventType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo ETag para entender se suas informações sobre objetos estão atualizadas para dispositivos criados pelo dispositivo ou eventos excluídos do dispositivo.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como encomendar eventos de dispositivos ligados e desligados](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a grade de eventos](../event-grid/overview.md)

* [Comparar as diferenças entre rotear eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)

* [Saiba como usar eventos de telemetria de IoT para implementar análises espaciais de IoT usando o Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
