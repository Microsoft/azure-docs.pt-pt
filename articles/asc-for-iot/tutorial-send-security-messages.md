---
title: Enviar suas mensagens de segurança para o ASC para pré-visualização do IoT | Documentos da Microsoft
description: Saiba como enviar suas mensagens de segurança com o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3daef0bfb2b9108e2b3c28ca0f6975a6f5768e92
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541590"
---
# <a name="tutorial-send-security-messages-sdk"></a>Tutorial: Enviar mensagens de segurança SDK

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial explica o ASC para capacidades de análise de dados de IoT ao escolher a recolher e enviar mensagens de segurança de dispositivo sem utilizar um ASC para o agente de IoT e explica como fazer isso.  

Neste tutorial, ficará a saber como: 
> [!div class="checklist"]
> * Utilizar a mensagem de segurança do envio API paraC#
> * Utilizar a mensagem de segurança do envio API para C

## <a name="asc-for-iot"></a>ASC para IoT 

ASC para IoT possa processar e analise qualquer tipo de dados de segurança de mensagens, desde que esteja em conformidade com os dados enviados para o [ASC para esquema de IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

## <a name="send-security-messages"></a>Enviar mensagens de segurança 

Enviar mensagens de segurança sem utilizar o ASC para agente de IoT, utilizando o [do Azure IoT device SDK](https://github.com/Azure/azure-iot-sdk-csharp).

Para enviar os dados do dispositivo a partir dos seus dispositivos para processamento pelo ASC para IoT, utilize um dos seguintes APIs para marcar as mensagens de encaminhamento correto para o ASC para o pipeline de processamento de IoT. As mensagens enviadas desta forma serão processadas e apresentadas como informações de segurança dentro do ASC para IoT dentro de ambas as IoT Hub ou no Centro de segurança do Azure. 

Todos os dados que são enviados, mesmo se marcado com o cabeçalho correto, também devem estar em conformidade com o [ASC para esquema de mensagens de IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

> [!NOTE]
> As mensagens enviadas a que não são compatíveis com o esquema são ignorados. Certifique-se verificar o esquema antes de iniciar o envio de dados que as mensagens ignoradas atualmente não são armazenadas. 

### <a name="send-security-message-api"></a>API de mensagem de segurança de envio

O **enviar mensagens de segurança** API está atualmente disponível em C e C#.  

#### <a name="c-api"></a>API C#

```cs
private static async Task SendSecurityMessageAsync()
{
    string messageContent = "Security Data";
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