---
title: Enviar suas mensagens de segurança para a central de segurança do Azure para IoT | Microsoft Docs
description: Saiba como enviar suas mensagens de segurança usando a central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2019
ms.author: mlottner
ms.openlocfilehash: 4d91eecc6168ae195fecdf788f091fd70b785f05
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937136"
---
# <a name="send-security-messages-sdk"></a>Enviar SDK de mensagens de segurança

Este guia de instruções explica a central de segurança do Azure para recursos de serviço de IoT quando você opta por coletar e enviar suas mensagens de segurança de dispositivo sem usar uma central de segurança do Azure para agente de IoT e explica como fazer isso.  

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Enviar mensagens de segurança usando o SDK do Azure IoT C
> * Enviar mensagens de segurança usando o SDK C# do Azure IOT
> * Enviar mensagens de segurança usando o SDK do Python do Azure IoT
> * Enviar mensagens de segurança usando o SDK do node. js do Azure IoT
> * Enviar mensagens de segurança usando o SDK do Java do Azure IoT


## <a name="azure-security-center-for-iot-capabilities"></a>Central de segurança do Azure para recursos de IoT

A central de segurança do Azure para IoT pode processar e analisar qualquer tipo de dados de mensagem de segurança, desde que os dados enviados estejam em conformidade com a [central de segurança do Azure para o esquema de IOT](https://aka.ms/iot-security-schemas) e a mensagem seja definida como uma mensagem de segurança.

## <a name="security-message"></a>Mensagem de segurança

A central de segurança do Azure para IoT define uma mensagem de segurança usando os seguintes critérios:
- Se a mensagem foi enviada com o SDK do Azure IoT
- Se a mensagem estiver em conformidade com o [esquema de mensagem de segurança](https://aka.ms/iot-security-schemas)
- Se a mensagem foi definida como uma mensagem de segurança antes de enviar

Cada mensagem de segurança inclui os metadados do remetente `AgentId`, como `MessageSchemaVersion` , `AgentVersion`e uma lista de eventos de segurança.
O esquema define as propriedades válidas e obrigatórias da mensagem de segurança, incluindo os tipos de eventos.

>[!Note]
> As mensagens enviadas que não estão em conformidade com o esquema são ignoradas. Certifique-se de verificar o esquema antes de iniciar o envio de dados, pois as mensagens ignoradas não estão armazenadas no momento. 

>[!Note]
> Mensagens enviadas que não foram definidas como uma mensagem de segurança usando o SDK do Azure IoT não serão roteadas para a central de segurança do Azure para o pipeline de IoT.

## <a name="valid-message-example"></a>Exemplo de mensagem válida

O exemplo a seguir mostra um objeto de mensagem de segurança válido. O exemplo contém os metadados da mensagem e `ProcessCreate` um evento de segurança.

Uma vez definido como uma mensagem de segurança e enviado, essa mensagem será processada pela central de segurança do Azure para IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Enviar mensagens de segurança 

Enviar mensagens de segurança *sem* usar a central de segurança do Azure para agente de IOT, usando o SDK do dispositivo do Azure [IOT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), o [SDK do dispositivo IOT C# do](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)Azure, o [SDK do node. js do Azure IOT](https://github.com/Azure/azure-iot-sdk-node), o SDK [do Python do Azure IOT](https://github.com/Azure/azure-iot-sdk-python)ou o [SDK do Java do Azure IOT ](https://github.com/Azure/azure-iot-sdk-java).

Para enviar os dados do dispositivo de seus dispositivos para processamento pela central de segurança do Azure para IoT, use uma das APIs a seguir para marcar as mensagens para o roteamento correto para a central de segurança do Azure para o pipeline de processamento de IoT. 

Todos os dados que são enviados, mesmo se marcados com o cabeçalho correto, também devem estar em conformidade com a [central de segurança do Azure para o esquema de mensagem de IOT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Enviar API de mensagem de segurança 

A API **enviar mensagens de segurança** está disponível no momento em C#C e, Python, Node. js e Java.  

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>API C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>API do node. js

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>API Python

```python
async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_d2c_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>API de Java

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>Passos seguintes
- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
- Saiba como acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md)
