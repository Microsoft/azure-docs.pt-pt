---
title: Hub Azure IoT como fonte da Grelha de Eventos
description: Este artigo fornece as propriedades e esquemas para eventos Do Hub Azure IoT. Ele lista os tipos de eventos disponíveis, um evento de exemplo, e propriedades de eventos.
services: iot-hub
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f9bf807884ab5592fa320532f3ca10a223081263
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393333"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Hub Azure IoT como fonte da Grelha de Eventos
Este artigo fornece as propriedades e esquemas para eventos Do Hub Azure IoT. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md). 

## <a name="event-grid-event-schema"></a>Esquema de evento sinuoso do evento Da Grelha de Eventos

### <a name="available-event-types"></a>Tipos de eventos disponíveis

O Azure IoT Hub emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo está registado num hub IoT. |
| Microsoft.Devices.DeviceEliminado | Publicado quando um dispositivo é apagado de um hub IoT. | 
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo está ligado a um hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desligado de um hub IoT. | 
| Microsoft.Devices.DeviceTelemetria | Publicada quando uma mensagem de telemetria é enviada para um centro ioT. |

Todos os eventos do dispositivo, exceto os eventos de telemetria do dispositivo, estão geralmente disponíveis em todas as regiões suportadas pela Event Grid. O evento de telemetria de dispositivos está em pré-visualização pública e está disponível em todas as regiões, exceto Leste dos EUA, Oeste dos EUA, Europa Ocidental, [Governo Azure,](../azure-government/documentation-government-welcome.md) [Azure China 21Vianet](/azure/china/china-welcome)e [Azure Alemanha.](https://azure.microsoft.com/global-infrastructure/germany/)

### <a name="example-event"></a>Evento de exemplo

O esquema para eventos Ligados ao Dispositivo e dispositivodesligados tem a mesma estrutura. Este evento de amostra mostra o esquema de um evento criado quando um dispositivo está ligado a um hub IoT:

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

O evento DeviceTelemettry é levantado quando um evento de telemetria é enviado para um Hub IoT. Um esquema de amostra para este evento é mostrado abaixo.

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

O esquema para eventos DispositivoSCriados e DispositivoSEliminados têm a mesma estrutura. Este evento de amostra mostra o esquema de um evento criado quando um dispositivo está registado num hub IoT:

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
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| data | objeto | Dados do evento IoT Hub.  |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para todos os eventos do IoT Hub, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| hubName | string | Nome do Hub IoT onde o dispositivo foi criado ou eliminado. |
| deviceId | string | O identificador único do dispositivo. Esta cadeia sensível a casos pode ter até 128 caracteres de comprimento, e suporta caracteres `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanuméricos ascii de 7 bits mais os seguintes caracteres especiais: . |

O conteúdo do objeto de dados é diferente para cada editor de eventos. 

Para eventos ioT hub **ligados** ao dispositivo e desligados do **dispositivo,** o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| móduloId | string | O identificador único do módulo. Este campo é de saída apenas para dispositivos de módulos. Esta cadeia sensível a casos pode ter até 128 caracteres de comprimento, e suporta caracteres `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanuméricos ascii de 7 bits mais os seguintes caracteres especiais: . |
| dispositivoConnectionStateEventInfo | objeto | Informações do evento do estado de ligação do dispositivo
| sequênciaNúmero | string | Um número que ajuda a indicar a ordem dos eventos ligados ao dispositivo ou desligados do dispositivo. O último evento terá um número de sequência superior ao evento anterior. Este número pode mudar em mais de 1, mas está a aumentar estritamente. Veja [como utilizar o número da sequência](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para o evento **Device Telemetry** IoT Hub, o objeto de dados contém a mensagem dispositivo-nuvem no formato de [mensagem do hub IoT](../iot-hub/iot-hub-devguide-messages-construct.md) e tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| body | string | O conteúdo da mensagem a partir do dispositivo. |
| propriedades | string | As propriedades da aplicação são cordas definidas pelo utilizador que podem ser adicionadas à mensagem. Estes campos são opcionais. |
| propriedades do sistema | string | [As propriedades](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) do sistema ajudam a identificar conteúdos e fonte seleções das mensagens. A mensagem de telemetria do dispositivo deve estar num formato JSON válido com o conjunto de conteúdoType para JSON e contentEncoding definido para UTF-8 nas propriedades do sistema de mensagens. Se isto não estiver definido, o IoT Hub escreverá as mensagens no formato codificado base 64.  |

Para **eventos ioT** hub criados e **dispositivos eliminados,** o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| gémeo | objeto | Informações sobre o dispositivo twin, que é a representação na nuvem dos metadados do dispositivo de aplicação. | 
| dispositivoID | string | O identificador único do dispositivo gémeo. | 
| etag | string | Um validador para garantir a consistência das atualizações a um dispositivo twin. Cada veado é garantido ser único por dispositivo twin. |  
| dispositivoEtag| string | Um validador para garantir a consistência das atualizações a um registo do dispositivo. Cada dispositivo Etag é garantido ser único por registo de dispositivos. |
| status | string | Se o dispositivo twin está ativado ou desativado. | 
| statusUpdateTime | string | O carimbo de tempo ISO8601 da última atualização de estado gémeo do dispositivo. |
| conexãoEstado | string | Se o dispositivo está ligado ou desligado. | 
| últimaAtividadeTempo | string | O carimbo de tempo ISO8601 da última atividade. | 
| cloudToDeviceMessageCount | número inteiro | Contagem de mensagens de nuvem para dispositivo enviadas para este dispositivo. | 
| authenticationType | string | Tipo de autenticação utilizado `SAS`para `SelfSigned`este `CertificateAuthority`dispositivo: ou, ou . |
| x509Impressão de polegar | string | A impressão digital é um valor único para o certificado x509, comumente usado para encontrar um certificado particular em uma loja de certificados. A impressão digital é gerada dinamicamente usando o algoritmo SHA1, e não existe fisicamente no certificado. | 
| impressão primária Thumbprint | string | Impressão digital primária para o certificado x509. |
| impressão secundária Thumbprint | string | Impressão de polegar secundária para o certificado x509. | 
| versão | número inteiro | Um inteiro que é incrementado por um cada vez que o dispositivo twin é atualizado. |
| desejado | objeto | Uma parte das propriedades que só podem ser escritas pelo back-end da aplicação, e lidas pelo dispositivo. | 
| relatado | objeto | Uma parte das propriedades que só pode ser escrita pelo dispositivo, e lida pela aplicação no back-end. |
| lastUpdated | string | O carimbo de tempo ISO8601 da última atualização de propriedade gémea do dispositivo. | 

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Enviar notificações por e-mail sobre eventos do Hub IoT do Azure com o Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu IoT Hub. |
| [Reagir aos eventos do IoT Hub usando a Grelha de Eventos para desencadear ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do IoT Hub com a Grelha de Eventos. |
| [Encomendar dispositivo conectado e dispositivo eventos desligados](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como encomendar eventos estatais de ligação do dispositivo. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para saber como o IoT Hub e a Event Grid trabalham em conjunto, consulte [os eventos Do React to IoT Hub, utilizando a Rede de Eventos para desencadear ações.](../iot-hub/iot-hub-event-grid.md)