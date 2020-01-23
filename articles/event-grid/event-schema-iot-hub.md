---
title: Esquema da grade de eventos do Azure para o Hub IoT | Microsoft Docs
description: Este artigo fornece as propriedades e o esquema para eventos do Hub IoT do Azure. Ele lista os tipos de evento disponíveis, um evento de exemplo e propriedades de evento.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513036"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Esquema de evento da grade de eventos do Azure para Hub IoT

Este artigo fornece as propriedades e o esquema para eventos do Hub IoT do Azure. Para obter uma introdução aos esquemas de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md). 

Para obter uma lista de tutoriais e scripts de exemplo, consulte [origem do evento do Hub IOT](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tipos de evento disponíveis

O Hub IoT do Azure emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado em um hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um hub IoT. | 
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está conectado a um hub IoT. |
| Microsoft. Devices. DeviceDisconnected | Publicado quando um dispositivo é desconectado de um hub IoT. | 
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria é enviada a um hub IoT. |

Todos os eventos de dispositivo, exceto eventos de telemetria de dispositivo, estão geralmente disponíveis em todas as regiões com suporte na grade de eventos. O evento de telemetria do dispositivo está em visualização pública e está disponível em todas as regiões, exceto leste dos EUA, oeste dos EUA, Europa Ocidental, [Azure governamental](../azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome)e [Azure Alemanha](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Evento de exemplo

O esquema para eventos DeviceConnected e DeviceDisconnected tem a mesma estrutura. Este evento de exemplo mostra o esquema de um evento gerado quando um dispositivo está conectado a um hub IoT:

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

O evento DeviceTelemetry é gerado quando um evento de telemetria é enviado para um hub IoT. Um esquema de exemplo para esse evento é mostrado abaixo.

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

O esquema para eventos DeviceCreated e DeviceDeleted tem a mesma estrutura. Este evento de exemplo mostra o esquema de um evento gerado quando um dispositivo é registrado em um hub IoT:

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

Todos os eventos contêm os mesmos dados de nível superior: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | Identificador exclusivo do evento. |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| data | objeto | Dados de evento do Hub IoT.  |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para todos os eventos do Hub IoT, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| hubName | string | Nome do Hub IoT em que o dispositivo foi criado ou excluído. |
| deviceId | string | O identificador único do dispositivo. Essa cadeia de caracteres que diferencia maiúsculas de minúsculas pode ter até 128 caracteres e dá suporte a caracteres alfanuméricos ASCII de 7 bits, além dos seguintes caracteres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

O conteúdo do objeto de dados é diferente para cada editor de evento. 

Para eventos **conectados ao dispositivo** e ao **dispositivo desconectado** do Hub IOT, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| moduleId | string | O identificador exclusivo do módulo. Este campo só é apresentado para dispositivos de módulo. Essa cadeia de caracteres que diferencia maiúsculas de minúsculas pode ter até 128 caracteres e dá suporte a caracteres alfanuméricos ASCII de 7 bits, além dos seguintes caracteres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objeto | Informações de evento de estado de conexão do dispositivo
| sequenceNumber | string | Um número que ajuda a indicar A ordem do dispositivo conectado ou eventos desconectados do dispositivo. O evento mais recente terá um número de sequência maior que o evento anterior. Esse número pode ser alterado em mais de 1, mas está estritamente aumentando. Consulte [como usar o número de sequência](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para o evento Hub IOT de **telemetria do dispositivo** , o objeto de dados contém a mensagem do dispositivo para a nuvem no [formato de mensagem do Hub IOT](../iot-hub/iot-hub-devguide-messages-construct.md) e tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| body | string | O conteúdo da mensagem do dispositivo. |
| propriedades | string | As propriedades do aplicativo são cadeias de caracteres definidas pelo usuário que podem ser adicionadas à mensagem. Esses campos são opcionais. |
| Propriedades do sistema | string | [As propriedades do sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ajudam a identificar o conteúdo e a origem das mensagens. A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o contentType definido como JSON e contentEncoding definido como UTF-8 nas propriedades do sistema de mensagens. Se isso não estiver definido, o Hub IoT gravará as mensagens no formato codificado 64 base.  |

Para o **dispositivo criado** e o **dispositivo excluiu** eventos do Hub IOT, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| twin | objeto | Informações sobre o dispositivo "or", que é a representação em nuvem dos metadados do dispositivo de aplicativo. | 
| deviceID | string | O identificador exclusivo do dispositivo. | 
| etag | string | Um validador para garantir a consistência de atualizações em um dispositivo. Cada ETag é garantido como sendo exclusivo por dispositivo. |  
| deviceEtag| string | Um validador para garantir a consistência de atualizações em um registro de dispositivo. Cada deviceEtag é garantido para ser exclusivo por registro de dispositivo. |
| status | string | Se o dispositivo to está habilitado ou desabilitado. | 
| statusUpdateTime | string | O carimbo de data/hora ISO8601 da última atualização de status do dispositivo. |
| connectionState | string | Se o dispositivo está conectado ou desconectado. | 
| lastActivityTime | string | O carimbo de data/hora ISO8601 da última atividade. | 
| cloudToDeviceMessageCount | número inteiro | Contagem das mensagens de nuvem para o dispositivo enviadas para este dispositivo. | 
| authenticationType | string | Tipo de autenticação usado para este dispositivo: `SAS`, `SelfSigned`ou `CertificateAuthority`. |
| x509Thumbprint | string | A impressão digital é um valor exclusivo para o certificado X509, normalmente usado para localizar um certificado específico em um repositório de certificados. A impressão digital é gerada dinamicamente usando o algoritmo SHA1 e não existe fisicamente no certificado. | 
| primaryThumbprint | string | Impressão digital primária para o certificado X509. |
| secondaryThumbprint | string | Impressão digital secundária para o certificado X509. | 
| versão | número inteiro | Um inteiro que é incrementado por um cada vez que o dispositivo é atualizado. |
| desired | objeto | Uma parte das propriedades que podem ser gravadas somente pelo back-end do aplicativo e lidas pelo dispositivo. | 
| reported | objeto | Uma parte das propriedades que podem ser gravadas somente pelo dispositivo e lidas pelo back-end do aplicativo. |
| lastUpdated | string | O carimbo de data/hora ISO8601 da última atualização de Propriedade do dispositivo. | 

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para saber mais sobre como o Hub IoT e a grade de eventos funcionam juntos, consulte [reagir aos eventos do Hub IOT usando a grade de eventos para disparar ações](../iot-hub/iot-hub-event-grid.md).