---
title: Esquema de grelha de eventos do Azure para o IoT Hub | Documentos da Microsoft
description: Página de referência para o formato de esquema de eventos e propriedades do IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393193"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Esquema de eventos do Azure Event Grid para o IoT Hub

Este artigo fornece as propriedades e o esquema para eventos do IoT Hub do Azure. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md). 

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de evento do IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

O IoT Hub do Azure emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registado para um hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é eliminado de um hub IoT. | 
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é ligado à Internet de um hub IoT. | 
| Microsoft.Devices.DeviceTelemetry | Publicado quando é enviada uma mensagem de telemetria para um hub IoT. |

Todos os eventos de dispositivo, exceto os eventos de telemetria do dispositivo estão geralmente disponíveis em todas as regiões suportadas pelo Event Grid. Eventos de telemetria do dispositivo está em pré-visualização pública e está disponível em todas as regiões, exceto E.U.A. leste, E.U.A. oeste, Europa Ocidental, [do Azure Government](/azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome.md), e [Azure Alemanha](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Evento de exemplo

O esquema para eventos DeviceConnected e DeviceDisconnected têm a mesma estrutura. Este evento de exemplo mostra o esquema de um evento que ocorre quando um dispositivo está ligado a um hub IoT:

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

O evento de DeviceTelemetry é gerado quando um evento de telemetria é enviado para um IoT Hub. Um esquema de exemplo para este evento é mostrado abaixo.

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

O esquema para eventos DeviceCreated e DeviceDeleted têm a mesma estrutura. Este evento de exemplo mostra o esquema de um evento que ocorre quando um dispositivo é registado para um hub IoT:

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
| ID | string | Identificador exclusivo para o evento. |
| tópico | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| Assunto | string | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| Dados | objeto | Dados de eventos do IoT Hub.  |
| dataVersion | string | A versão de esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão de esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

Para todos os eventos do IoT Hub, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| hubName | string | Nome do IoT Hub em que o dispositivo foi criado ou eliminado. |
| deviceId | string | O identificador exclusivo do dispositivo. Esta cadeia de maiúsculas e minúsculas pode ter até 128 carateres e suporta carateres de alfanuméricos ASCII de 7 bits, bem como os seguintes carateres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

O conteúdo do objeto de dados é diferente para cada editor de eventos. 

Para **dispositivo ligado** e **dispositivo desligado** eventos do IoT Hub, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| moduleId | string | O identificador exclusivo do módulo. Este campo é de saída apenas para dispositivos de módulo. Esta cadeia de maiúsculas e minúsculas pode ter até 128 carateres e suporta carateres de alfanuméricos ASCII de 7 bits, bem como os seguintes carateres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objeto | Informações de eventos de estado de ligação do dispositivo
| sequenceNumber | string | Um número que ajuda a indica a ordem de dispositivo ligado ou dispositivo desligado eventos. Evento mais recente terá um número de sequência é maior do que o evento anterior. Este número pode ser alterada por mais de 1, mas está estritamente aumentando. Ver [como utilizar o número de sequência](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para **telemetria do dispositivo** evento do IoT Hub, o objeto de dados contém a mensagem de dispositivo-para-cloud no [formato de mensagem do hub IoT](../iot-hub/iot-hub-devguide-messages-construct.md) e tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Corpo | string | O conteúdo da mensagem do dispositivo. |
| properties | string | Propriedades da aplicação são definidas pelo utilizador as cadeias de caracteres que podem ser adicionadas à mensagem. Estes campos são opcionais. |
| Propriedades do sistema | string | [Propriedades do sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ajudar a identificar conteúdo e a origem das mensagens. Mensagem de telemetria do dispositivo tem de estar no formato JSON válido com contentType definido como JSON e contentEncoding definida como UTF-8 nas propriedades do sistema de mensagem. Se não for definida, o IoT Hub escrever as mensagens no formato codificado de 64 base.  |

Para **dispositivo criado** e **dispositivo eliminado** eventos do IoT Hub, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| duplo | objeto | Informações sobre o dispositivo duplo, que é a representação de cloud de metadados de dispositivo do aplicativo. | 
| deviceID | string | O identificador exclusivo do dispositivo duplo. | 
| etag | string | Um validador para garantir a consistência das atualizações para um dispositivo duplo. Cada etag é garantido que seja exclusivo por dispositivo duplo. |  
| deviceEtag| string | Um validador para garantir a consistência das atualizações para um registo do dispositivo. Cada deviceEtag é a garantia de ser exclusivo por registo de dispositivo. |
| status | string | Se o dispositivo duplo está ativado ou desativado. | 
| statusUpdateTime | string | Atualizar o carimbo de hora ISO8601 do último Estado de twin do dispositivo. |
| connectionState | string | Se o dispositivo está ligado ou desligado. | 
| lastActivityTime | string | O carimbo de hora ISO8601 da última atividade. | 
| cloudToDeviceMessageCount | inteiro | Contagem da cloud para mensagens de dispositivo enviadas para este dispositivo. | 
| authenticationType | string | Tipo de autenticação utilizado para este dispositivo: ambos `SAS`, `SelfSigned`, ou `CertificateAuthority`. |
| x509Thumbprint | string | O thumbprint é um valor exclusivo para o x509 certificado, frequentemente utilizado para localizar um determinado certificado num arquivo de certificados. O thumbprint é gerado dinamicamente usando o algoritmo SHA1 e não existem fisicamente no certificado. | 
| primaryThumbprint | string | Thumbprint primário para o x509 certificado. |
| secondaryThumbprint | string | Thumbprint secundário para o x509 certificado. | 
| version | inteiro | Um número inteiro que é incrementado em um cada vez que o dispositivo duplo é atualizado. |
| pretendido | objeto | Uma parte das propriedades que podem ser gravados apenas pelo back-end da aplicação e ler pelo dispositivo. | 
| comunicado | objeto | Uma parte das propriedades que pode ser escrito apenas pelo dispositivo e ler o back-end da aplicação. |
| lastUpdated | string | Atualizar o carimbo de hora ISO8601 da última propriedade de twin do dispositivo. | 

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para saber mais sobre como o IoT Hub e o Event Grid funcionam em conjunto, consulte [reagir a eventos do IoT Hub com o Event Grid para realizar ações](../iot-hub/iot-hub-event-grid.md).