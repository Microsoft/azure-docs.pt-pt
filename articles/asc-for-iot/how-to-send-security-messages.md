---
title: Enviar suas mensagens de segurança ao centro de segurança do Azure para pré-visualização do IoT | Documentos da Microsoft
description: Saiba como enviar suas mensagens de segurança com o Centro de segurança do Azure para IoT.
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
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 73335773695059b3c2afd121a0dd39ada8d28bb0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618081"
---
# <a name="send-security-messages-sdk"></a>Enviar mensagens de segurança SDK

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de procedimentos explica o Centro de segurança do Azure (ASC) para capacidades do serviço de IoT ao escolher recolher e enviar mensagens de segurança de dispositivo sem utilizar um ASC para o agente de IoT e explica como fazer isso.  

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Utilizar a mensagem de segurança do envio API paraC#
> * Utilizar a mensagem de segurança do envio API para C

## <a name="asc-for-iot-capabilities"></a>ASC para recursos de IoT

ASC para IoT possa processar e analise qualquer tipo de dados de segurança de mensagens, desde que esteja em conformidade com os dados enviados para o [ASC para esquema de IoT](https://aka.ms/iot-security-schemas) e a mensagem é definida como uma mensagem de segurança.

## <a name="security-message"></a>Mensagem de segurança

ASC para IoT define uma mensagem de segurança usando os seguintes critérios:
- Se a mensagem foi enviada com o Azure IoT C /C# SDK
- Se a mensagem está em conformidade com o [esquema de mensagens de segurança](https://aka.ms/iot-security-schemas)
- Se a mensagem foi definida como uma mensagem de segurança antes de enviar

Cada mensagem de segurança inclui os metadados do remetente como `AgentId`, `AgentVersion`, `MessageSchemaVersion` e uma lista de eventos de segurança.
O esquema define as propriedades de válido e obrigatórias da mensagem de segurança, incluindo os tipos de eventos.

[!NOTE]
> As mensagens enviadas a que não são compatíveis com o esquema são ignorados. Certifique-se verificar o esquema antes de iniciar o envio de dados que as mensagens ignoradas atualmente não são armazenadas. 
> As mensagens enviadas a que não foram definidas como uma mensagem de segurança com o Azure IoT C /C# SDK não será encaminhado para o ASC para o pipeline de IoT

## <a name="valid-message-example"></a>Exemplo de mensagem válido

O exemplo abaixo mostra um objeto de mensagem de segurança válido. O exemplo contém os metadados de mensagem e outro `ProcessCreate` eventos de segurança.

Uma vez definida como uma mensagem de segurança e enviados, esta mensagem será processada pelo ASC para IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Enviar mensagens de segurança 

Enviar mensagens de segurança sem utilizar o ASC para agente de IoT, utilizando o [IoT do Azure C# SDK do dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) ou [SDK de dispositivo do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Para enviar os dados do dispositivo a partir dos seus dispositivos para processamento pelo ASC para IoT, utilize um dos seguintes APIs para marcar as mensagens de encaminhamento correto para o ASC para o pipeline de processamento de IoT. As mensagens enviadas desta forma serão processadas e apresentadas como informações de segurança dentro do ASC para IoT dentro de ambas as IoT Hub ou no Centro de segurança do Azure. 

Todos os dados que são enviados, mesmo se marcado com o cabeçalho correto, também devem estar em conformidade com o [ASC para esquema de mensagens de IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>API de mensagem de segurança de envio

O **enviar mensagens de segurança** API está atualmente disponível em C e C#.  

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

#### <a name="c-api"></a>API DE C

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

## <a name="next-steps"></a>Passos Seguintes
- Leia o ASC para o serviço de IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder ao [dados de segurança não processados](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas](concept-security-alerts.md)
