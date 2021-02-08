---
title: Enviar mensagens de segurança do dispositivo IoT para defender
description: Saiba como enviar as suas mensagens de segurança utilizando o Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/8/2021
ms.author: shhazam
ms.custom: devx-track-js
ms.openlocfilehash: d59121f2dbae208ba045a9c2e6d66245296537a0
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820692"
---
# <a name="send-security-messages-sdk"></a>Enviar mensagens de segurança SDK

Este guia explica o Defender para as capacidades de serviço IoT quando escolhe recolher e enviar mensagens de segurança do seu dispositivo sem utilizar um Defender para agente IoT, e explica como fazê-lo.

Neste guia, ficará a saber como:

> [!div class="checklist"]
> * Envie mensagens de segurança usando o Azure IoT C SDK
> * Envie mensagens de segurança usando o Azure IoT C# SDK
> * Envie mensagens de segurança usando o Azure IoT Python SDK
> * Envie mensagens de segurança usando o Azure IoT Node.js SDK
> * Envie mensagens de segurança usando o Azure IoT Java SDK

## <a name="defender-for-iot-capabilities"></a>Defender para capacidades IoT

O Defender para IoT pode processar e analisar qualquer tipo de dados de mensagens de segurança, desde que os dados enviados estejam em conformidade com o [Sistema de IoT](https://aka.ms/iot-security-schemas) do Defender e a mensagem seja definida como uma mensagem de segurança.

## <a name="security-message"></a>Mensagem de segurança

O Defender para IoT define uma mensagem de segurança utilizando os seguintes critérios:

- Se a mensagem foi enviada com Azure IoT SDK
- Se a mensagem estiver em conformidade com o esquema da [mensagem de segurança](https://aka.ms/iot-security-schemas)
- Se a mensagem foi definida como uma mensagem de segurança antes de enviar

Cada mensagem de segurança inclui os metadados do remetente, `AgentId` `AgentVersion` como, `MessageSchemaVersion` e uma lista de eventos de segurança.
O esquema define as propriedades válidas e necessárias da mensagem de segurança, incluindo os tipos de eventos.

> [!NOTE]
> As mensagens enviadas que não cumpram o esquema são ignoradas. Certifique-se de verificar o esquema antes de iniciar o envio de dados, uma vez que as mensagens ignoradas não estão armazenadas atualmente.

> [!NOTE]
> As mensagens enviadas que não foram definidas como uma mensagem de segurança utilizando o Azure IoT SDK não serão encaminhadas para o pipeline Defender para IoT.

## <a name="valid-message-example"></a>Exemplo de mensagem válido

O exemplo abaixo mostra um objeto de mensagem de segurança válido. O exemplo contém os metadados de mensagens e um `ProcessCreate` evento de segurança.

Uma vez definida como uma mensagem de segurança e enviada, esta mensagem será processada pelo Defender para IoT.

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

Envie mensagens de segurança *sem* utilizar o Defender para agente IoT, utilizando o [dispositivo Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [Azure IoT C# dispositivo SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK,](https://github.com/Azure/azure-iot-sdk-python)ou [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Para enviar os dados do dispositivo dos seus dispositivos para processamento pelo Defender para IoT, utilize uma das seguintes APIs para marcar mensagens para corrigir o encaminhamento para o Defender para o pipeline de processamento de IoT.

Todos os dados enviados, mesmo que marcados com o cabeçalho correto, devem também estar em conformidade com o [sistema de mensagens IoT](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Enviar mensagem de segurança API

As **mensagens de segurança Enviar** API estão atualmente disponíveis em C e C#, Python, Node.js e Java.

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

#### <a name="nodejs-api"></a>API do Node.js

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

Para utilizar a API Python, é necessário instalar o [dispositivo azure-iot-iot da](https://pypi.org/project/azure-iot-device/)embalagem .

Ao utilizar a API Python, pode enviar a mensagem de segurança através do módulo ou através do dispositivo utilizando o dispositivo único ou a cadeia de ligação do módulo. Quando utilizar o seguinte exemplo de script Python, com um dispositivo, utilize **ioTHubDeviceClient**, e com um módulo, utilize **ioTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
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

- Leia o Defender para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder aos [dados de segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas](concept-security-alerts.md)
