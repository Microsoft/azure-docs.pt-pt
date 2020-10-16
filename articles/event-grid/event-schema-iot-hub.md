---
title: Azure IoT Hub como fonte de grade de eventos
description: Este artigo fornece as propriedades e esquema para eventos Azure IoT Hub. Ele lista os tipos de eventos disponíveis, um evento exemplo, e propriedades de eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 02ecf8d4df55aa6b4319e40892778f85f94e29a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113654"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure IoT Hub como fonte de grade de eventos
Este artigo fornece as propriedades e esquema para eventos Azure IoT Hub. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). 

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Azure IoT Hub emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo está registado num hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é eliminado de um hub IoT. | 
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desligado de um hub IoT. | 
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria é enviada para um hub IoT. |

Todos os eventos de dispositivos, exceto eventos de telemetria do dispositivo, estão geralmente disponíveis em todas as regiões suportadas pela Grade de Eventos. O evento de telemetria de dispositivos está em pré-visualização pública e está disponível em todas as regiões, exceto nos EUA, Oeste dos EUA, Europa Ocidental, [Governo Azure](../azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome), e [Azure Germany.](https://azure.microsoft.com/global-infrastructure/germany/)

### <a name="example-event"></a>Exemplo evento

O esquema para eventos DeviceConnected e DeviceDis conectados tem a mesma estrutura. Este evento de amostra mostra o esquema de um evento criado quando um dispositivo está ligado a um hub IoT:

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
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

O evento DeviceTelemetry é levantado quando um evento de telemetria é enviado para um Hub IoT. Um esquema de amostra para este evento é mostrado abaixo.

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

O esquema para eventos DeviceCreated e DeviceDeleted tem a mesma estrutura. Este evento de amostra mostra o esquema de um evento criado quando um dispositivo está registado num hub IoT:

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
      "deviceEtag": "null",
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

### <a name="event-properties"></a>Propriedades do evento

Todos os eventos contêm os mesmos dados de alto nível: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | Identificador único para o evento. |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| dados | objeto | Dados do evento IoT Hub.  |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para todos os eventos do IoT Hub, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| nome hub | string | Nome do Hub IoT onde o dispositivo foi criado ou eliminado. |
| deviceId | string | O identificador único do dispositivo. Esta cadeia sensível a casos pode ter até 128 caracteres de comprimento, e suporta caracteres alfanuméricos ASCII de 7 bits, além dos seguintes caracteres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |

O conteúdo do objeto de dados é diferente para cada editor de eventos. 

Para **eventos IoT** Hub ligados e **dispositivos ligados** ao dispositivo, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| moduleId | string | O identificador único do módulo. Este campo é apenas para dispositivos de módulos. Esta cadeia sensível a casos pode ter até 128 caracteres de comprimento, e suporta caracteres alfanuméricos ASCII de 7 bits, além dos seguintes caracteres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |
| dispositivoConnectionStateEventInfo | objeto | Informações sobre eventos do estado de ligação do dispositivo
| sequênciaNumera | string | Um número que ajuda a indicar a ordem do dispositivo ligado ou eventos desligados do dispositivo. O evento mais recente terá um número de sequência superior ao do evento anterior. Este número pode mudar em mais de 1, mas está estritamente a aumentar. Ver [como utilizar o número de sequência](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para o evento IoT Hub de **Telemetria do Dispositivo,** o objeto de dados contém a mensagem dispositivo-nuvem no [formato de mensagem do hub IoT](../iot-hub/iot-hub-devguide-messages-construct.md) e tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| body | string | O conteúdo da mensagem do dispositivo. |
| propriedades | string | As propriedades da aplicação são cadeias definidas pelo utilizador que podem ser adicionadas à mensagem. Estes campos são opcionais. |
| propriedades do sistema | string | [As propriedades do sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ajudam a identificar o conteúdo e a origem das mensagens. A mensagem de telemetria do dispositivo deve estar num formato JSON válido com o conteúdo Definido para JSON e conteúdoEncoding definido para UTF-8 nas propriedades do sistema de mensagem. Se isto não estiver definido, o IoT Hub escreverá as mensagens no formato codificado base 64.  |

Para **eventos IoT** Hub criados e **eliminados por dispositivos,** o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| gémeo | objeto | Informação sobre o dispositivo twin, que é a representação em nuvem dos metadados do dispositivo de aplicação. | 
| deviceID | string | O identificador único do dispositivo gémeo. | 
| etag | string | Um validador para garantir a consistência das atualizações a um dispositivo gémeo. Cada etag é garantido ser único por dispositivo twin. |  
| deviceEtag| string | Um validador para garantir a consistência das atualizações a um registo do dispositivo. Cada dispositivoEtag é garantido ser único por registo do dispositivo. |
| status | string | Se o twin do dispositivo está ativado ou desativado. | 
| statusUpdateTime | string | O semão de tempo ISO8601 da última atualização do estado do twin dispositivo. |
| ligação Estado | string | Se o aparelho está ligado ou desligado. | 
| última Hora de Atividade | string | O semão tempotando ISO8601 da última atividade. | 
| cloudToDeviceMessageCount | número inteiro | Contagem de nuvem para mensagens de dispositivo enviadas para este dispositivo. | 
| authenticationType | string | Tipo de autenticação utilizado para este dispositivo: ou `SAS` `SelfSigned` , ou `CertificateAuthority` . |
| x509Thumbprint | string | A impressão digital é um valor único para o certificado x509, comumente usado para encontrar um certificado particular numa loja de certificados. A impressão digital é gerada dinamicamente usando o algoritmo SHA1, e não existe fisicamente no certificado. | 
| impressão primáriaThumbprint | string | Impressão digital primária para o certificado x509. |
| secundárioThumbprint | string | Impressão digital secundária para o certificado x509. | 
| versão | número inteiro | Um inteiro que é aumentado por um cada vez que o twin do dispositivo é atualizado. |
| desejado | objeto | Uma parte das propriedades que só podem ser escritas pelo back-end da aplicação e lidas pelo dispositivo. | 
| relatado | objeto | Uma parte das propriedades que só podem ser escritas pelo dispositivo e lidas pelo back-end da aplicação. |
| lastUpdated | string | O semão de tempo ISO8601 da última atualização de propriedade dupla do dispositivo. | 

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Enviar notificações por e-mail sobre eventos do Hub IoT do Azure com o Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu IoT Hub. |
| [Reagir aos eventos do IoT Hub usando a Grade de Eventos para desencadear ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do Hub IoT com grade de eventos. |
| [Dispositivo de encomenda ligado e eventos desligados do dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como encomendar eventos estatais de ligação do dispositivo. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para saber como o IoT Hub e a Grade de Eventos funcionam em conjunto, consulte [os eventos do IoT Hub utilizando a Grade de Eventos para desencadear ações.](../iot-hub/iot-hub-event-grid.md)